# Zeromium — The From-Scratch Browser Manual

> A complete, production-grade web browser built from zero in Rust.
> Goal: render *everything* on the modern web, with security and a **< 50 MB idle**
> memory footprint on very low-end hardware as first-class constraints.

This repository is the **instruction manual + build guide** for Zeromium and its
engine crates. Every subsystem is implemented from scratch (minimal external
dependencies) unless explicitly noted.

---

## Identity

| Item            | Value                                                       |
|-----------------|-------------------------------------------------------------|
| Browser name    | **Zeromium**                                                |
| Language        | Rust (edition 2021+, `rust-toolchain.toml` pinned)          |
| Rendering       | **Vulkan** (software fallback planned)                      |
| JS engine       | **hyperion-js** (our own, from scratch)                     |
| TLS             | **rustls** (auditable, pure-Rust)                           |
| Concurrency     | Our own minimal async event loop                            |
| Storage         | In-memory only (privacy-first, no disk persistence)         |
| Target idle RAM | **< 50 MB**                                                 |
| Spec goal       | Full modern living standard (HTML5 / CSS / DOM / JS)        |
| License         | Unlicensed / private                                        |

## Engine crate names (unique, no shared prefix)

| Crate          | Responsibility                                              |
|----------------|-------------------------------------------------------------|
| `zeronet`      | Networking: DNS, HTTP/1.1+2+3, TLS, caching, proxies        |
| `zerohtml`     | HTML tokenizer + tree construction (spec algorithm)          |
| `zerodom`      | DOM tree, nodes, events, mutation observers                  |
| `zerocss`      | CSS parser, cascade, selector matching, computed values     |
| `zerolayout`   | Formatting, box model, flex/grid, positioning                |
| `zerorender`   | Vulkan rasterizer, paint, compositing, fonts (from scratch) |
| `hyperion-js`  | JavaScript engine: lexer, parser, bytecode VM, JIT           |
| `zerosec`      | Sandbox, origin model, CSP, certificate policy, fuzzing     |
| `zeroui`       | Browser chrome (tabs, URL bar) drawn in our own renderer    |
| `zeromem`      | Arena allocators, slab, bump, object pools (low-end core)   |
| `zerotask`     | Async runtime + lightweight fibers                          |
| `zerostore`    | In-memory storage, cookie jar, session state                |
| `zerocodec`    | PNG decode (and later WebP/JPEG) from scratch               |
| `zerofont`     | TTF/OTF rasterizer from scratch                             |

## How to read this manual

1. `parts/00-introduction.md` — vision, scope, non-goals.
2. `parts/01-design-principles.md` — the rules every crate obeys.
3. `parts/02-architecture.md` — process/model + data flow.
4. `parts/03-build-environment.md` — toolchain, Vulkan SDK, CI.
5. `parts/04-zeronet.md` … `parts/12-zeroui.md` — per-crate build manuals.
6. `parts/13-low-end.md` — hitting < 50 MB.
7. `parts/14-testing.md` — production-grade conformance & fuzzing.
8. `parts/15-roadmap.md` — milestone plan to "renders everything".
9. `parts/16-coding-standards.md` — how to write Zeromium code.
10. `reference/` — generated lookup docs (HTML elements, CSS properties,
   DOM APIs, JS builtins, HTTP statuses, security model).

## The one-line build loop

```
cargo build --release && ./target/release/zeromium https://example.com
```

Everything downstream is detail.
