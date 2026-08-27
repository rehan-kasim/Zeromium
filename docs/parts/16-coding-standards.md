# Part 16 — Coding Standards

How Zeromium code is written. Enforced by CI (`clippy -D warnings`).

## Style
- `rustfmt` default; 100-col soft limit, `rustfmt.toml` committed.
- No `unwrap()`/`expect()` on externally-influenced state. Use `?` or
  explicit `match`. `debug_assert!` only for internal invariants.
- Prefer `thiserror`/`Result` returns over panics.

## Memory
- Allocate from `zeromem` arenas, not the global allocator, on hot paths.
- Mark `MEM_BUDGET` per crate; respect it.

## Safety
- `unsafe` only with a `// SAFETY:` comment and a reviewer sign-off.
- CI greps for `unsafe` and fails PRs that add it without a tracked reason.

## Modules & crates
- Crates are independent; cross-crate deps flow downward per Part 02 graph.
- No crate depends on `zerorender`/`hyperion-js` except the browser wiring.
- Public APIs documented (`///`); no `pub` without docs at crate boundary.

## Testing
- Pure core is `#[no_std]`-friendly behind a feature for headless tests.
- Every parser has a fuzz target; every crate has unit tests.

## Commit & review
- Small, reviewed PRs. Each PR must not lower WPT/Test262 pass rates.
- `alloc-track` CI checks memory budget on touched crates.

## Naming (systems are uniquely named)
- Engine crates: `zeronet`, `zerohtml`, `zerodom`, `zerocss`, `zerolayout`,
  `zerorender`, `hyperion-js`, `zerosec`, `zeroui`, `zeromem`, `zerotask`,
  `zerostore`, `zerocodec`, `zerofont`. No shared prefix required.
