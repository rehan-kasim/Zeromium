# Part 00 — Introduction

Zeromium is a web browser implemented almost entirely from scratch in Rust.
The motivation is threefold:

1. **Mastery.** Understanding every layer of the web stack, from a TCP
   segment to a painted pixel, by building it.
2. **Security.** Minimal trusted computing base, auditable pure-Rust crypto,
   strong origin isolation, and aggressive input fuzzing.
3. **Low-end first.** Most browsers assume gigabytes of RAM and a GPU.
   Zeromium is designed to render real sites in **under 50 MB idle** on
   devices like a Raspberry Pi Zero or an old phone.

## Scope

**In scope**
- Fetching and rendering modern HTTPS sites.
- A from-scratch JavaScript engine (`hyperion-js`).
- Vulkan-based rendering with a software fallback.
- Full modern spec conformance as the long-term target.
- Privacy-first in-memory storage only.

**Out of scope (initially)**
- Browser extensions / plugins.
- Persistent on-disk profiles, history, or caches.
- Accessibility tree (deferred; may be added later).
- Non-PNG image formats at launch (WebP/JPEG follow).

## What "from scratch" means here

- We write our own: HTML parser, DOM, CSS engine, layout, rasterizer,
  font rasterizer, JS engine, async runtime, arena allocators,
  PNG decoder, UI toolkit.
- We *reuse* only where it is unsafe or wasteful to roll our own:
  - `rustls` for TLS (cryptography correctness is not a place to experiment).
  - The Vulkan loader / `ash` bindings to talk to the GPU.
  - OS primitives (threads, futex, file IO) via `std`/`libc`.

## Reading path for a new contributor

Start at `zeronet` (get bytes), then `zerohtml` → `zerodom` → `zerocss`
→ `zerolayout` → `zerorender`. Add `hyperion-js` once a static page
paints. `zerosec` and `zeromem` are cross-cutting and should be read
early even though they are not a "stage".
