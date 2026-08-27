# Part 10 — hyperion-js: JavaScript Engine

`hyperion-js` is our from-scratch ECMAScript engine. It is the largest single
subsystem after layout. Built in layers so a usable subset runs early.

## Layers
1. **Lexer** — Unicode-aware tokenizer, all punctuators, identifiers,
   literals (incl. template, regex, bigint).
2. **Parser** — ESTree-style AST, full grammar (statements, expressions,
   classes, modules, async/await, optional chaining, etc.).
3. **Bytecode compiler** — AST → compact bytecode with a register VM.
4. **VM** — stack/register interpreter executing bytecode over `Value`s.
5. **JIT** — tier-up: record hot traces, emit machine code (via our own
   assembler or `cranelift` behind a flag; from-scratch backend later).
6. **Standard library** — `Object`, `Array`, `String`, `Number`, `Math`,
   `JSON`, `Promise`, `Map/Set`, `TypedArray`, `Intl` (progressively),
   plus DOM bindings via `zerodom` glue.

## Values & GC
- `Value` is a NaN-boxed 64-bit representation (compact, fast).
- GC: a generational collector tuned for low heap (start < 8 MB), incremental
  to avoid jank. `zeromem` backs the GC arena.

## DOM bindings
- Generated glue maps `document.*`, element accessors, events to `zerodom`.
- Security boundaries (cross-origin) enforced by `zerosec` before any binding
  call returns data.

## Conformance
- Test262 suite. Track pass rate as the headline JS metric.

## Milestones
- M1: interpreter runs arithmetic + DOM `getElementById` + events.
- M2: classes, closures, promises.
- M3: JIT tier-up.
- M4: Test262 > 90%.

## Fuzzing
- `cargo fuzz run js_parser` and `js_value` — no panics, no OOM blowups.
