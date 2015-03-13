# Functions

### Basic design

We assume the ABI is strongly typed, known at compilation time and static. No introspection mechanism will be provided. We assert that all contracts will have the interface definitions of any contracts they call available at compile-time.

This specification does not address contracts whose interface is dynamic or otherwise known only at run-time. Should these cases become important they can be adequately handled as facilities built within the Ethereum ecosystem.

### Specifics

The first four bytes of the call data denotes the function to be called, it is the
first (left, high-order in big-endian) four bytes of the Keccak (SHA-3) hash of the signature of the function. The signature is defined as the canonical expression of the basic prototype. Starting from the fifth byte, the encoded arguments follow. The return values are encoded in the same way, without the four bytes specifying the function.

Next, a list of the values of each argument follows. For this, note that types can have a
fixed size or their size can depend on the value. For a non-fixed-size types only the number of its
elements (not the byte size) as a 32-byte big-endian number is used. The actual values of
the non-fixed-size types are encoded after the list of fixed-size values.

Formally:
```
enc(f(a_1, ..., a_n)) = signature_hash(f) fixed(a_1) ... fixed(a_n) variable(a_1) ... variable(a_n) 
```
Where `fixed(a_i)` is the actual value of the argument for fixed-size elements (for the encoding see below) and the number of elements for non-fixed-size types. `variable(a_i)` is the empty string for fixed-size types and the actual data for non-fixed size types (for the encoding see below).

The following fixed-size elementary types exist:
- `uint<N>`: unsigned integer type of `N` bits, `0 < N <= 256`, `N % 8 == 0`. e.g. `uint32`, `uint8`, `uint256`.
- `int<N>`: two's complement signed integer type of `N` bits, `0 < N <= 256`, `N % 8 == 0`.
- `address`: equivalent to `hash160`, except for the assumed interpretation and language typing.
- `uint`, `int`, `hash`: equivalent to `uint256`, `int256`, `hash256`, respectively.
- `bool`: equivalent to `uint8` restricted to the values 0 and 1
- `real<N>x<M>`: fixed-point signed number of `N+M` bits, `0 < N + M <= 256`, `N % 8 == M % 8 == 0`. Corresponds to the int256 equivalent binary value divided by `2^M`.
- `ureal<N>x<M>`: unsigned variant of `real<N>x<M>`.
- `real`, `ureal`: equivalent to `real128x128`, `ureal128x128`
- `bytes<N>`: binary type of `N` bytes, `N >= 0`. Unicode strings are assumed to be UTF-8 encoded.

They are all encoded in big-endian, padded to a multiple of 32 bytes. Negative values
(for signed types) are padded with ones on the higher-order side, non-negative values
and unsigned types with zeros.
Byte types are padded with zeros on the lower-order side.  A `real<N>x<M>`
number `x` is encoded as the uint/int number `x * 2^M`. The behaviour is
unspecified for incorrect padding or values that are out of range.

The following (fixed-size) array type exists:
- `<type>[N]`: a fixed-length array of the given fixed-length type.

Each of its elements is encoded according to the above rules, which means that
the type `uint8[10]` takes `32 * 10 = 320` bytes.

The following non-fixed-size types exist: 
- `bytes`: dynamic sized string. Unicode strings are assumed to be UTF-8 encoded.
- `<type>[]`: a variable-length array of the given fixed-length type.

The number of elements (number of bytes for the byres type, number of elements for
arrays) is given before any actual data (as described above) as a 256 bit big endian integer.
A `bytes` of length `N` is encoded the same way as `bytes<N>` and a `<type>[]` with exactly
`N` elements is encoded the same way as `<type>[N]`.

### Signature

The function signature is simply the function name with the parenthesised list of parameter types. Parameter types are split by a single comma - no spaces are used.

### Examples

Given the contract:

```js
contract Foo {
  function bar(real[2] xy) {}
  function baz(uint32 x, bool y) returns (bool r) { r = x > 32 || y; }
  function sam(bytes name, uint[] data) {}
}
```

Thus for our `Foo` example if we wanted to call `baz` with the parameters `69` and `true`, we would pass 68 bytes total, which can be broken down into:

- `0xf7183750`: the Method ID. This is derived as the first 4 bytes of the Keccak hash of the ASCII form of the signature `baz(uint32,bool)`.
- `0x0000000000000000000000000000000000000000000000000000000000000045`: the first parameter, a uint32 value `69` padded to 32 bytes
- `0x0000000000000000000000000000000000000000000000000000000000000001`: the second parameter - boolean `true`, padded to 32 bytes

In total:
```
0xf718375000000000000000000000000000000000000000000000000000000000000000450000000000000000000000000000000000000000000000000000000000000001
```
It returns a single `bool`. If, for example, it were to return `false`, its output would be the single byte array `0x0000000000000000000000000000000000000000000000000000000000000000`, a single bool.

If we wanted to call `bar` with the argument `[2.125, 8.5]`, we would pass 68 bytes total, broken down into:
- `0x3e279860`: the Method ID. This is derived from the signature `bar(real128x128[2])`. Note that `real` is substituted for its canonical representation `real128x128`.
- `0x0000000000000000000000000000000240000000000000000000000000000000`: the first part of the first parameter, a read128x128 value `2.125`.
- `0x0000000000000000000000000000000880000000000000000000000000000000`: the first part of the first parameter, a read128x128 value `8.5`.

In total:
```
0x3e27986000000000000000000000000000000002400000000000000000000000000000000000000000000000000000000000000880000000000000000000000000000000
```

If we wanted to call `sam` with the arguments `"dave"` and `[1,2,3]`, we would pass 136 bytes total, broken down into:
- `0x8FF261B0`: the Method ID. This is derived from the signature `sam(bytes,uint256[])`. Note that `uint` is substituted for its canonical representation `uint256`.
- `0x0000000000000000000000000000000000000000000000000000000000000004`: the size of the first dynamic parameter, measured as the bytes type length in bytes. In this case, 4.
- `0x0000000000000000000000000000000000000000000000000000000000000003`: the size of the second dynamic parameter, measured as the number of items in the array. In this case it has a size of 3 items.
- `0x6461766500000000000000000000000000000000000000000000000000000000`: the first parameter: the UTF-8 (equal to ASCII in this case) encoding of `"dave"`.
- `0x0000000000000000000000000000000000000000000000000000000000000001`: the first entry of the second parameter.
- `0x0000000000000000000000000000000000000000000000000000000000000002`: the second entry of the second parameter.
- `0x0000000000000000000000000000000000000000000000000000000000000003`: the third entry of the second parameter.

In total:
```
0xe4ae26d6000000000000000000000000000000000000000000000000000000000000000400000000000000000000000000000000000000000000000000000000000000036461766500000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000100000000000000000000000000000000000000000000000000000000000000020000000000000000000000000000000000000000000000000000000000000003
```


# Events

Events are an abstraction of the Ethereum logging/event-watching protocol. Log entries provide the contract's address, a series of up to four topics and some arbitrary length binary data. Events leverage the existing function ABI in order to interpret this (together with an interface spec) as a properly typed structure.

Given an event name and series of event parameters, we split them into two sub-series: those which are indexed and those which are not. Those which are indexed, which may number up to 3, are used alongside the Keccak hash of the event signature to form the topics of the log entry. Those which as not indexed form the byte array of the event.

In effect, a log entry using this ABI is described as:

- `address`: the address of the contract (intrinsically provided by Ethereum);
- `topics[0]`: `keccak(EVENT_NAME+"("+EVENT_ARGS.map(canonical_type_of).join(",")+")")` (`canonical_type_of` is a function that simply returns the canonical type of a given argument, e.g. for `uint indexed foo`, it would return `uint256`);
- `topics[n]`: `EVENT_INDEXED_ARGS[n - 1]` (`EVENT_INDEXED_ARGS` is the series of `EVENT_ARGS` that are indexed);
- `data`: `abi_serialise(EVENT_NON_INDEXED_ARGS)` (`EVENT_NON_INDEXED_ARGS` is the series of `EVENT_ARGS` that are not indexed, `abi_serialise` is the ABI serialisation function used for returning a series of typed values from a function, as described above).

# JSON

The JSON format for a contract's interface is given by an array of function and/or event descriptions. A function description is a JSON object with the fields:

- `type`: always `"function"` (the default, and so may be omitted);
- `name`: the name of the function;
- `inputs`: an array of objects, each of which contains:
* `name`: the name of the parameter;
* `type`: the canonical type of the parameter.
- `outputs`: an array of objects similar to `inputs`.

An event description is a JSON object with fairly similar fields:

- `type`: always `"event"`
- `name`: the name of the function;
- `inputs`: an array of objects, each of which contains:
* `name`: the name of the parameter;
* `type`: the canonical type of the parameter.
* `indexed`: `true` if the field is part of the log's data segment, `false` if it one of the log's topics.

For example, 

```js
contract Test {
function Test(){ b = 0x12345678901234567890123456789012; }
event Event(uint indexed a, hash b)
event Event2(uint indexed a, hash b)
function foo(uint a) { Event(a, b); }
hash b;
}
```

would result in the JSON:

```js
[{
"type":"event",
"inputs": [{"name":"a","type":"uint256","indexed":true},{"name":"b","type":"hash256","indexed":false}],
"name":"Event"
}, {
"type":"event",
"inputs": [{"name":"a","type":"uint256","indexed":true},{"name":"b","type":"hash256","indexed":false}],
"name":"Event2"
}, {
"type":"function",
"inputs": [{"name":"a","type":"uint256"}],
"name":"foo",
"outputs": []
}]
```

# Example Javascript Usage

```js
var Test = eth.contract(
[{
"type":"event",
"inputs": [{"name":"a","type":"uint256","indexed":true},{"name":"b","type":"hash256","indexed":false}],
"name":"Event"
}, {
"type":"event",
"inputs": [{"name":"a","type":"uint256","indexed":true},{"name":"b","type":"hash256","indexed":false}],
"name":"Event2"
}, {
"type":"function",
"inputs": [{"name":"a","type":"uint256"}],
"name":"foo",
"outputs": []
}]);
var theTest = new Test(addrTest);

// examples of usage:
// every log entry ("event") coming from theTest (i.e. Event & Event2):
var f0 = eth.filter(theTest);
// just log entries ("events") of type "Event" coming from theTest:
var f1 = eth.filter(theTest.Event);
// also written as
var f1 = theTest.Event();
// just log entries ("events") of type "Event" and "Event2" coming from theTest:
var f2 = eth.filter([theTest.Event, theTest.Event2]);
// just log entries ("events") of type "Event" coming from theTest with indexed parameter 'a' equal to 69:
var f3 = eth.filter(theTest.Event, {'a': 69});
// also written as
var f3 = theTest.Event({'a': 69});
// just log entries ("events") of type "Event" coming from theTest with indexed parameter 'a' equal to 69 or 42:
var f4 = eth.filter(theTest.Event, {'a': [69, 42]});
// also written as
var f4 = theTest.Event({'a': [69, 42]});

// options may also be supplied as a second parameter with `earliest`, `latest`, `offset` and `max`, as defined for `eth.filter`.
var options = { 'max': 100 };
var f4 = theTest.Event({'a': [69, 42]}, options);

var trigger;
f4.watch(trigger);

// call foo to make an Event:
theTest.foo(69);

// would call trigger like:
//trigger(theTest.Event, {'a': 69, 'b': '0x12345678901234567890123456789012'}, n);
// where n is the block number that the event triggered in.
```

Implementation:

```js
// e.g. f4 would be similar to:
web3.eth.filter({'max': 100, 'address': theTest.address, 'topics': [ [69, 42] ]});
// except that the resultant data would need to be converted from the basic log entry format like:
{
  'address': theTest.address,
  'topics': [web3.sha3("Event(uint256,hash256)"), 0x00...0045 /* 69 in hex format */],
  'data': '0x12345678901234567890123456789012',
  'number': n
}
// into data good for the trigger, specifically the three fields:
  Test.Event // derivable from the first topic
  {'a': 69, 'b': '0x12345678901234567890123456789012'} // derivable from the 'indexed' bool in the interface, the later 'topics' and the 'data'
  n // from the 'number'
```

Event result:
```js
[ {
  'event': Test.Event,
  'args': {'a': 69, 'b': '0x12345678901234567890123456789012'},
  'number': n
  },
  { ...
  } ...
]
```

### JUST DONE! [develop branch]
**NOTE: THIS IS OLD - IGNORE IT unless reading for historical purposes**

- Internal LogFilter, log-entry matching mechanism and eth_installFilter needs to support matching multiple values (OR semantics) *per* topic index (at present it will only match topics with AND semantics and set-inclusion, not per-index).

i.e. at present you can only ask for each of a number of given topic values to be matched throughout each topic:

- `topics: [69, 42, "Gav"]` would match against logs with 3 topics `[42, 69, "Gav"]`, `["Gav", 69, 42]` but **not** against logs with topics `[42, 70, "Gav"]`.

we need to be able to provide one of a number of topic values, and, each of these options for each topic index:

- `topics: [[69, 42], [] /* anything */, "Gav"]` should match against logs with 3 topics `[42, 69, "Gav"]`, `[42, 70, "Gav"]` but **not** against `["Gav", 69, 42]`.