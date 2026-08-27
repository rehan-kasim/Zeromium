# Part 04 — zeronet: Networking

`zeronet` is the network service. It owns DNS, sockets, TLS, HTTP/1.1+2+3,
caching, redirects, decompression, cookies (in-memory), and proxy support.
Renderers talk to it only via `zerosec`-verified IPC.

## Design
- Our own **minimal async** runtime (`zerotask`) drives all IO — no tokio.
- Single event loop per network thread; connections are state machines.

## Stages
1. **URL → origin** via `zerosec` (the only place URLs are parsed).
2. **DNS**: our own UDP resolver (with `getaddrinfo` fallback behind a flag),
   result cached in an LRU inside `zerostore` (memory only).
3. **Connect + TLS**: `rustls` client, strict certificate chain validation.
   HSTS upgrade applied by `zerosec`.
4. **HTTP**: 
   - HTTP/1.1 first (streaming request/response).
   - HTTP/2 via a from-scratch frame state machine.
   - HTTP/3/QUIC implemented after 1.1+2 stabilize (tracked in roadmap).
5. **Redirects**: followed automatically up to 5 hops; loop detection.
6. **Decompression**: gzip, then brotli, then zstd (from-scratch decoders in
   `zerocodec` where feasible; `flate2`/equivalent avoided to honor "from
   scratch" — implement RFC 1951/1952 directly).
7. **Cookies**: in-memory jar in `zerostore`, same-origin policy enforced by
   `zerosec`. No persistence.
8. **Cache**: in-memory LRU; `Cache-Control` respected; evicted under memory
   pressure by `zeromem`.

## Public API (sketch)
```rust
pub async fn fetch(req: Request) -> Result<Response, NetError>;
pub fn resolve(host: &str) -> impl Stream<Item = IpAddr>;
```

## Milestones
- M1: HTTP/1.1 GET over TLS to a real site, stream bytes out.
- M2: gzip + redirect + cookie jar.
- M3: HTTP/2.
- M4: HTTP/3/QUIC.

## Fuzzing
- `cargo fuzz run http1_response` — must never panic.
- `cargo fuzz run dns_packet`.
