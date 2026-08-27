# Part 03 — Build Environment

## Toolchain
- Rust stable, pinned in `rust-toolchain.toml` (e.g. `1.82.0`).
- `cargo` + `rustfmt` + `clippy` required in CI.
- `sccache` optional for faster rebuilds on low-end machines.

## Vulkan SDK
- Install the Vulkan SDK (LunarG) for your OS.
- `zerorender` uses `ash` (thin Vulkan bindings). A `VK_ICD_FILENAMES`
  software rasterizer (lavapipe / SwiftShader) is used in CI and on
  GPU-less devices.
- Feature `vulkan` (default). Feature `swrast` forces software path.

## Workspace

`Cargo.toml` (root):
```toml
[workspace]
resolver = "2"
members = [
  "crates/zeromium",
  "crates/zeronet",
  "crates/zerohtml",
  "crates/zerodom",
  "crates/zerocss",
  "crates/zerolayout",
  "crates/zerorender",
  "crates/hyperion-js",
  "crates/zerosec",
  "crates/zeroui",
  "crates/zeromem",
  "crates/zerotask",
  "crates/zerostore",
  "crates/zerocodec",
  "crates/zerofont",
]
```

## Features
- `alloc-track` — track per-crate memory for the < 50 MB budget.
- `fuzz` — expose fuzz entry points.
- `wpt` — pull Web Platform Tests as a submodule and run them.

## CI (production-grade)
- `cargo clippy --all-targets -- -D warnings`
- `cargo test`
- `cargo miri test` on the unsafe crates (zeromem, zerorender).
- Fuzzing via `cargo fuzz` for zeronet, zerohtml, zerocss, zerofont,
  zerocodec, hyperion-js.
- A "low-end CI" runner (QEMU + 256 MB RAM) verifies the memory budget.

## Justfile (developer shortcuts)
```
build:  cargo build --release
test:   cargo test --all
fuzz:   cargo fuzz run html_tokenizer
bench:  cargo bench
```
