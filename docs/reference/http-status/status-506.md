# HTTP 506 Variant Also Negotiates

`zeronet` handling of HTTP status **506 Variant Also Negotiates**.

- **Class**: 5xx.
- **Behavior**: `zeronet` maps this to a response state / redirect / error.
- **Security**: redirects validated by `zerosec` origin rules (max 5 hops).
- **Tests**: `cargo fuzz run http1_response` and WPT fetch tests.

