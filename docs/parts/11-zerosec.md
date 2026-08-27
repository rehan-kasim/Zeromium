# Part 11 — zerosec: Security

`zerosec` is the trust boundary for the entire browser. It owns: origin
model, sandboxing, CSP, certificate policy, and cross-process IPC policy.

## Responsibilities
1. **Origin & URL** — the *only* crate that parses URLs and computes
   origins. Everyone else calls `zerosec::origin_of(url)`.
2. **Sandbox** — per-renderer sandbox setup:
   - Linux: seccomp-bpf + namespaces + no socket syscalls.
   - Windows: Job objects + restricted tokens.
   - macOS: sandbox entitlements / seatbelt.
3. **IPC policy** — every cross-process message is validated against a
   capability table; renderers cannot request raw sockets.
4. **CSP** — Content-Security-Policy parse + enforcement hooks used by
   `zeronet` (block requests) and `hyperion-js` (block inline).
5. **Certificate policy** — strict chain validation via `rustls`, HSTS
   upgrade, public-key pinning table (optional).
6. **Site isolation** — each origin in its own renderer/agent cluster;
   cross-origin reads blocked at the `zerodom` boundary.

## Threat model
- Untrusted web content runs in a renderer with no direct network, no raw
  filesystem, no other origin's DOM.
- Network service is the only socket owner; it enforces CSP + cert policy.

## API
```rust
pub fn origin_of(url: &str) -> Origin;
pub fn check_ipc(msg: &Envelope) -> Result<(), SecError>;
pub fn sandbox_current_process() -> Result<(), SecError>;
```

## Fuzzing
- `cargo fuzz run url_origin` and `cargo fuzz run csp_parse`.
- Security review of every `unsafe` block in the repo (CI grep gate).

## Milestones
- M1: origin model + renderer sandbox on Linux.
- M2: IPC capability policy + CSP.
- M3: cross-platform sandbox (Win/macOS).
- M4: site isolation per origin.
