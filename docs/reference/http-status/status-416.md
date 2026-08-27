# HTTP 416 Range Not Satisfiable

`zeronet` handling of HTTP status **416 Range Not Satisfiable**.

- **Class**: 4xx.
- **Behavior**: `zeronet` maps this to a response state / redirect / error.
- **Security**: redirects validated by `zerosec` origin rules (max 5 hops).
- **Tests**: `cargo fuzz run http1_response` and WPT fetch tests.

