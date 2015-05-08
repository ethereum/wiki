We will now formally specify the encoding, such that it will have the following
properties, which are especially useful if some arguments are nested arrays:

**Properties:**

1. The number of reads necessary to access a value is at most the depth of the
value inside the argument array structure, i.e. four reads are needed to
retrieve `a_i[k][l][r]`. In a previous version of the ABI, the number of reads scaled
linearly with the total number of dynamic parameters in the worst case.

2. The data of a variable or array element is not interleaved with other data
and it is relocatable, i.e. it only uses relative "addresses"


**Definition:** The following types are called "dynamic":
* `bytes`
* `T[]` for any `T`
* `T[k]` for any dynamic `T` and any `k > 0`

All other types are called "static".

**Definition:** `len(a)` is the number of bytes in a binary string `a`.
The type of `len(a)` is assumed to be `uint256`.

We define `enc` as a mapping of values of the ABI types to binary strings such
that `len(enc(X))` depends on the value of `X` if and only if the type of `X`
is dynamic.

**Definition:** For any ABI value `X`, we recursiveliy define `enc(X)`, depending
on the type of `X` being

- `T[k]` for any `T` and `k`:

  `enc(X) = head(X[0]) ... head(X[k-1]) tail(X[0]) ... tail(X[k-1])`

  where `head` and `tail` are defined for `X[i]` being of a static type as
    `head(X[i]) = enc(X[i])` and `tail(X[i]) = ""` (the empty string)
  and as
    `head(X[i]) = enc(len(head(X[0]) ... head(X[k-1]) tail(X[0]) ... tail(X[i-1])))`
    `tail(X[i]) = enc(X[i])`
  otherwise.

  Note that in the dynamic case, `head(X[i])` is well-defined since the lengths of
  the head parts only depend on the types and not the values. Its value is the offset
  of the beginning of `tail(X[i])` relative to the start of `enc(X)`.
  
- `T[]` where `X` has `k` elements (`k` is assumed to be of type `uint256`):

  `enc(X) = enc(k) enc([X[1], ..., X[k]])`

  i.e. it is encoded as if it were an array of static size `k`, prefixed with
  the number of elements.

- `bytes`, of length `k` (which is assumed to be of type `uint256`):

  `enc(X) = enc(k) pad_right(X)`, i.e. the number of bytes is encoded as a
    `uint256` followed by the actual value of `X` as a byte sequence, followed by
    the minimum number of zero-bytes such that `len(enc(X))` is a multiple of 32.

- `uint<N>`: `enc(X)` is the big-endian encoding of `X`, padded on the higher-order side with zero-bytes such that the length is a multiple of 32 bytes.
- `uint`: as in the `uint256` case
- `address`: as in the `uint120` case
- `int<N>`: `enc(X)` is the big-endian two's complement encoding of `X`, padded on the higher-oder side with `0xff` for negative `X` and with zero bytes for positive `X` such that the length is a multiple of 32 bytes.
- `int`: as in the `int256` case
- `bool`: as in the `uint8` case, where `1` is used for `true` and `0` for `false`
- `real<N>x<M>`: `enc(X)` is `enc(X * 2**M)` where `X * 2**M` is interpreted as a `int256`.
- `real`: as in the `real128x128` case
- `ureal<N>x<M>`: `enc(X)` is `enc(X * 2**M)` where `X * 2**M` is interpreted as a `uint256`.
- `ureal`: as in the `ureal128x128` case
- `bytes<N>`: `enc(X)` is the sequence of bytes in `X` padded with zero-bytes to a length of 32.

Note that for any `X`, `len(enc(X))` is a multiple of 32.

A call to the function `f` with parameters `a_1, ..., a_n` is encoded as

  `signature_hash(f) enc([a_1, ..., a_n])`

and the return values `v_1, ..., v_k` of `f` are encoded as

  `enc([v_1, ..., v_k])`

where the types of `[a_1, ..., a_n]` and `[v_1, ..., v_k]` are assumed to be
fixed-size arrays of length `n` and `k`, respectively. Note that strictly,
`[a_1, ..., a_n]` can be an "array" with elements of different types, but the
encoding is still well-defined as the assumed common type `T` (above) is not
actually used.

## Examples

### Use of Dynamic Types

A call to a function with the signature `f(uint,uint32[],bytes10,bytes)` with values `(0x123, [0x456, 0x789], "1234567890", "Hello, world!")` is encoded in the following way:

The first four bytes are `sha3("f(uint256,uint32[],bytes10,bytes)")`, i.e. `0x8be65246`.
Then we encode the head parts of all four arguments. For the static types `uint256` and `bytes10`, these are directly the values we want to pass, whereas for the dynamic types `uint32[]` and `bytes`, we use the offset in bytes to the start of their data area, measured from the start of the value encoding (i.e. not counting the first four bytes containing the hash of the function signature). These are:

 - `0x0000000000000000000000000000000000000000000000000000000000000123` (`0x123` padded to 32 bytes)
 - `0x0000000000000000000000000000000000000000000000000000000000000080` (offset to start of data part of second parameter, 4*32 bytes, exactly the size of the head part)
 - `0x3132333435363738393000000000000000000000000000000000000000000000` (`"1234567890"` padded to 32 bytes on the right)
 - `0x00000000000000000000000000000000000000000000000000000000000000e0` (offset to start of data part of fourth parameter = offset to start of data part of first dynamic parameter + size of data part of first dynamic parameter = 4*32 + 3*32 (see below))

After this, the data part of the first dynamic argument, `[0x456, 0x789]` follows:

 - `0x0000000000000000000000000000000000000000000000000000000000000002` (number of elements of the array, 2)
 - `0x0000000000000000000000000000000000000000000000000000000000000456` (first element)
 - `0x0000000000000000000000000000000000000000000000000000000000000789` (second element)

Finally, we encode the data part of the second dynamic argument, `"Hello, world!"`:

 - `0x000000000000000000000000000000000000000000000000000000000000000d` (number of elements (bytes in this case): 13)
 - `0x48656c6c6f2c20776f726c642100000000000000000000000000000000000000` (`"Hello, world!"` padded to 32 bytes on the right)

All together, the encoding is (spaces added for clarity):

`0x8be65246 0000000000000000000000000000000000000000000000000000000000000123 0000000000000000000000000000000000000000000000000000000000000080 3132333435363738393000000000000000000000000000000000000000000000 00000000000000000000000000000000000000000000000000000000000000e0 0000000000000000000000000000000000000000000000000000000000000002 0000000000000000000000000000000000000000000000000000000000000456 0000000000000000000000000000000000000000000000000000000000000789 000000000000000000000000000000000000000000000000000000000000000d 48656c6c6f2c20776f726c642100000000000000000000000000000000000000`