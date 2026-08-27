# HTTP 204 No Content

`zeronet` handling of HTTP status **204 No Content**.

- **Class**: 2xx.
- **Behavior**: `zeronet` maps this to a response state / redirect / error.
- **Security**: redirects validated by `zerosec` origin rules (max 5 hops).
- **Tests**: `cargo fuzz run http1_response` and WPT fetch tests.

