# Vulkan/Render: texture-image

`zerorender` note for **texture-image**.

- **Module**: `zerorender` (Vulkan via `ash`); software fallback `swrast`.
- **Memory**: bounded by `zeromem`; tracked for < 50 MB budget.
- **Purpose**: paint `DisplayList` from `zerolayout` to screen.

## Checklist
- [ ] Implement on GPU path
- [ ] Implement on swrast path
- [ ] Memory bound
- [ ] CI (lavapipe) green

