Given a piece of documentation stating:

```
/// Send `(valueInmGAV / 1000).fixed(0,3)` GAV from the account of `message.caller.address()`, to an account accessible only by `to.address()`.
```

We can parse this into several components:

- `///` The start-of-documentation marker; for this token, the end of documentation marker is the new-line-whitespace combination that isn't immediately followed by `///`. This is a doxygen-style format; also allowed is `/**` to begin and `*/` to end. In the latter, the `* ` found at the beginning of any intermediate lines is ignored.
- ` ` Separation-space after `///`. Ignored.
- `Send ` The first portion of the text.
- `(valueInmGAV / 1000).fixed(0,3)` A dynamic expression. This should be a valid Javascript/Paperscript expression, which when evaluated in an EVM Javascript environment initialised with various system values (such as parameters).
