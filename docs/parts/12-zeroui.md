# Part 12 — zeroui: Browser Chrome

`zeroui` is the browser's own UI (tab strip, URL bar, back/forward, find-in-
page) and it is **drawn by `zerorender`** — no OS toolkit, no egui/iced. This
keeps the dependency surface tiny and the look consistent everywhere.

## Components
1. **Window** — created via `winit`-free minimal platform layer
   (`zerowin` or direct Win32/Cocoa/X11 calls) to honor "from scratch";
   the window just provides a Vulkan surface + input events.
2. **Widget tree** — a small retained-mode UI tree (`Button`, `Text`,
   `TextField`, `TabBar`) laid out by a mini copy of `zerolayout` logic.
3. **URL bar** — issues navigations through the browser process → network
   service; shows security state from `zerosec`.
4. **Tabs** — each tab maps to a renderer process; `zeroui` manages the
   IPC handles and tab lifecycle.
5. **Start page** — a simple built-in local HTML page (no network), the
   default homepage.

## Memory
- UI is a tiny fraction of budget; still allocated via `zeromem`.

## API
```rust
pub fn run(browser: BrowserHandle) -> Result<(), UiError>;
```

## Milestones
- M1: single window + URL bar + one tab renders a page.
- M2: multiple tabs, back/forward, reload.
- M3: find-in-page, settings, start page polish.
