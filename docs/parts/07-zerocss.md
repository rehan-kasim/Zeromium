# Part 07 — zerocss: CSS Engine

`zerocss` parses CSS, builds the cascade, matches selectors against
`zerodom`, and produces computed values. Target: full CSS Snapshot / modern
spec (selectors, values, cascade, custom properties, at-rules).

## Pipeline
1. **Tokenizer** — tokens: ident, hash, number, dimension, string,
   url, function, delims, whitespace, comment.
2. **Parser** — declarations, rules, at-rules (`@media`, `@supports`,
   `@keyframes`, `@font-face`, `@layer` progressively).
3. **Selector matching** — compile selectors to a matcher that walks
   `zerodom`; support compound/complex selectors, pseudo-classes/elements,
   specificity, `:is()/:not()/:has()` (`:has` is expensive — gate behind a
   flag initially).
4. **Cascade** — origin/precedence, `!important`, specificity, layer order,
   inline styles.
5. **Computed values** — resolve `em/rem/%`, `var()`, initial/inherit/
   unset, shorthands → longhands.
6. **Layout hints** — emit a styled tree consumed by `zerolayout`.

## Memory
- Stylesheet rules stored in `zeromem` arena; matched results cached per
  element and invalidated on DOM mutation.

## API
```rust
pub fn parse_stylesheet(css: &str, base: &Origin) -> Stylesheet;
pub fn cascade(doc: &Document, sheets: &[Stylesheet]) -> ComputedStyleTree;
```

## Conformance
- WPT `css/` suites. Track pass rate per module.

## Milestones
- M1: selectors L1-L3 + cascade + common properties.
- M2: custom properties, `calc()`, `:has()`.
- M3: full modern property set (grid, containers, etc. feeding layout).
