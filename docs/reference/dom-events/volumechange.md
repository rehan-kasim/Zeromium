# DOM event: volumechange

`zerodom` + `hyperion-js` note for event **volumechange**.

- **Dispatched by**: browser engine or `hyperion-js` dispatch.
- **Flow**: capturing → target → bubbling (per spec).
- **Bindings**: `addEventListener` exposed to JS.
- **Security**: listener runs in `hyperion-js` under origin isolation.
- **Conformance**: WPT `dom/events/`.

## Checklist
- [ ] Event object shape
- [ ] Dispatch order
- [ ] Bubbling/cancel
- [ ] JS binding

