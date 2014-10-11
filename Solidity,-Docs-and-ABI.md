An ABI is intended to serve as the de facto method for encoding & decoding data into & out of transactions.

For this ABI, contracts are treated as objects. They export a particular interface, not dissimilar from that of an OO language, for example for a contract `Foo`:

```
contract Foo
{
  function sam(string32 in1) { ... }
  function bar(uint256 in1, string in2) returns (string out1, bool out2) { ... }
  function baz(uint32 in1, real in2) returns bool { ... }

state:
  uint256 tom;
}
```

Note you haven't seen this language before. This is a new high level language codenamed Solidity, that will feel mostly similar to something between JavaScript and C++ but with a number of syntactic additions to make it suitable for writing contracts within Ethereum. Language additions include:

* static typing;
* contracts as first-class entities;
* state as part of a contract able to map & segment information into the storage;
* invariants, pre- and post-conditions as part of a contract;
* variadic return values with named components;
* a safe language subset allowing guaranteed static analysis & proofs;
* an inbuilt test-generation and execution environment for situations where formal proofing is not practical.

The above would result in three methods being exposed: `bar`, `baz` and `sam`, roughly expressed in JSON as:

```
{
  "bar": {
    "inputs": [
      "in1": "uint256",
      "in2": "string"
    ],
    "outputs": [
      "out1": "string",
      "out2": "bool"
    ]
  },
  "baz": {
    "inputs": [
      "in1": "uint32",
      "in2": "real"
    ],
    "outputs": [
      "__default": "bool"
    ]
  },
  "sam": {
    "inputs": [
      "in1": "string32"
    ],
    "outputs": []
  }
}
```


In previous versions of the proof-of-concept series, these methods would have differentiated against through placing their zero-padded ASCII representation within the first 32 bytes of the message data; similarly, all fixed types would use a full 32-byte word, regardless of their intrinsic size.

The purpose of the ABI is twofold:
* to avoid such a large waste of data by efficiently encoding the method identification and parameters;
* to provide a clear and formal means to specify the actions that a contract may do.

### Basic design

We assume the ABI is strongly typed, known at compilation time and static. No introspection mechanism will be provided. We assert that all contracts will have the interface definitions of any contracts they call available at compile-time.

This specification does not address contracts whose interface is dynamic or otherwise known only at run-time. Should these cases become important they can be adequately handled as facilities built within the Ethereum ecosystem.

### ABI specifics

The data input is encoded as:

* 1 byte *Method ID*; the 0-based index of the alphabetically ordered list of methods of the contract.
* Multiple N-byte *Parameter*; dependent on the specific parameter series.

The data output is encoded in much the same way but without the Method ID.

Parameters are encoded as their full binary (and in the case of integral types, big-endian) representation. Integral types are allowed to be of arbitrary byte size up to 256-bits. Hash (or short unformatted data) types are again allowed to be of similar size. Bools are encoded as a simple byte. Enumerations are encoded as their shortest integral representation possible.

Strings may be either static (`string32`) or dynamic (`string`, `text`). In the static case, they are left-aligned within their container (making them compatible with C-style string manipulation functions). In the dynamic case, they are prefixed by one (`string`) or possibly two (`text`) bytes specifying the length. This (chosen over a zero-terminated strings) is to minimise gas used wile parsing the data input. There is no provision within the ABI for strings of greater than 65536 bytes. (To be honest, if you're wanting to communicate > 64K between contracts within such a storage-constrained environment like Ethereum 1.0, you should probably question your solution.)

We assume the following types:
- `uint<N>` binary type of `N` bits, `N <= 256`, `N > 0`, `N % 8 == 0`. e.g. `uint32`, `uint8`, uint256.
- `int<N>`, `hash<N>`: equivalent to `uint<N>` except for the assumed interpretation and language typing.
- `uint`: equivalent to `uint256` (same with `int` and `hash`).
- `address`: equivalent to `hash160`, except for the assumed interpretation and language typing.
- `bool`: equivalent to `uint8`, except for the assumed interpretation and language typing.
- `string<N>`: binary type of `N` bytes, `N > 0`. Assumed to be UTF-8 encoded, right-aligned, zero-passed data.
- `string`: dynamic sized string (up to 256 bytes). Assumed to be UTF-8 encoded.
- `text`: dynamic sized large string (up to 65536 bytes). Assumed to be UTF-8 encoded.
- `real<N>x<M>`: fixed-point signed quantity of `N+M` bits, `N+M <= 256`, `N+M > 0`, `N % 8 == 0`, `M % 8 == 0`. Corresponds to the uint equivalent binary value divided by `2^M`.
- `ureal<N>x<M>`: unsigned variant of `real<N>x<M>`.
- `real`: equivalent to `real128x128` (same with `ureal`).

Thus for our `Foo` example if we wanted to call `baz` with the parameters 69 and true, we would pass 6 bytes total `0x010000004501`, which can be broken down into:

- `0x01`: the Method ID, (for `bar` it would be `0x00`, for `sam` 0x02).
- `0x00000045`: the first parameter, a uint32 value `69`.
- `0x01`: the second parameter - boolean `true`, encoded as a single byte.

It returns a single `bool`. If, for example, it were to return `false`, its output would be the single byte array `0x00`, a single bool encoded as a byte.

### Documentation

It is expected that each method, together with the contract itself, will be documented in several ways. Aside from informal descriptions, each method should come with a formalised method to describe exactly its effect on the state of Ethereum. It should also include, where possible, pre- and post-conditions on the contract state, and the contract itself should include invariants over its own state, again each translated into natural language.

For GavCoin, a meta coin that that is sub-divisible down to 1,000, and includes two functions `balance` and `send`, we might see the basic, undocumented contract as:

```
contract GavCoin
{
  function send(address to, uint256 valueInmGAV) {
    balances[to] += valueInmGAV;
    balances[transaction.sender] -= valueInmGAV;
  }
  function balance(address who) constant returns (uint256 balanceInmGAV) {
    balanceInmGAV = balances[who];
  }

invariants:
  reduce(0, +, map(valueOf, balances)) == 100000000000;

construction:
  balances[transaction.sender] = 100000000000;

state:
  mapping balances (address) returns uint256 with function(address a) returns uint256 { return a; };
};
```

Of course it is wrong. A static checker will analyse this (with the language assertion that the uint256 type must not be allowed to underflow unless explicitly given that attribute) and come back with something like:

```
Line 8: >>> balances[transaction.sender] -= valueInmGAV <<<:
  balances[...] may underflow with '-=' operation using unbounded operand valueInmGAV.
  Suggestion: surround code with conditional to bound operand valueInmGAV.
```

This is basically saying that it can see no reason why `valueInmGAV` could be no bigger than `balances[transaction.sender]`. And it's right. We forgot to check the parameters. It's easily fixed by adding the conditional and altering the method to:

```
  void send(address to, uint256 valueInmGAV) {
    if (balances[transaction.sender] >= valueInmGAV) {
      balances[to] += valueInmGAV;
      balances[transaction.sender] -= valueInmGAV;
    }
  }
```

This would then be formally documented:

```
contract GavCoin
{
  /// Send $((valueInmGAV / 1000).fixed(0,3)) GAV from the account of $(message.caller.address()), to an account accessible only by $(to.address()).
  function send(address to, uint256 valueInmGAV) {
    if (balances[message.caller] >= valueInmGAV) {
      balances[to] += valueInmGAV;
      balances[message.caller] -= valueInmGAV;
    }
  }
  
  /// $((balanceInmGAV / 1000).fixed(0,3)) GAV is the total funds available to $(who.address()).
  function balance(address who) constant returns (uint256 balanceInmGAV) {
    balanceInmGAV = balances[who];
  }

invariants:
  /// The sum total amount of GAV in the system is 1 million.
  reduce(0, add, map(valueOf, balances)) == 100000000000;

construction:
  /// Endows $(message.caller.address()) with 1m GAV.
  balances[message.caller] = 100000000000;

state:
  mapping balances(address) returns uint256 with function(address a) returns uint256 { return a; };
};
```

This documentation would then allow the Ethereum browser to translate any message (and thus transaction) going in to this contract into English (and on to other languages hence). It would also allow a lay viewer to immediately discern what the contract conforms to; in this case they could see that it's premined in favour of the transaction sender and that the total amount of coins in the system never changes.

For example, should a DApp, malicious or otherwise, attempt to send a transaction that gives all the user's GAV to itself, the user would, prior to signing and submission, receive a message saying something like:

```
Untrusted ÐApp "Foo Sprocket DApp" attempting to transact in your name:
Send 45.780 GAV from the account of Your Name Here to an account accessible only by Foo Sprocket DApp.
Do you wish to allow this?
```

Of course, they might be a bit more cunning and send it to an anonymous address, however, by differentiating friends' (and other known people) addresses from anonymous or untrusted addresses both visibly and clearly, we can imagine a mechanism that, at least for simple contracts, allow users a good level of security without excessive harm to their experience.

### Getting the Documentation in

The documentation, would be extracted from the source code ready to sit in a (probably JSON) file:

```
{
  "methods": {
    "send": "Send $((valueInmGAV / 1000).fixed(0,3)) GAV to an account accessible only by $(to.address()).",
    "balance": "$((balanceInmGAV / 1000).fixed(0,3)) GAV is the total funds available to $(who.address())."
  },
  "invariants": [
    "The sum total amount of GAV in the system is 1 million."
  ],
  "construction": [
    "Endows $(message.caller.address()) with 1m GAV."
  ]
}

```

This file would be hashed and distributed (either on a centralised website or, more preferably, through Swarm). It would be referenced by the Ethereum Singleton Trust contract in order to allow people or organisations that you know or trust to help inform you of its audit results and trustworth. Of course if you were a coder you could audit it manually (in this case it's pretty trivial) and determine how well the formal documentation matches the code, and also submit newer versions, perhaps in different languages that incorporate changes you feel are required to better descibe its actions & ramifications.

### The ABI-Description File

So the ABI description file ("header file") for the contract would be trivially derivable from the contract source code. It would be distributed by the author for anyone who wanted to message or transact with it. It would contain only enough information to compose the input data for a transaction and to decode the output data. Here's how the GAVCoin example contract would look:

```
[
  { "name": "send", "input": [ { "name": "to", "type": "address" }, { "name": "valueInmGAV", "type": "uint256" } ], "output": [] },
  { "name": "balance", "input": [ { "name": "who", "type": "address" } ], "output": [ { "name": "balanceInmGAV", "type": "uint256" } ] }
]
```

And that's it. Any questions to Gav.