### Basic design

We assume the ABI is strongly typed, known at compilation time and static. No introspection mechanism will be provided. We assert that all contracts will have the interface definitions of any contracts they call available at compile-time.

This specification does not address contracts whose interface is dynamic or otherwise known only at run-time. Should these cases become important they can be adequately handled as facilities built within the Ethereum ecosystem.

### Specifics

The first byte of the call data denotes the function to be called, it is the
0-based index into the list of functions of the contract, sorted
lexicographically by name (which are assumed to be UTF-8 encoded, compared in
binary). Starting from the first byte, the encoded arguments follow. The return
values are encoded in the same way, without the function index byte.

Types can have a fixed size or their size can depend on the value. For all
non-fixed-size types, in the order of their occurrence, the number of its
elements (not the byte size) as a 32-byte big-endian number follows.

After that, the arguments themselves are encoded.

The following fixed-size elementary types exist:
- `uint<N>`: unsigned integer type of `N` bits, `0 < N <= 256`, `N % 8 == 0`. e.g. `uint32`, `uint8`, `uint256`.
- `int<N>`: two's complement signed integer type of `N` bits, `0 < N <= 256`, `N % 8 == 0`.
- `hash<N>`: equivalent to `uint<N>` except for the assumed interpretation and language typing.
- `address`: equivalent to `hash160`, except for the assumed interpretation and language typing.
- `uint`, `int`, `hash`: equivalent to `uint256`, `int256`, `hash256`, respectively.
- `bool`: equivalent to `uint8` restricted to the values 0 and 1
- `real<N>x<M>`: fixed-point signed number of `N+M` bits, `0 < N + M <= 256`, `N % 8 == M % 8 == 0`. Corresponds to the int256 equivalent binary value divided by `2^M`.
- `ureal<N>x<M>`: unsigned variant of `real<N>x<M>`.
- `real`, `ureal`: equivalent to `real128x128`, `ureal128x128`
- `string<N>`: binary type of `N` bytes, `N > 0`. Unicode strings are assumed to be UTF-8 encoded.

They are all encoded in big-endian, padded to a multiple of 32 bytes. Signed
types are padded with ones on the higher-order side, unsigned types with zeros.
String types are padded with zeros on the lower-order side.  A `real<N>x<M>`
number `x` is encoded as the uint/int number `x * 2^M`. The behaviour is
unspecified for incorrect padding or values that are out of range.

The following (fixed-size) array type exists:
- `<type>[N]`: a fixed-length array of the given fixed-length type.

Each of its elements is encoded according to the above rules, which means that
the type `uint8[10]` takes `32 * 10 = 320` bytes.

The following non-fixed-size types exist: 
- `string`: dynamic sized string. Unicode strings are assumed to be UTF-8 encoded.
- `<type>[]`: a variable-length array of the given fixed-length type.

The number of elements (number of bytes for string, number of elements for
arrays) is given before any actual data (as described above). A `string` of
length `N` is encoded the same way as `string<N>` and a `<type>[]` with exactly
`N` elements is encoded the same way as `<type>[N]`.

Thus for our `Foo` example if we wanted to call `baz` with the parameters 69 and true, we would pass 65 bytes total, which can be broken down into:

- `0x01`: the Method ID, (for `bar` it would be `0x00`, for `sam`, `0x02`).
- `0x00000000000000000000000000000045`: the first parameter, a uint32 value `69` padded to 32 bytes
- `0x00000000000000000000000000000001`: the second parameter - boolean `true`, padded to 32 bytes

It returns a single `bool`. If, for example, it were to return `false`, its output would be the single byte array `0x00000000000000000000000000000000`, a single bool.

### Examples

* Method ID 2, no arguments

    02

* Method ID 0, arguments [3, 5, 7]

    00
    0000000000000000000000000000000000000000000000000000000000000003
    0000000000000000000000000000000000000000000000000000000000000005
    0000000000000000000000000000000000000000000000000000000000000007

* Method ID 9, arguments [4, 3**160, -9]

    09
    0000000000000000000000000000000000000000000000000000000000000004
    304d37f120d696c834550e63d9bb9c14b4f9165c9ede434e4644e3998d6db881
    fffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff7

* Method ID 5, arguments ["george"]

    05
    0000000000000000000000000000000000000000000000000000000000000006
    67656f726765

* Method ID 5, arguments [1, -1, "george"]

    05
    0000000000000000000000000000000000000000000000000000000000000006
    0000000000000000000000000000000000000000000000000000000000000001
    ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff
    67656f726765

* Method ID 3, arguments [[1,2,3], 4, "george"]

    03
    0000000000000000000000000000000000000000000000000000000000000003
    0000000000000000000000000000000000000000000000000000000000000006
    0000000000000000000000000000000000000000000000000000000000000004
    000000000000000000000000000000000000000000000000000000000000000100000000000000000000000000000000000000000000000000000000000000020000000000000000000000000000000000000000000000000000000000000003
    67656f726765