# Part 05 — zerohtml: HTML Parser

`zerohtml` implements the HTML5 parsing algorithm precisely: a tokenizer
(including all 80+ tokenization states) feeding tree construction (with
insertion modes, foster parenting, and the quirks/limited-quirks/standards
DOCTYPE switch).

## Why spec-exact
A lenient "best effort" parser diverges from every other browser and breaks
real sites. We implement the living standard's state machine. This is the
single most conformance-critical crate.

## Components
1. **Tokenizer** (`tokenizer.rs`):
   - Input stream with confidence-based encoding detection (UTF-8 default;
     latin1/shift_jis as first non-UTF8 additions).
   - States: data, tag open, tag name, before attr name, attr name,
     attr value (single/double/none), markup declaration, comment, doctype,
     CDATA, RAWTEXT/RCDATA (script/style), etc.
   - Emits events to the tree builder incrementally (streaming).
2. **Tree builder** (`tree.rs`):
   - Insertion modes: initial, before html, before head, in head, in body,
     text, in table, in caption, in select, after body, in frameset, etc.
   - Stack of open elements, list of active formatting elements.
   - foster parenting, implied tags, adoption agency algorithm.
3. **Namespaces**: HTML by default; SVG/MathML via the namespace logic in the
   spec (full support target).
4. **Error recovery**: every spec "parse error" is recorded into an error
   list (for debugging) but never aborts.

## Memory
- Nodes are allocated in `zeromem` arenas owned by `zerodom`.
- No full-document buffer required; tokenizer streams from `zeronet`.

## Public API
```rust
pub fn parse_stream(input: impl Read, sink: &mut dyn DomSink);
// or, for tests:
pub fn parse_str(s: &str) -> zerodom::Document;
```

## Fuzzing
- `cargo fuzz run html_tokenizer` with arbitrary bytes — must produce a valid
  tree or a recorded-error tree, never panic.
- Conformance: run the WPT `html/syntax/` suite.

## Milestones
- M1: tokenizer passes WPT tokenization tests.
- M2: tree builder passes `html/syntax/parser/` tests.
- M3: SVG/MathML namespaces.
