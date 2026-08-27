# Part 02 — Architecture

## Process model (security + low-end)

Zeromium uses a **multi-process, sandboxed** model, the only design that
satisfies both the security and low-end goals:

```
┌─────────────────────────────────────────────┐
│ zeromium  (browser process / chrome)         │
│   ├─ zeroui      (tabs, URL bar, drawn by    │
│   │               zerorender)                │
│   ├─ zerotask    (async runtime / scheduler) │
│   └─ IPC broker  (zerosec-verified channels) │
└───────────────┬─────────────────────────────┘
                │ isolated IPC (zerosec policy)
   ┌────────────┼────────────┬────────────────┐
   ▼            ▼            ▼                ▼
┌────────┐ ┌────────┐ ┌────────┐      ┌────────────┐
│ render │ │ render │ │ render │ ...  │ network    │
│ proc   │ │ proc   │ │ proc   │      │ service    │
│(tab 1) │ │(tab 2) │ │(tab N) │      │(zeronet)   │
└────────┘ └────────┘ └────────┘      └────────────┘
```

- **Network service** owns all sockets/TLS (reduces attack surface in renderers).
- **Renderer processes** are sandboxed by `zerosec` (seccomp/Win32-Job/mach).
  They cannot open sockets; they ask the network service.
- **Browser process** owns `zeroui` and the IPC broker.

## Data flow (loading a page)

```
URL ─▶ zeronet(resolve, connect, TLS, fetch)
        └▶ bytes ─▶ zerohtml(tokenize + tree)
                      └▶ zerodom(tree + events)
                          ├▶ zerocss(parse + cascade)
                          │     └▶ zerolayout(boxes)
                          │           └▶ zerorender(Vulkan paint)
                          └▶ hyperion-js(compile + run on events)
```

## Crate dependency graph

```
zeromium ──▶ zeroui, zerotask, zerosec, zerostore
zeroui    ──▶ zerorender, zerotask
zerorender──▶ zerofont, zerocodec, zeromem
zerolayout──▶ zerodom, zerocss, zeromem
zerocss   ──▶ zerodom, zeromem
zerohtml  ──▶ zerodom, zeromem
zerodom   ──▶ zeromem
hyperion-js──▶ zerodom, zeromem, zerotask
zeronet   ──▶ zerotask, zerosec, zerostore, rustls
zerosec   ──▶ zeromem
zeromem   ──▶ (none)
zerotask  ──▶ zeromem
```

No crate depends on `zerorender` or `hyperion-js` except where the browser
process wires them — keeps the core parse/style/layout testable headless.
