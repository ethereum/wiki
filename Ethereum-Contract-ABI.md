### ABI specifics

The data input is encoded as:

* 1 byte *Method ID*; the 0-based index of the alphabetically ordered list of methods of the contract.
* 2-byte Big Endian parameter count for each arbitrary-sized parameter.
* Multiple N-byte *Parameter*; dependent on the specific parameter series.

The data output is encoded in much the same way but without the Method ID.

All variable-width parameters have their 2-byte item-count front-loaded in the data input.

Parameters are encoded as their full binary (and in the case of integral types, big-endian) representation. Integral types are allowed to be of arbitrary byte size up to 256-bits. Hash (or short unformatted data) types are again allowed to be of similar size. Bools are encoded as a simple byte. Enumerations are encoded as their shortest integral representation possible.

Strings may be either static (`string32`) or dynamic (`string`, `text`). In the static case, they are left-aligned within their container (making them compatible with C-style string manipulation functions). In the dynamic case, they are prefixed by one (`string`) or possibly two (`text`) bytes specifying the length. This (chosen over a zero-terminated strings) is to minimise gas used wile parsing the data input. There is no provision within the ABI for strings of greater than 65536 bytes. (To be honest, if you're wanting to communicate > 64K between contracts within such a storage-constrained environment like Ethereum 1.0, you should probably question your solution.)

We assume the following fixed-width types:
- `uint<N>` binary type of `N` bits, `N <= 256`, `N > 0`, `N % 8 == 0`. e.g. `uint32`, `uint8`, uint256.
- `int<N>`, `hash<N>`: equivalent to `uint<N>` except for the assumed interpretation and language typing.
- `uint`: equivalent to `uint256` (same with `int` and `hash`).
- `address`: equivalent to `hash160`, except for the assumed interpretation and language typing.
- `bool`: equivalent to `uint8`, except for the assumed interpretation and language typing.
- `string<N>`: binary type of `N` bytes, `N > 0`. Assumed to be UTF-8 encoded, right-aligned, zero-passed data.
- `real<N>x<M>`: fixed-point signed quantity of `N+M` bits, `N+M <= 256`, `N+M > 0`, `N % 8 == 0`, `M % 8 == 0`. Corresponds to the uint equivalent binary value divided by `2^M`.
- `ureal<N>x<M>`: unsigned variant of `real<N>x<M>`.
- `real`: equivalent to `real128x128` (same with `ureal`).
- `<type>[N]`: a fixed-length array of the given fixed-length type.

There are also several variable-length types:
- `string`: dynamic sized string (up to 65536 bytes). Assumed to be UTF-8 encoded.
- `<type>[]`: a variable-length array of the given fixed-length type.

For these variable length (or specifically, variable-count) types, we concatenate them (2-bytes each) and place them at the beginning of the data input, directly after the *Method ID*.

Thus for our `Foo` example if we wanted to call `baz` with the parameters 69 and true, we would pass 6 bytes total `0x010000004501`, which can be broken down into:

- `0x01`: the Method ID, (for `bar` it would be `0x00`, for `sam` 0x02).
- `0x00000045`: the first parameter, a uint32 value `69`.
- `0x01`: the second parameter - boolean `true`, encoded as a single byte.

It returns a single `bool`. If, for example, it were to return `false`, its output would be the single byte array `0x00`, a single bool encoded as a byte.
