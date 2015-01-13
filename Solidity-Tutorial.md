Solidity is a high-level language whose syntax is similar to that of Javascript and it is designed to compile to code for the Ethereum Virtual Machine. This
tutorial provides a basic introduction to Solidity and assumes some knowledge of
the Ethereum Virtual Machine and programming in general. For more details,
please see the Solidity specficiation (yet to be written). This tutorial does not cover features like
the natural language documentation or formal verification and is also not meant as a final specification
of the language.

You can start using [Solidity in your browser](http://chriseth.github.io/cpp-ethereum),
with no need to download or compile anything. This application only supports
compilation - if you want to run the code or inject it into the blockchain, you
have to use a client like AlethZero.



## Simple Example

```
contract SimpleStorage {
    uint storedData;
    function set(uint x) {
        storedData = x;
    }
    function get() constant returns (uint retVal) {
        return storedData;
    }
}
```

`uint storedData` declares a state variable called `storedData` of type `uint`
(unsigned integer of 256 bits) whose position in storage is automatically
allocated by the compiler. The functions `set` and `get` can be used to modify
or retrieve the value of the variable.

## Subcurrency Example

```
contract Coin {
    address minter;
    mapping (address => uint) balances;
    function Coin() {
        minter = msg.sender;
    }
    function mint(address owner, uint amount) {
        if (msg.sender != minter) return;
        balances[owner] += amount;
    }
    function send(address receiver, uint amount) {
        if (balances[msg.sender] < amount) return;
        balances[msg.sender] -= amount;
        balances[receiver] += amount;
    }
    function queryBalance(address addr) constant returns (uint balance) {
        return balances[addr];
    }
}
```

This contract introduces some new concepts. One of them is the `address` type,
which is a 160 bit value that does not allow any arithmetic operations.
Furthermore, the state variable `balance` is of a complex datatype that maps
addresses to unsigned integers. Mappings can be seen as hashtables which are
virtually initialized such that every possible key exists and is mapped to a
value whose byte-representation is all zeros. The special function `Coin` is the
constructor which is run during creation of the contract and
cannot be called afterwards. It permanently stores the address of the person creating the
contract: Together with `tx` and `block`, `msg` is a magic global variable that
contains some properties which allow access to the world outside of the contract.
The function `queryBalance` is declared `constant` and thus is not allowed to
modify the state of the contract (note that this is not yet enforced, though).
In Solidity, return "parameters" are named and essentially create a local
variable. So to return the balance, we could also just use `bal =
balance[addr];` without any return statement.

## Comments

Single-line comments (`//`) and multi-line comments (`/*...*/`) are possible, while
triple-slash comments (`///`) right in front of function declarations introduce NatSpec
comments (which are not covered here).

## Types

The currently implemented (elementary) types are booleans (`bool`), integer and fixed-length string (string0 to string32) types.
The integer types are signed and unsigned integers of various bit widths
(`int8`/`uint8` to `int256`/`uint256` in steps of 8 bits, where `uint`/`int` are
aliases for `uint256`/`int256`), hashes (`hash8` to `hash256` again in 8 bit
steps and `hash` is an alias for `hash256`) and addresses (of 160 bits).

Comparisons (`<=`, `!=`, `==`, etc.) always yield booleans which can be
combined using `&&`, `||` and `!`. Note that the usual short-circuiting rules
apply for `&&` and `||`, which means that for expressions of the form
`(0 < 1 || fun())`, the function is actually never called.

If an operator is applied to different integer types, the compiler tries to
implicitly convert one of the operands to the type of the other (the same is
true for assignments). In general, an implicit conversion is possible if it
makes sense semantically and no information is lost: `uint8` is convertible to
`uint16` and `int120` to `int256`, but `int8` is not convertible to `uint256`.
Furthermore, unsigned integers can be converted to hashes of the same or larger
size, but not vice-versa. Any type that can be converted to `hash160` can also
be converted to `address`.

If the compiler does not allow implicit conversion but you know what you are
doing, an explicit type conversion is sometimes possible:

```
int8 y = -3;
uint x = uint(y);
```

At the end of this code snippet, `x` will have the value `0xfffff..fd` (64 hex
characters), which is -3 in two's complement representation of 256 bits.

For convenience, it is not always necessary to explicitly specify the type of a
variable, the compiler automatically infers it from the type of the first
expression that is assigned to the variable:
```
hash x = 0x123;
var y = x;
```
Here, the type of `y` will be `hash`. Using `var` is not possible for function
parameters or return parameters.

## Integer Literals

The type of integer literals is not determined as long as integer literals are
combined with themselves. This is probably best explained with examples:

```
var x = 1 - 2;
```
The value of `1 - 2` is `-1`, which is assigned to `x` and thus `x` receives
the type `int8` -- the smallest type that contains `-1`. The following code
snippet behaves differently, though:
```
var one = 1;
var two = 2;
var x = one - two;
```

Here, `one` and `two` both have type `uint8` which is also propagated to `x`. The
subtraction inside the type `uint8` causes wrapping and thus the value of
`x` will be `255`.

It is even possible to temporarily exceed the maximum of 256 bits as long as
only integer literals are used for the computation:
```
var x = (0xffffffffffffffffffff * 0xffffffffffffffffffff) * 0;
```
Here, `x` will have the value `0` and thus the type `uint8`.

## Control Structures

Most of the control structures from C/JavaScript are available in Solidity
except for `switch` (not planned) and `goto` (note that it's called Solidity). So
there is: `if`, `else`, `while`, `for`, `break`, `continue`, `return`. Note that there
is no type conversion from non-boolean to boolean types as there is in C and
JavaScript, so `if (1) { ... }` is _not_ valid Solidity.

## Special Variables and Functions

There are special variables and functions which always exist in the global
namespace.

### Block and Transaction Properties

 - `block.coinbase` (`address`): current block miner's address
 - `block.difficulty` (`uint`): current block difficulty
 - `block.gaslimit` (`uint`): current block gaslimit
 - `block.number` (`uint`): current block number
 - `block.prevhash` (`hash`): previous block hash
 - `block.timestamp` (`uint`): current block timestamp
 - `msg.gas` (`uint`): remaining gas
 - `msg.sender` (`address`): sender of the message (current call)
 - `msg.value` (`uint`): number of wei sent with the message
 - `tx.gasprice` (`uint`): gas price of the transaction
 - `tx.origin` (`address`): sender of the transaction (full call chain)

### Cryptographic Functions

 - `sha3(hash) returns (hash)`: compute the SHA3 hash of 256 bits of data
 - `sha256(hash) returns (hash)`: compute the SHA256 hash of 256 bits of data
 - `ripemd160(hash) returns (hash160)`: compute RIPEMD of 256 bits of data
 - `ecrecover(hash, hash8, hash, hash) returns (address)`: recover public key from elliptic curve signature

### Contract Related

 - `this` (current contract's type): the current contract, explicitly convertible to `address`
 - `suicide(address)`: suicide the current contract, sending its funds to the given address

Furthermore, all functions of the current contract are callable directly including the current function.

## Functions on addresses

It is possible to query the balance of an address using the property `balance`
and to send Ether (in units of wei) to an address using the `send` function:

```
address x = 0x123;
if (x.balance < 10 && address(this).balance >= 10) x.send(10);
```

Furthermore, to interface with contracts that do not adhere to the ABI (like the NameReg contract),
the functions `callstring32` and `callstring32string32` are provided which take one or two arguments of type string32 (they might change in the future):

```
address nameReg = 0x72ba7d8e73fe8eb666ea66babc8116a41bfb10e2;
nameReg.callstring32string32("register", "MyName");
```

Note that contracts inherit all members of address, so it is possible to query the balance of the
current contract using `this.balance`.

## Order of Evaluation of Expressions

The evaluation order of expressions is not specified (more formally, the order
in which the children of one node in the expression tree are evaluated is not
specified, but they are of course evaluated before the node itself). It is only
guaranteed that statements are executed in order and short-circuiting for
boolean expressions is done.

## Arrays

Arrays are planned but not implemented yet.

## Structs

Solidity provides a way to define new types in the form of structs, which is
shown in the following example:

```
contract CrowdFunding {
  struct Funder {
    address addr;
    uint amount;
  }
  struct Campaign {
    address beneficiary;
    uint fundingGoal;
    uint numFunders;
    uint amount;
    mapping (uint => Funder) funders;
  }
  uint numCampaigns;
  mapping (uint => Campaign) campaigns;
  function newCampaign(address beneficiary, uint goal) returns (uint campaignID) {
    campaignID = numCampaigns++; // campaignID is return variable
    Campaign c = campaigns[campaignID];  // assigns reference
    c.beneficiary = beneficiary;
    c.fundingGoal = goal;
  }
  function contribute(uint campaignID) {
    Campaign c = campaigns[campaignID];
    Funder f = c.funders[c.numFunders++];
    f.addr = msg.sender;
    f.amount = msg.value;
    c.amount += f.amount;
  }
  function checkGoalReached(uint campaignID) returns (bool reached) {
    Campaign c = campaigns[campaignID];
    if (c.amount < c.fundingGoal)
      return false;
    c.beneficiary.send(c.amount);
    c.amount = 0;
    return true;
  }
}
```

The contract does not provide the full functionality of a crowdfunding
contract, but it contains the basic concepts necessary to understand structs.
Struct types can be used as value types for mappings and they can itself
contain mappings (even the struct itself can be the value type of the mapping, although it is not possible to include a struct as is inside of itself). Note how in all the functions, a struct type is assigned to a local variable. This does not copy the struct but only store a reference so that assignments to members of the local variable actually write to the state.

## Interfacing with other Contracts

There are two ways to interface with other contracts: Either call a method of a contract whose address is known or create a new contract. Both uses are shown in the example below. Note that (obviously) the source code of a contract to be created needs to be known, which means that it has to come before the contract that creates it (and cyclic dependencies are not possible since the bytecode of the new contract is actually contained in the bytecode of the creating contract).

```
contract OwnedToken {
  // TokenCreator is a contract type that is defined below. It is fine to reference it
  // as long as it is not used to create a new contract.
  TokenCreator creator;
  address owner;
  string32 name;
  function OwnedToken(string32 _name) {
    address nameReg = 0x72ba7d8e73fe8eb666ea66babc8116a41bfb10e2;
    nameReg.callstring32string32("register", _name);
    owner = msg.sender;
    // We do an explicit type conversion from `address` to `TokenCreator` and assume that the type of
    // the calling contract is TokenCreator, there is no real way to check.
    creator = TokenCreator(msg.sender);
    name = _name;
  }
  function changeName(string32 newName) {
    // Only the creator can alter the name -- contracts are explicitly convertible to addresses.
    if (msg.sender == address(creator)) name = newName;
  }
  function transfer(address newOwner) {
    // Only the current owner can transfer the token.
    if (msg.sender != owner) return;
    // We also want to ask the creator if the transfer is fine.
    // Note that this calls a function of the contract defined below.
    if (creator.isTokenTransferOK(owner, newOwner))
      owner = newOwner;
  }
}
contract TokenCreator {
  function createToken(string32 name) returns (address tokenAddress) {
    // Create a new Token contract and return its address.
    return address(new OwnedToken(name));
  }
  function changeName(address tokenAddress, string32 name) {
    // We need an explicit type conversion because contract types are not part of the ABI.
    OwnedToken token = OwnedToken(tokenAddress);
    token.changeName(name);
  }
  function isTokenTransferOK(address currentOwner, address newOwner) returns (bool ok) {
    // Check some arbitrary condition.
    address tokenAddress = msg.sender;
    return (sha3(hash160(newOwner)) & 0xff) == (hash160(tokenAddress) & 0xff);
  }
}
```

## Constructor Arguments

A Solidity contract expects constructor arguments after the end of the contract data itself.
This means that you pass the arguments to a contract by putting them after the
compiled bytes as returned by the compiler in the usual ABI format.

## Esoteric Features

There are some types in Solidity's type system that have no counterpart in the syntax. One of these types are the types of functions. But still, using `var` it is possible to have local variables of these types:

```
contract FunctionSelector {
  function select(bool useB, uint x) returns (uint z) {
    var f = a;
    if (useB) f = b;
    return f(x);
  }
  function a(uint x) returns (uint z) {
    return x * x;
  }
  function b(uint x) returns (uint z) {
    return 2 * x;
  }
}
```

Calling `select(false, x)` will compute `x * x` and `select(true, x)` will compute `2 * x`.
