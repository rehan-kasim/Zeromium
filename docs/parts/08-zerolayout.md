# Part 08 — zerolayout: Layout & Formatting

`zerolayout` turns the styled `zerodom` tree into a box tree and computes
geometry (position + size) ready for `zerorender`. This is where most
real-world rendering bugs live, so it is built incrementally and tested
against WPT `css/` layout tests.

## Box model
- Block/inline/atomic-inline boxes; margins, borders, padding.
- Intrinsic/preferred/minimum sizes for shrink-to-fit.
- Containing block, static position, overflow.

## Formatting contexts
- Block formatting context.
- Inline formatting + line breaking (Unicode line-break algorithm, from
  scratch in `zerofont`/a `zerotext` helper).
- Flexbox (target full spec).
- CSS Grid (target full spec).
- Positioned (absolute/fixed), floats (gated initially), tables (gated).

## Algorithm
1. Build box tree from DOM + computed style.
2. Compute widths top-down, heights bottom-up.
3. Inline layout: build lines, place runs, honor `white-space`, `word-break`.
4. Produce a **display list** (paint commands) handed to `zerorender`.

## Memory
- Boxes allocated in `zeromem` arena; reuse boxes across layout passes where
  possible (damage rectangles from `zerorender` trigger partial relayout).

## API
```rust
pub fn layout(doc: &Document, style: &ComputedStyleTree,
              viewport: Size) -> DisplayList;
```

## Milestones
- M1: block + inline + basic formatting (static sites render).
- M2: flexbox.
- M3: grid, positioning, overflow, clipping.
- M4: floats, tables, writing modes.
