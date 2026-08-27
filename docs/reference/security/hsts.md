# Security: hsts

`zerosec` design note: **hsts**.

- **Owner**: `zerosec` (single source of truth for this boundary).
- **Goal**: enforce least privilege and origin isolation.
- **Integration**: consumed by `zeronet`, `zerodom`, `hyperion-js`, `zeroui`.
- **Verification**: fuzz targets + security review of `unsafe` blocks.

## Checklist
- [ ] Spec/algorithm defined
- [ ] Enforced at boundary
- [ ] Test (WPT / fuzz)
- [ ] CI gate

