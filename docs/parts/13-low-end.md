# Part 13 — Low-End Optimization (< 50 MB idle)

Hitting < 50 MB idle on a Pi Zero-class device is a hard design constraint,
not an optimization phase. Techniques enforced across crates:

## Allocation strategy
- **`zeromem` arenas** for every long-lived structure (DOM nodes, boxes,
  styles, JS values). No per-node `Box`/`Rc`.
- Bump allocator for parse passes; slab for stable handles.
- Object pools for short-lived paint intermediates.

## Memory budgeting
- Each crate has a `MEM_BUDGET` constant. Under `alloc-track`, exceeding it
  logs and, in test/CI, fails.
- The "low-end CI" runner (QEMU, 256 MB RAM) boots Zeromium, loads a real
  page, and asserts RSS < 50 MB idle after settle.

## Streaming & bounded buffers
- `zeronet` streams; `zerohtml` streams; nothing buffers a whole response
  unless forced.
- Image/atlas caches are bounded LRU and shrink under pressure.

## Cheap startup
- Lazy-init expensive subsystems (JIT, font caches) only when first needed.
- No preload of the whole spec tables; load on demand.

## Power/thermal
- Damage-rect compositing avoids full repaints.
- Software Vulkan (lavapipe) is the fallback when no GPU; still under budget.

## Measurement
```
cargo build --release --features alloc-track
./target/release/zeromium --memreport https://news.ycombinator.com
# prints per-crate MB + total RSS
```
Target: network+render+ui idle combined < 50 MB.
