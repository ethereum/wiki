---
name: Solidity Docs and ABI
category: 
---

*NOTE: This is intended largely as a vague overview and a historical reference. For specific details and the latest specification, see [Ethereum Contract ABI](https://github.com/ethereum/wiki/wiki/Ethereum-Contract-ABI), [Ethereum Natural Specification Format](https://github.com/ethereum/wiki/wiki/Ethereum-Natural-Specification-Format) and [Solidity Tutorial](https://github.com/ethereum/wiki/wiki/Solidity-Tutorial).*

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


In previous versions of the proof-of-concept series, only simple 32-byte values were supported; the ABI adds the ability to also have variably sized arguments.

### ABI

See [Ethereum Contract ABI](https://github.com/ethereum/wiki/wiki/Ethereum-Contract-ABI).

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
/// @title Some title here.
/// @author Homer Simpson
contract GavCoin
{
  /// @notice Send `(valueInmGAV / 1000).fixed(0,3)` GAV from the account of
  /// `message.caller.address()`, to an account accessible only by `to.address()`.
  /// @dev This is the developer documentation.
  /// @param to The docs for the first param.
  /// @param valueInmGav The docs for the second param.
  function send(address to, uint256 valueInmGAV) {
    if (balances[message.caller] >= valueInmGAV) {
      balances[to] += valueInmGAV;
      balances[message.caller] -= valueInmGAV;
    }
  }
  
  /// @notice `(balanceInmGAV / 1000).fixed(0,3)` GAV is the total funds available to `who.address()`.
  function balance(address who) constant returns (uint256 balanceInmGAV) {
    balanceInmGAV = balances[who];
  }

invariants:
  /// @notice The sum total amount of GAV in the system is 1 million.
  reduce(0, add, map(valueOf, balances)) == 100000000000;

construction:
  /// @notice Endows `message.caller.address()` with 1m GAV.
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
  "source": "...",
  "language": "Solidity",
  "languageVersion": 1,
  "methods": {
    "send": { "notice": "Send `(valueInmGAV / 1000).fixed(0,3)` GAV from the account of `message.caller.address()`, to an account accessible only by `to.address()`." },
    "balance": { "notice": "`(balanceInmGAV / 1000).fixed(0,3)` GAV is the total funds available to `who.address()`." }
  },
  "invariants": [
    { "notice": "The sum total amount of GAV in the system is 1 million." }
  ],
  "construction": [
    { "notice": "Endows `message.caller.address()` with 1m GAV." }
  ]
}
```

The full documentation format, that includes developer-specific documentation includes several more attributes:

```
{
  "author": "Gav Wood",
  "description": "Some description of this contract.",
  "methods": {
    "send": {
      "title": "Send some GAV.",
      "details": "..."
    },
    "balance": {
      "title": "Send some GAV.",
      "details": "..."
    }
  },
  "invariants": [
    { "title": "...", "details": "Markdown description of the first invariant." }
  ],
  "construction": {
    "details": "Creates the contract with..."
  }
}
```

This file would be hashed and distributed (either on a centralised website or, more preferably, through Swarm). It would be referenced by the Ethereum Singleton Trust contract in order to allow people or organisations that you know or trust to help inform you of its audit results and trustworth. Of course if you were a coder you could audit it manually (in this case it's pretty trivial) and determine how well the formal documentation matches the code, and also submit newer versions, perhaps in different languages that incorporate changes you feel are required to better descibe its actions & ramifications.

### The ABI-Description File

So the ABI description file ("header file") for the contract would be trivially derivable from the contract source code. It would be distributed by the author for anyone who wanted to message or transact with it. It would contain only enough information to compose the input data for a transaction and to decode the output data. Here's how the GAVCoin example contract would look:

TODO: include `const`ness in the JSON:

```
[
  { "name": "send", "const": false, "input": [ { "name": "to", "type": "address" }, { "name": "valueInmGAV", "type": "uint256" } ], "output": [] },
  { "name": "balance", "const": true, "input": [ { "name": "who", "type": "address" } ], "output": [ { "name": "balanceInmGAV", "type": "uint256" } ] }
]
```

And that's it. Any questions to Gav.