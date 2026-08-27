# JS builtin: Node

`hyperion-js` implementation note for global **Node**.

- **Kind**: ECMAScript / Web IDL global.
- **Value model**: stored as NaN-boxed `Value` in the VM heap.
- **GC**: managed by `hyperion-js` generational collector.
- **Bindings**: DOM globals wired through `zerodom` glue.
- **Conformance**: Test262 + WPT `webidl/` and `html/webapp/`.

## Checklist
- [ ] Constructor/prototype
- [ ] Methods & getters
- [ ] Edge cases per spec
- [ ] Test262 coverage
- [ ] DOM integration

