# Part 06 — zerodom: Document Object Model

`zerodom` is the in-memory tree produced by `zerohtml` and manipulated by
`hyperion-js`. It must be spec-faithful (Node, Element, Document, Text,
Comment, DocumentFragment, Attr, etc.) and allocation-efficient.

## Structure
- Nodes live in a `zeromem` arena; handles are index-based (`NodeId`),
  not `Rc<RefCell>`. This keeps memory low and is FFI/JS-engine friendly.
- `NodeId` + generation counter gives safe weak references.
- Events: an event target trait, capturing/bubbling phases, `Event` objects.
- Mutation observers: a compact observer registry (no per-node allocation
  unless observed).

## API shape (subset)
```rust
pub struct Document { arena: Arena<Node> }
pub type NodeId = u32;
impl Document {
    pub fn create_element(&self, name: &str) -> NodeId;
    pub fn append_child(&self, parent: NodeId, child: NodeId);
    pub fn query_selector(&self, sel: &Selector) -> Option<NodeId>;
}
```

## Integration
- `zerohtml` calls into `zerodom` via a `DomSink` trait.
- `hyperion-js` binds `document`, element methods, and events through
  generated glue (see Part 10).

## Conformance
- WPT `dom/` and `domparsing/` suites.
- Keep the node arena under a few MB for typical pages (tracked by
  `alloc-track`).

## Milestones
- M1: tree + basic element/attr/text API.
- M2: events + bubbling.
- M3: mutation observers, ranges, full DOM Living Standard surface.
