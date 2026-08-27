# Part 14 — Testing (Production-Grade)

Zeromium's quality bar is "renders everything," which requires conformance
testing, not just unit tests.

## Layers
1. **Unit tests** — `cargo test` in every crate. Pure cores are `#[no_std]`
   where possible for headless testing.
2. **Conformance suites**
   - **WPT** (Web Platform Tests) as a submodule; run `html/`, `css/`,
     `dom/`, `fetch/` under `features.wpt`. Track pass % in CI badges.
   - **Test262** for `hyperion-js`.
3. **Fuzzing** — `cargo fuzz` targets for every parser and the JS engine:
   - `html_tokenizer`, `html_tree`, `css_parser`, `url_origin`, `csp_parse`,
     `dns_packet`, `http1_response`, `http2_frame`, `png_decode`,
     `font_parse`, `js_parser`, `js_value`.
   - A crash = security bug; triage within 24h.
4. **Miri** — run the `unsafe` crates (`zeromem`, `zerorender`, `hyperion-js`
   value code) under Miri to catch UB.
5. **Snapshot/layout tests** — render reference pages, diff against
   approved baselines (per-platform, since Vulkan output varies).
6. **Memory tests** — low-end CI asserts RSS budget.

## CI gates (all must pass)
- `cargo clippy -D warnings`
- `cargo test --all`
- `cargo miri test -p zeromem -p zerorender`
- WPT pass rate not lower than `main` (no regressions).
- `cargo fuzz run <target> -- -max_total_time=60` in CI smoke mode.

## Coverage
- `cargo tarpaulin` (or `llvm-cov`) enforces per-crate minimums for parsers.
