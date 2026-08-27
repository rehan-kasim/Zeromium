# Part 15 — Roadmap

The path from "prints bytes" to "renders everything." Each phase ends with a
measured conformance/memory number.

## Phase 0 — Foundation
- Workspace, `zeromem`, `zerotask`, `zerosec` origin model.
- CI + fuzz harness scaffolding.

## Phase 1 — Static page (no JS)
- `zeronet` (HTTP/1.1+TLS) → `zerohtml` → `zerodom` → `zerocss` →
  `zerolayout` → `zerorender` → `zeroui` single tab.
- Milestone: render a static real site (e.g. example.com, Wikipedia article)
  to screen. Idle < 50 MB.

## Phase 2 — Rich layout & images
- Flexbox, grid, positioning, `zerocodec` PNG, `zerofont` text shaping.
- Milestone: complex blogs/MDN render correctly; WPT css pass > 60%.

## Phase 3 — JavaScript
- `hyperion-js` interpreter + DOM bindings. Events, fetch via network svc.
- Milestone: interactive sites (Hacker News, simple SPA) work. Test262 > 50%.

## Phase 4 — Hardening & isolation
- Renderer sandbox (Linux → Win/macOS), CSP, site isolation, HTTP/2.
- Milestone: security review passed; no known RCE paths.

## Phase 5 — Conformance push
- Full modern spec coverage: tables, floats, `:has`, container queries,
  animations. HTTP/3/QUIC. JIT tier-up.
- Milestone: "renders everything" — WPT > 95%, Test262 > 90%, idle < 50 MB.

## Phase 6 — Polish
- Find-in-page, settings, start page, devtools-lite, low-end tuning.

Each phase is itself a shippable, tested browser.
