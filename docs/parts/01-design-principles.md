# Part 01 — Design Principles

Every crate in Zeromium obeys these non-negotiable rules.

## P1. Low-end first, always
- No allocation on the hot path without an arena. Every parser, the layout
  engine, and the rasterizer allocate from `zeromem` pools, never the global
  allocator in steady state.
- Idle memory budget is **< 50 MB**. A crate that eats 20 MB at startup is
  a bug. Measure with `#[cfg(feature = "alloc-track")]`.
- Prefer bounded buffers and streaming APIs. Never buffer an entire response
  unless the spec forces it.

## P2. Security is a property, not a feature
- All untrusted input (network bytes, HTML, CSS, JS, fonts, images) is
  parsed behind a fuzz harness. A crash is a security bug.
- Cross-origin isolation is enforced by `zerosec` at the boundary, not
  sprinkled through callers.
- Integer math on untrusted lengths uses checked/saturating ops. No
  `unwrap()` on parsed sizes.

## P3. From scratch unless dangerous
- Roll our own for: parsing, DOM, layout, paint, fonts, JS, async, allocators.
- Use `rustls` for TLS and `ash` for Vulkan. Document any other external dep
  in `docs/parts/03-build-environment.md` with a justification.

## P4. Deterministic, testable units
- Each crate exposes a pure, `#[no_std]`-friendly core where possible, with
  the `std`/OS layer behind a feature flag. This enables WPT and fuzz targets
  to run without a display or network.

## P5. Streaming by default
- `zeronet` delivers bytes to `zerohtml` incrementally.
- `zerohtml` emits DOM nodes as they are constructed; `zerolayout` can begin
  computing intrinsic sizes before the document ends.

## P6. One definition of "origin"
- `zerosec` owns the origin/scheme/host/port tuple and every boundary check.
  No crate re-implements URL/origin logic.

## P7. Panic is not a crash strategy
- `unwrap()`/`expect()` are banned on externally-influenced state. Internal
  invariants may use `debug_assert!`.

## P8. Measurable progress
- Every milestone has a conformance number (WPT pass rate, MB used, FPS).
  "It feels faster" is not a metric.
