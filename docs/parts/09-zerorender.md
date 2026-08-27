# Part 09 — zerorender: Vulkan Renderer

`zerorender` paints the `DisplayList` from `zerolayout` using **Vulkan**,
with a software fallback (`swrast`) for GPU-less low-end devices.

## Backend
- `ash` Vulkan bindings; a thin command-buffer + descriptor + pipeline
  manager.
- `VK_ICD_FILENAMES=lavapipe` (or SwiftShader) for CI and no-GPU devices.
- Compositor: layers → damage rectangles → partial present to save power/RAM.

## Paint pipeline
1. Receive `DisplayList` (rects, text runs, images, borders, shadows).
2. Tessellate into vertex buffers (cached in `zeromem`).
3. Record command buffers per dirty region.
4. Present; keep a minimal backing store (< budget) for compositing.

## Text
- `zerofont` rasterizes glyphs to atlases; `zerorender` draws textured quads.
- Subpixel AA optional (off by default on low-DPI to save memory).

## Images
- `zerocodec` decodes PNG → RGBA; `zerorender` uploads as textures.

## Memory discipline (critical for < 50 MB)
- Texture atlases bounded; LRU eviction.
- Framebuffers sized to viewport, not screen, when possible.
- `alloc-track` verifies the whole renderer stays within its RAM slice.

## Milestones
- M1: solid fills, borders, text (software Vulkan).
- M2: images (PNG), clipping, transforms.
- M3: compositor + damage tracking + GPU path tuned.
- M4: animations/transitions timing.
