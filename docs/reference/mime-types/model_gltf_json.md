# MIME type: model/gltf+json

`zeronet`/`zerorender`/`zerocodec` handling of **model/gltf+json**.

- **Decided by**: `Content-Type` from `zeronet`, validated by `zerosec`.
- **Action**: route to layout/paint/codec or download.
- **Sniffing**: minimal, spec-guided (avoid MIME confusion XSS).

## Checklist
- [ ] Parse header
- [ ] Dispatch to handler
- [ ] Sniff rules
- [ ] Security check

