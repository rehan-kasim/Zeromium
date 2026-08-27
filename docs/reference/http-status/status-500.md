# HTTP 500 Internal Server Error

`zeronet` handling of HTTP status **500 Internal Server Error**.

- **Class**: 5xx.
- **Behavior**: `zeronet` maps this to a response state / redirect / error.
- **Security**: redirects validated by `zerosec` origin rules (max 5 hops).
- **Tests**: `cargo fuzz run http1_response` and WPT fetch tests.

