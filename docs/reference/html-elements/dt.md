# HTML element &lt;dt&gt;

Zeromium implementation note for `<dt>`.

- **Crate**: `zerohtml` tokenizes and `zerodom` builds the node.
- **Namespace**: HTML (SVG/MathML handled by namespace logic).
- **Parsing**: follows the HTML5 insertion-mode rules for `dt`.
- **Layout**: `zerolayout` assigns a box per the CSS display value.
- **Conformance**: tracked in WPT `html/semantics/` and `html/syntax/`.
- **Security**: untrusted content; parsed under `zerosec` origin model.

## Checklist
- [ ] Tokenizer state
- [ ] Tree-builder insertion mode
- [ ] Default styling (UA stylesheet in `zerocss`)
- [ ] Layout box kind
- [ ] DOM API surface in `zerodom` + `hyperion-js` bindings

