Solidity is a high-level language whose syntax is similar to that of JavaScript and it is designed to compile to code for the Ethereum Virtual Machine. This
tutorial starts with a basic introduction to Solidity and assumes some knowledge of
the Ethereum Virtual Machine and programming in general. It tries to explain all features present in the language but does not cover features like
the [natural language specification](Ethereum-Natural-Specification-Format)
or formal verification and is also not meant as a final specification
of the language.

You can start using [Solidity in your browser](http://chriseth.github.io/cpp-ethereum),
with no need to download or compile anything. This application only supports
compilation - if you want to run the code or inject it into the blockchain, you
have to use a client like [Geth](https://github.com/ethereum/go-ethereum) or [AlethZero](https://github.com/ethereum/alethzero).

**Quick links:**

- [Cheat Sheet](#cheatsheet)
- [Tips and Tricks](#tips-and-tricks)

**Table of contents**

<!-- TOC depth:6 withLinks:1 updateOnSave:1 -->
- [Some Examples](#some-examples)
	- [Storage](#storage)
	- [Subcurrency Example](#subcurrency-example)
- [Layout of a Solidity Source File](#layout-of-a-solidity-source-file)
- [Structure of a Solidity Contract](#structure-of-a-solidity-contract)
- [Types](#types)
	- [Elementary Types (Value Types)](#elementary-types-value-types)
	- [Operators Involving LValues](#operators-involving-lvalues)
	- [Conversions between Elementary Types](#conversions-between-elementary-types)
		- [Implicit Conversions](#implicit-conversions)
		- [Explicit Conversions](#explicit-conversions)
		- [Type Deduction](#type-deduction)
	- [Functions on addresses](#functions-on-addresses)
	- [Enums](#enums)
	- [Reference Types](#reference-types)
		- [Data location](#data-location)
		- [Arrays](#arrays)
		- [Structs](#structs)
- [Syntactic Sugar and Globally Available Variables](#syntactic-sugar-and-globally-available-variables)
	- [Ether and Time Units](#ether-and-time-units)
	- [Special Variables and Functions](#special-variables-and-functions)
		- [Block and Transaction Properties](#block-and-transaction-properties)
		- [Cryptographic Functions](#cryptographic-functions)
		- [Contract Related](#contract-related)
- [Control Structures](#control-structures)
	- [Function Calls](#function-calls)
		- [Internal Function Calls](#internal-function-calls)
		- [External Function Calls](#external-function-calls)
		- [Named and Optional Function Parameters](#named-and-optional-function-parameters)
	- [Order of Evaluation of Expressions](#order-of-evaluation-of-expressions)
	- [Assignment](#assignment)
	- [Exceptions](#exceptions)
- [Contracts](#contracts)
	- [Interfacing with other Contracts](#interfacing-with-other-contracts)
	- [Libraries](#libraries)
	- [Constructor Arguments](#constructor-arguments)
	- [Contract Inheritance](#contract-inheritance)
		- [Arguments for Base Constructors](#arguments-for-base-constructors)
		- [Multiple Inheritance and Linearization](#multiple-inheritance-and-linearization)
	- [Abstract Contracts](#abstract-contracts)
	- [Visibility Specifiers](#visibility-specifiers)
	- [Accessor Functions](#accessor-functions)
	- [Fallback Functions](#fallback-functions)
	- [Function Modifiers](#function-modifiers)
	- [Constants](#constants)
	- [Events](#events)
		- [Additional Resources for Understanding Events:](#additional-resources-for-understanding-events)
- [Miscellaneous](#miscellaneous)
	- [Layout of State Variables in Storage](#layout-of-state-variables-in-storage)
	- [Esoteric Features](#esoteric-features)
	- [Internals - the Optimizer](#internals-the-optimizer)
	- [Using the Commandline Compiler](#using-the-commandline-compiler)
	- [Tips and Tricks](#tips-and-tricks)
	- [Pitfalls](#pitfalls)
	- [Cheatsheet](#cheatsheet)
		- [Global Variables](#global-variables)
		- [Function Visibility Specifiers](#function-visibility-specifiers)
		- [Modifiers](#modifiers)
		- [Types](#types)
<!-- /TOC -->

# Some Examples

Let us begin with some examples. It is fine if you do not understand everything
right now, we will go into more detail later.

## Storage

```js
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

```js
contract Coin {
    address minter;
    mapping (address => uint) balances;

    event Send(address from, address to, uint value);

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
        Send(msg.sender, receiver, amount);
    }
    function queryBalance(address addr) constant returns (uint balance) {
        return balances[addr];
    }
}
```

This contract introduces some new concepts. One of them is the `address` type,
which is a 160 bit value that does not allow any arithmetic operations.
Furthermore, the state variable `balances` is of a complex datatype that maps
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
variable. So to return the balance, we could also just use `balance =
balances[addr];` without any return statement.
Events like `Send` allow external clients to search the blockchain more efficiently.
If an event is invoked like in the function `send`, this fact is permanently stored in the blockchain, but more on this later.

# Layout of a Solidity Source File

A Solidity source file can contain an arbitrary number of contracts.
**Other source files** can be referenced using `import "filename";` and the symbols
defined there will also be available in the current source file. Note that
the browser-based compiler does not support multiple files and if you are using
the [commandline compiler](#using-the-commandline-compiler), you have to explicitly specify all files you will use
as arguments, the compiler will not search your filesystem on its own.

**Comments** Single-line comments (`//`) and multi-line comments (`/*...*/`) are possible, while
triple-slash comments (`///`) right in front of function declarations introduce
[NatSpec](Ethereum-Natural-Specification-Format) (which are not covered here).


# Structure of a Solidity Contract

In Solidity, contracts are what classes are in object-oriented languages.
They can contain so-called state variables, which are permanently stored
in storage together with the contract and functions, the entry pointers
into operating on these state variables. Apart from state variables, there are
also local variables declared inside functions, whose content is cleared
as soon as control flow returns from the function.

# Types

Solidity is a statically typed language, which means that the type of each
variable (state and local) needs to be specified (or at least known - see
[Type Deduction](#type-deduction) below) at
compile-time. Solidity provides several elementary types which can be combined
to complex types.

## Elementary Types (Value Types)

The following types are also called value types because variables of these
types will always be passed by value, i.e. they are always copied when they
are used as function arguments or in assignments.

`bool`: Booleans, constants `true`, `false`, operators: `!` (logical negation) `&&` (logical conjunction, "and"), `||` (logical disjunction, "or"), `==` (equality) and `!=` (inequality).

The operators `||` and `&&` apply the common short-circuiting rules. This means that in the expression `f(x) || g(y)`, if `f(x)` evaluates to `true`, `g(y)` will not be evaluated even if it may have side-effects.

`int•` / `uint•`: Signed and unsigned integers of various sizes. Keywords `uint8` to `uint256` in steps of `8` (unsigned of 8 up to 256 bits) and `int8` to `int256`. `uint` and `int` are aliases for `uint256` and `int256`, respectively.

Operators:  
Comparisons: `<=`, `<`, `==`, `!=`, `>=`, `>` (evaluate to `bool`)  
Bit operators: `&`, `|`, `^` (bitwise exclusive or), `~` (bitwise negation)  
Arithmetic operators: `+`, `-`, unary `-`, unary `+`, `*`, `/`, `%` (remainder), `**` (exponentiation)

`address`: Holds a 20 byte value (size of an Ethereum address). Operators: `<=`, `<`, `==`, `!=`, `>=` and `>`. Address types also have members (see [Functions on addresses](#functions-on-addresses)) and serve as base for all contracts.

`byte`, `bytes1`, ..., `bytes32`: Fixed-size byte arrays, `byte` is an alias for `bytes1`.

Operators:  
Comparisons: `<=`, `<`, `==`, `!=`, `>=`, `>` (evaluate to `bool`)  
Bit operators: `&`, `|`, `^` (bitwise exclusive or), `~` (bitwise negation)

`bytes`: Dynamically-sized byte array, see [arrays](#arrays). Not a value-type!

`string`: Dynamically-sized UTF8-encoded string, see [arrays](#arrays). Not a value-type!

__Integer Literals__: Integer literals are arbitrary precision integers until they are used together with a non-literal. In `var x = 1 - 2;`, for example, the value of `1 - 2` is `-1`, which is assigned to `x` and thus `x` receives the type `int8` -- the smallest type that contains `-1`, although the natural types of `1` and `2` are actually `uint8`.  
It is even possible to temporarily exceed the maximum of 256 bits as long as only integer literals are used for the computation: `var x = (0xffffffffffffffffffff * 0xffffffffffffffffffff) * 0;` Here, `x` will have the value `0` and thus the type `uint8`.

__String Literals__: String literals are written with double quotes (`"abc"`). As with integer literals, their type can vary, but they are implicitly convertible to `bytes•` if they fit, to `bytes` and to `string`.

## Operators Involving LValues

If `a` is an LValue (i.e. a variable or something that can be assigned to), the following operators are available as shorthands:

`a += e` is equivalent to `a = a + e`. The operators `-=`, `*=`, `/=`, `%=`, `a |=`, `&=` and `^=` are defined accordingly. `a++` and `a--` are equivalent to `a += 1` / `a -= 1` but the expression itself still has the previous value of `a`. In contrast, `--a` and `++a` have the same effect on `a` but return the value after the change.

**delete**
`delete a` assigns the initial value for the type to `a`. I.e. for integers it is equivalent to `a = 0`, but it can also be used on arrays, where it assigns a dynamic array of length zero or a static array of the same length with all elements reset. For structs, it assigns a struct with all members reset.

`delete` has no effect on whole mappings (as the keys of mappings may be arbitrary and are generally unknown). So if you delete a struct, it will reset all members that are not mappings and also recurse into the members unless they are mappings. However, individual keys and what they map to can be deleted.

It is important to note that `delete a` really behaves like an assignment to `a`, i.e. it stores a new object in `a`.
```js
contract DeleteExample {
  uint data;
  uint[] dataArray;
  function f() {
    uint x = data;
    delete x; // sets x to 0, does not affect data
    delete data; // sets data to 0, does not affect x which still holds a copy
    uint[] y = dataArray;
    delete dataArray; // this sets dataArray.length to zero, but as uint[] is a complex object, also
    // y is affected which is an alias to the storage object
    // On the other hand: "delete y" is not valid, as assignments to local variables
    // referencing storage objects can only be made from existing storage objects.
  }
}
```

## Conversions between Elementary Types

### Implicit Conversions

If an operator is applied to different types, the compiler tries to
implicitly convert one of the operands to the type of the other (the same is
true for assignments). In general, an implicit conversion between value-types
is possible if it
makes sense semantically and no information is lost: `uint8` is convertible to
`uint16` and `int128` to `int256`, but `int8` is not convertible to `uint256`
(because `uint256` cannot hold e.g. `-1`).
Furthermore, unsigned integers can be converted to bytes of the same or larger
size, but not vice-versa. Any type that can be converted to `uint160` can also
be converted to `address`.

### Explicit Conversions

If the compiler does not allow implicit conversion but you know what you are
doing, an explicit type conversion is sometimes possible:

```js
int8 y = -3;
uint x = uint(y);
```

At the end of this code snippet, `x` will have the value `0xfffff..fd` (64 hex
characters), which is -3 in two's complement representation of 256 bits.

If a type is explicitly converted to a smaller type, higher-order bits are
cut off:

```js
uint32 a = 0x12345678;
uint16 b = uint16(a); // b will be 0x5678 now
```

### Type Deduction

For convenience, it is not always necessary to explicitly specify the type of a
variable, the compiler automatically infers it from the type of the first
expression that is assigned to the variable:
```js
uint20 x = 0x123;
var y = x;
```
Here, the type of `y` will be `uint20`. Using `var` is not possible for function
parameters or return parameters.

Beware that currently, the type is only deduced from the first assignment, so
the loop in the following snippet is infinite, as `i` will have the type
`uint8` and any value of this type is smaller than `2000`.
```js
for (var i = 0; i < 2000; i++)
{
    // do something
}
```

## Functions on addresses

It is possible to query the balance of an address using the property `balance`
and to send Ether (in units of wei) to an address using the `send` function:

```js
address x = 0x123;
if (x.balance < 10 && address(this).balance >= 10) x.send(10);
```

Beware that if `x` is a contract address, its code will be executed together with the `send` call (this is a limitation of the EVM and cannot be prevented). If that execution runs out of gas or fails in any way, the Ether transfer will be reverted. In this case, `send` returns `false`.

Furthermore, to interface with contracts that do not adhere to the ABI (like the classic NameReg contract),
the function `call` is provided which takes an arbitrary number of arguments of any type. These arguments are ABI-serialized (i.e. also padded to 32 bytes). One exception is the case where the first argument is encoded to exactly four bytes. In this case, it is not padded to allow the use of function signatures here.

```js
address nameReg = 0x72ba7d8e73fe8eb666ea66babc8116a41bfb10e2;
nameReg.call("register", "MyName");
nameReg.call(bytes4(sha3("fun(uint256)")), a);
```

`call` returns a boolean indicating whether the invoked function terminated (`true`) or caused an EVM exception (`false`). It is not possible to access the actual data returned (for this we would need to know the encoding and size in advance).

In a similar way, the function `callcode` can be used: The difference is that only the code of the given address is used, all other aspects (storage, balance, ...) are taken from the current contract. The purpose of `callcode` is to use library code which is stored in another contract. The user has to ensure that the layout of storage in both contracts is suitable for callcode to be used.

Both `call` and `callcode` are very low-level functions and should only be used as a *last resort* as they break the type-safety of Solidity.

Note that contracts inherit all members of address, so it is possible to query the balance of the
current contract using `this.balance`.

## Enums

Enums are one way to create a user-defined type in Solidity. They are explicitly convertible
to and from all integer types but implicit conversion is not allowed.

```js
contract test {
    enum ActionChoices { GoLeft, GoRight, GoStraight, SitStill }
    ActionChoices choice;
    ActionChoices constant defaultChoice = ActionChoices.GoStraight;
    function setGoStraight()
    {
        choice = ActionChoices.GoStraight;
    }
    // Since enum types are not part of the ABI, the signature of "getChoice"
    // will automatically be changed to "getChoice() returns (uint8)"
    // for all matters external to Solidity. The integer type used is just
    // large enough to hold all enum values, i.e. if you have more values,
    // `uint16` will be used and so on.
    function getChoice() returns (ActionChoices)
    {
        return choice;
    }
    function getDefaultChoice() returns (uint)
    {
        return uint(defaultChoice);
    }
}
```

## Reference Types

Complex types, i.e. types which do not always fit into 256 bits have to be handled
more carefully than the value-types we have already seen. Since copying
them can be quite expensive, we have to think about whether we want them to be
stored in memory (which is not persisting) or storage (where the state
variables are held).

### Data location

Every complex type, i.e. *arrays* and *structs*, has an additional
annotation, the "data location", about whether it is stored in memory or in storage. Depending on the
context, there is always a default, but it can be overridden by appending
either `storage` or `memory` to the type. The default for local variables
and function arguments is `memory` and the location is forced
to "storage" for state variables (obviously).

There is also a third data location, "calldata" which is a non-modifyable
non-persistent area whether function arguments are stored. Function parameters
(not return parameters) of external functions are forced to "calldata" and
it behaves mostly like memory.

Data locations are important because they change how assignments behave:
Assignments between storage and memory and also to a state variable (even from other state variables)
always create an independent copy.
Assignments to local storage variables only assign a reference though, and
this reference always points to the state variable even if the latter is changed
in the meantime.
On the other hand, assignments from a memory stored reference type to another
memory-stored reference type does not create a copy.

```js
contract c {
  uint[] x; // the data location of x is storage
  // the data location of memoryArray is memory
  function f(uint[] memoryArray) {
    x = memoryArray; // works, copies the whole array to storage
    var y = x; // works, assigns a pointer, data location of y is storage
    y[7]; // fine, returns the 8th element
    y.length = 2; // fine, modifies x through y
    delete x; // fine, clears the array, also modifies y
    // The following does not work; it would need to create a new temporary /
    // unnamed array in storage, but storage is "statically" allocated:
    // y = memoryArray;
    // This does not work either, since it would "reset" the pointer, but there
    // is no sensible location it could point to.
    // delete y;
	g(x); // calls g, handing over a reference to x
	h(x); // calls h and creates an independent, temporary copy in memory
  }
  function g(uint[] storage storageArray) internal {}
  function h(uint[] memoryArray) {}
}
```

**Summary:**

Forced data location:
 - parameters (not return) of external functions: calldata
 - state variables: storage

Default data location:
 - parameters (also return) of functions: memory
 - all other local variables: storage

### Arrays

Arrays can have a compile-time fixed size or they can be dynamic.
For storage arrays, the element type can be arbitrary (i.e. also other
arrays, mappings or structs). For memory arrays, it cannot be a mapping and
has to be an ABI type if it is an argument of a publicly-visible function.

An array of fixed size `k` and element type `T` is written as `T[k]`,
an array of dynamic size as `T[]`. As an example, an array of 5 dynamic
arrays of `uint` is `uint[][5]` (note that the notation is reversed when
compared to some other languages). To access the second uint in the
third dynamic array, you use `x[2][1]` (indices are zero-based and
access works in the opposite way of the declaration, i.e. `x[2]`
shaves off one level in the type from the right).

Arrays have a `length` member to hold their number of elements.
Dynamic arrays can be resized in storage (not in memory) by changing the
`.length` member. This does not happen automatically when attempting to access elements outside the current length. The size of memory arrays is fixed (but dynamic, i.e. it can depend on runtime parameters) once they are created.

Variables of type `bytes` and `string` are special arrays. A `bytes` is similar to `byte[]`,
but it is packed tightly in calldata. `string` is equal to `bytes` but does not allow
length or index access (for now).

```js
contract ArrayContract {
  uint[2**20] m_aLotOfIntegers;
  // Note that the following is not a pair of arrays but an array of pairs.
  bool[2][] m_pairsOfFlags;
  // newPairs is stored in memory - the default for function arguments
  function setAllFlagPairs(bool[2][] newPairs) {
    // assignment to a storage array replaces the complete array
    m_pairsOfFlags = newPairs;
  }
  function setFlagPair(uint index, bool flagA, bool flagB) {
    // access to a non-existing index will throw an exception
    m_pairsOfFlags[index][0] = flagA;
    m_pairsOfFlags[index][1] = flagB;
  }
  function changeFlagArraySize(uint newSize) {
    // if the new size is smaller, removed array elements will be cleared
    m_pairsOfFlags.length = newSize;
  }
  function clear() {
    // these clear the arrays completely
    delete m_pairsOfFlags;
    delete m_aLotOfIntegers;
    // identical effect here
    m_pairsOfFlags.length = 0;
  }
  bytes m_byteData;
  function byteArrays(bytes data) {
    // byte arrays ("bytes") are different as they are stored without padding,
    // but can be treated identical to "uint8[]"
    m_byteData = data;
    m_byteData.length += 7;
    m_byteData[3] = 8;
    delete m_byteData[2];
  }
}
```

### Structs

Solidity provides a way to define new types in the form of structs, which is
shown in the following example:

```js
contract CrowdFunding {
  // Defines a new type with two fields.
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
    // Creates new struct and saves in storage. We leave out the mapping type.
    campaigns[campaignID] = Campaign(beneficiary, goal, 0, 0);
  }
  function contribute(uint campaignID) {
    Campaign c = campaigns[campaignID];
	// Creates a new temporary memory struct, initialised with the given values
	// and copies it over to storage.
	// Note that you can also use Funder(msg.sender, msg.value) to initialise.
    c.funders[c.numFunders++] = Funder({addr: msg.sender, amount: msg.value});
    c.amount += msg.value;
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
Struct types can be used inside mappings and arrays and they can itself
contain mappings and arrays.

It is not possible for a struct to contain a member of its own type,
although the struct itself can be the value type of a mapping member.
This restriction is necessary, as the size of the struct has to be finite.

Note how in all the functions, a struct type is assigned to a local variable
(of the default storage data location).
This does not copy the struct but only stores a reference so that assignments to
members of the local variable actually write to the state.

Of course, you can also directly access the members of the struct without
assigning it to a local variable, as in
`campaigns[campaignID].amount = 0`.

# Syntactic Sugar and Globally Available Variables

## Ether and Time Units

A literal number can take a suffix of `wei`, `finney`, `szabo` or `ether` to convert between the subdenominations of ether, where Ether currency numbers without a postfix are assumed to be "wei", e.g. `2 ether == 2000 finney` evaluates to `true`.

Furthermore, suffixes of `seconds`, `minutes`, `hours`, `days`, `weeks` and `years` can be used to convert between units of time where seconds are the base unit and units are converted naively (i.e. a year is always exactly 365 days, etc.).

## Special Variables and Functions

There are special variables and functions which always exist in the global
namespace and are mainly used to provide information about the blockchain.

### Block and Transaction Properties

 - `block.coinbase` (`address`): current block miner's address
 - `block.difficulty` (`uint`): current block difficulty
 - `block.gaslimit` (`uint`): current block gaslimit
 - `block.number` (`uint`): current block number
 - `block.blockhash` (`function(uint) returns (bytes32)`): hash of the given block
 - `block.timestamp` (`uint`): current block timestamp
 - `msg.data` (`bytes`): complete calldata
 - `msg.gas` (`uint`): remaining gas
 - `msg.sender` (`address`): sender of the message (current call)
 - `msg.sig` (`bytes4`): first four bytes of the calldata (i.e. function identifier)
 - `msg.value` (`uint`): number of wei sent with the message
 - `now` (`uint`): current block timestamp (alias for `block.timestamp`)
 - `tx.gasprice` (`uint`): gas price of the transaction
 - `tx.origin` (`address`): sender of the transaction (full call chain)

### Cryptographic Functions

 - `sha3(...) returns (bytes32)`: compute the Ethereum-SHA-3 hash of the (tightly packed) arguments
 - `sha256(...) returns (bytes32)`: compute the SHA-256 hash of the (tightly packed) arguments
 - `ripemd160(...) returns (bytes20)`: compute RIPEMD-160 hash of the (tightly packed) arguments
 - `ecrecover(bytes32, byte, bytes32, bytes32) returns (address)`: recover public key from elliptic curve signature - arguments are (data, v, r, s)

In the above, "tightly packed" means that the arguments are concatenated without padding, i.e.
`sha3("ab", "c") == sha3("abc") == sha3(0x616263) == sha3(6382179) = sha3(97, 98, 99)`. If padding is needed, explicit type conversions can be used.

It might be that you run into Out-of-Gas for `sha256`, `ripemd160` or `ecrecover` on a *private blockchain*. The reason for this is that those are implemented as so-called precompiled contracts and these contracts only really exist after they received the first message (although their contract code is hardcoded). Messages to non-existing contracts are more expensive and thus the execution runs into an Out-of-Gas error. A workaround for this problem is to first send e.g. 1 Wei to each of the contracts before you use them in your actual contracts. This is not an issue on the official or test net.

### Contract Related

 - `this` (current contract's type): the current contract, explicitly convertible to `address`
 - `suicide(address)`: suicide the current contract, sending its funds to the given address

Furthermore, all functions of the current contract are callable directly including the current function.

# Control Structures

Most of the control structures from C/JavaScript are available in Solidity
except for `switch` and `goto`. So
there is: `if`, `else`, `while`, `for`, `break`, `continue`, `return`, with
the usual semantics known from C / JavaScript.

Parentheses can *not* be omitted for conditionals, but curly brances can be omitted
around single-statement bodies.

Note that there is no type conversion from non-boolean to boolean types as
there is in C and JavaScript, so `if (1) { ... }` is _not_ valid Solidity.

## Function Calls

### Internal Function Calls

Functions of the current contract can be called directly ("internally"), also recursively, as seen in
this nonsensical example:

```js
contract c {
  function g(uint a) returns (uint ret) { return f(); }
  function f() returns (uint ret) { return g(7) + f(); }
}
```

These function calls are translated into simple jumps inside the EVM. This has
the effect that the current memory is not cleared, i.e. passing memory references
to internally-called functions is very efficient. Only functions of the same
contract can be called internally.

### External Function Calls

The expression `this.g(8);` is also a valid function call, but this time, the function
will be called "externally", via a message call and not directly via jumps.
Functions of other contracts have to be called externally. For an external call,
all function arguments have to be copied to memory.

When calling functions
of other contracts, the amount of Wei sent with the call and the gas can be specified:
```js
contract InfoFeed {
  function info() returns (uint ret) { return 42; }
}
contract Consumer {
  InfoFeed feed;
  function setFeed(address addr) { feed = InfoFeed(addr); }
  function callFeed() { feed.info.value(10).gas(800)(); }
}
```
Note that the expression `InfoFeed(addr)` performs an explicit type conversion stating
that "we know that the type of the contract at the given address is `InfoFeed`" and
this does not execute a constructor. We could also have used `function setFeed(InfoFeed _feed) { feed = _feed; }` directly.  Be careful about the fact that `feed.info.value(10).gas(800)`
only (locally) sets the value and amount of gas sent with the function call and only the
parentheses at the end perform the actual call.

### Named Calls and Anonymous Function Parameters

Function call arguments can also be given by name, in any order, and the names
of unused parameters (especially return parameters) can be omitted.

```js
contract c {
  function f(uint key, uint value) { ... }
  function g() {
    // named arguments
    f({value: 2, key: 3});
  }
  // omitted parameters
  function func(uint k, uint) returns(uint) {
    return k;
  }
}
```

## Order of Evaluation of Expressions

The evaluation order of expressions is not specified (more formally, the order
in which the children of one node in the expression tree are evaluated is not
specified, but they are of course evaluated before the node itself). It is only
guaranteed that statements are executed in order and short-circuiting for
boolean expressions is done.

## Assignment

The semantics of assignment are a bit more complicated for non-value types like arrays and structs.
Assigning *to* a state variable always creates an independent copy. On the other hand, assigning to a local variable creates an independent copy only for elementary types, i.e. static types that fit into 32 bytes. If structs or arrays (including `bytes` and `string`) are assigned from a state variable to a local variable, the local variable holds a reference to the original state variable. A second assignment to the local variable does not modify the state but only changes the reference. Assignments to members (or elements) of the local variable *do* change the state.

## Exceptions

Currently, there are two situations, where exceptions can happen in Solidity:

1. If you access an array beyond its length (i.e. `x[i]` where `i >= x.length`)
2. If a function called via a message call does not finish properly (i.e. it runs out of gas or throws an exception itself).

In such cases, Solidity will trigger an "invalid jump" and thus cause the EVM to revert all changes made to the state. The reason for this is that there is no safe way to continue execution, because an expected effect did not occur. Because we want to retain the atomicity of transactions, the safest thing to do is to revert all changes and make the whole transaction (or at least call) without effect.

It is planned to also throw and catch exceptions manually.

# Contracts

## Interfacing with other Contracts

There are two ways to interface with other contracts: Either call a method of a contract whose address is known or create a new contract. Both uses are shown in the example below. Note that (obviously) the source code of a contract to be created needs to be known, which means that it has to come before the contract that creates it (and cyclic dependencies are not possible since the bytecode of the new contract is actually contained in the bytecode of the creating contract).

```js
contract OwnedToken {
  // TokenCreator is a contract type that is defined below. It is fine to reference it
  // as long as it is not used to create a new contract.
  TokenCreator creator;
  address owner;
  bytes32 name;
  function OwnedToken(bytes32 _name) {
    address nameReg = 0x72ba7d8e73fe8eb666ea66babc8116a41bfb10e2;
    nameReg.call("register", _name); // This is an unsafe raw call to another contract.
    owner = msg.sender;
    // We do an explicit type conversion from `address` to `TokenCreator` and assume that the type of
    // the calling contract is TokenCreator, there is no real way to check.
    creator = TokenCreator(msg.sender);
    name = _name;
  }
  function changeName(bytes32 newName) {
    // Only the creator can alter the name -- contracts are explicitly convertible to addresses.
    if (msg.sender == address(creator)) name = newName;
  }
  function transfer(address newOwner) {
    // Only the current owner can transfer the token.
    if (msg.sender != owner) return;
    // We also want to ask the creator if the transfer is fine.
    // Note that this calls a function of the contract defined below.
    // If the call fails (e.g. due to out-of-gas), the execution here stops
    // immediately (the ability to catch this will be added later).
    if (creator.isTokenTransferOK(owner, newOwner))
      owner = newOwner;
  }
}
contract TokenCreator {
  function createToken(bytes32 name) returns (OwnedToken tokenAddress) {
    // Create a new Token contract and return its address.
    // From the JavaScript side, the return type is simply "address", as this is the closest
    // type available in the ABI.
    // To get the address, this can only be called by another contract (not eth_call).
    return new OwnedToken(name);
  }
  function changeName(OwnedToken tokenAddress, bytes32 name) {
    // Again, the external type of "tokenAddress" is simply "address".
    tokenAddress.changeName(name);
  }
  function isTokenTransferOK(address currentOwner, address newOwner) returns (bool ok) {
    // Check some arbitrary condition.
    address tokenAddress = msg.sender;
    return (sha3(newOwner) & 0xff) == (bytes20(tokenAddress) & 0xff);
  }
}
```

## Libraries

Libraries are similar to contracts, but their purpose is that they are deployed only once at a specific address and their code is reused using the `CALLCODE` feature of the EVM. This means that if library functions are called, their code is executed in the context of the calling contract, i.e. `this` points to the calling contract and especially the storage from the calling contract can be accessed (this is not yet possible from solidity).

The following example illustrates how to use libraries. Note that the library given below is not a good example for a library, since the benefits of a library in terms of saving gas for code deployment are only visible starting from a certain size.
```
library Math {
  function max(uint a, uint b) returns (uint) {
    if (a > b) return a;
    else return b;
  }
  function min(uint a, uint b) returns (uint) {
    if (a < b) return a;
    else return b;
  }
}
contract C {
  function register(uint value) {
    // The library functions can be called without a specific instance of the library,
    // since the "instance" will be the current contract.
    value = Math.max(10, Math.min(100, value)); // clamp value to [10, 100]
    // ...
  }
}
```

The calls to `Math.max` and `Math.min` are both compiled as calls (`CALLCODE`s) to an external contract. If you use libraries, take care that an actual external function call is performed, so `msg.sender` does not point to the original sender anymore but to the the calling contract and also `msg.value` contains the funds sent during the call to the library function.

As the compiler cannot know where the library will be deployed at, these addresses have to be filled into the final bytecode by a linker (see [Using the Commandline Compiler](#using-the-commandline-compiler) on how to use the commandline compiler for linking). If the addresses are not given as arguments to the compiler, the compiled hex code will contain placeholders of the form `__Math______` (where `Math` is the name of the library). The address can be filled manually by replacing all those 40 symbols by the hex encoding of the address of the library contract.

Restrictions for libraries in comparison to contracts:

 - no state variables
 - cannot inherit nor be inherited

(these might be lifted at a later point)

## Constructor Arguments

A Solidity contract expects constructor arguments after the end of the contract data itself.
This means that you pass the arguments to a contract by putting them after the
compiled bytes as returned by the compiler in the usual ABI format.

If you use web3.js's `MyContract.new()`, you do not have to care about this, though.

## Contract Inheritance

Solidity supports multiple inheritance by copying code including polymorphism.
Details are given in the following example.

```js
contract owned {
    function owned() { owner = msg.sender; }
    address owner;
}

// Use "is" to derive from another contract. Derived contracts can access all non-private members
// including internal functions and state variables. These cannot be accessed externally via
// `this`, though.
contract mortal is owned {
    function kill() { if (msg.sender == owner) suicide(owner); }
}

// These are only provided to make the interface known to the compiler.
// Note the bodiless functions. If a contract does not implement all functions
// it can only be used as an interface.
contract Config { function lookup(uint id) returns (address adr); }
contract NameReg { function register(bytes32 name); function unregister(); }

// Multiple inheritance is possible. Note that "owned" is also a base class of
// "mortal", yet there is only a single instance of "owned" (as for virtual
// inheritance in C++).
contract named is owned, mortal {
    function named(bytes32 name) {
        address ConfigAddress = 0xd5f9d8d94886e70b06e474c3fb14fd43e2f23970;
        NameReg(Config(ConfigAddress).lookup(1)).register(name);
    }

// Functions can be overridden, both local and message-based function calls take
// these overrides into account.
    function kill() {
        if (msg.sender == owner) {
            address ConfigAddress = 0xd5f9d8d94886e70b06e474c3fb14fd43e2f23970;
            NameReg(Config(ConfigAddress).lookup(1)).unregister();
// It is still possible to call a specific overridden function.
            mortal.kill();
        }
    }
}

// If a constructor takes an argument, it needs to be provided in the header (or modifier-invocation-style at the constructor of the derived contract (see below)).
contract PriceFeed is owned, mortal, named("GoldFeed") {
   function updateInfo(uint newInfo) {
      if (msg.sender == owner) info = newInfo;
   }

   function get() constant returns(uint r) { return info; }

   uint info;
}
```

Note that above, we call `mortal.kill()` to "forward" the destruction request. The way this is done
is problematic, as seen in the following example:
```js
contract mortal is owned {
    function kill() { if (msg.sender == owner) suicide(owner); }
}
contract Base1 is mortal {
    function kill() { /* do cleanup 1 */ mortal.kill(); }
}
contract Base2 is mortal {
    function kill() { /* do cleanup 2 */ mortal.kill(); }
}
contract Final is Base1, Base2 {
}
```

A call to `Final.kill()` will call `Base2.kill` as the most derived override, but this
function will bypass `Base1.kill`, basically because it does not even know about `Base1`.
The way around this is to use `super`:
```js
contract mortal is owned {
    function kill() { if (msg.sender == owner) suicide(owner); }
}
contract Base1 is mortal {
    function kill() { /* do cleanup 1 */ super.kill(); }
}
contract Base2 is mortal {
    function kill() { /* do cleanup 2 */ super.kill(); }
}
contract Final is Base2, Base1 {
}
```

If `Base1` calls a function of `super`, it does not simply call this function on one of its
base contracts, it rather calls this function on the next base contract in the final
inheritance graph, so it will call `Base2.kill()` (note that the final inheritance sequence is
-- starting with the most derived contract: Final, Base1, Base2, mortal, owned). Note that the actual function that
is called when using super is not known in the context of the class where it is used,
although its type is known. This is similar for ordinary virtual method lookup.

### Arguments for Base Constructors

Derived contracts need to provide all arguments needed for the base constructors. This can be done at two places:

```js
contract Base {
  uint x;
  function Base(uint _x) { x = _x; }
}
contract Derived is Base(7) {
  function Derived(uint _y) Base(_y * _y) {
  }
}
```

Either directly in the inheritance list (`is Base(7)`) or in the way a modifier would be invoked as part of the header of the derived constructor (`Base(_y * _y)`). The first way to do it is more convenient if the constructor argument is a constant and defines the behaviour of the contract or describes it. The second way has to be used if the constructor arguments of the base depend on those of the derived contract. If, as in this silly example, both places are used, the modifier-style argument takes precedence.


### Multiple Inheritance and Linearization

Languages that allow multiple inheritance have to deal with several problems, one of them being the [Diamond Problem](https://en.wikipedia.org/wiki/Multiple_inheritance#The_diamond_problem). Solidity follows the path of Python and uses "[C3 Linearization](https://en.wikipedia.org/wiki/C3_linearization)" to force a specific order in the DAG of base classes. This results in the desirable property of monotonicity but disallows some inheritance graphs. Especially, the order in which the base classes are given in the `is` directive is important. In the following code, Solidity will give the error "Linearization of inheritance graph impossible".
```js
contract X {}
contract A is X {}
contract C is A, X {}
```
The reason for this is that `C` requests `X` to override `A` (by specifying `A, X` in this order), but `A` itself requests to override `X`, which is a contradiction that cannot be resolved.

A simple rule to remember is to specify the base classes in the order from "most base-like" to "most derived".

## Abstract Contracts

Contract functions can lack an implementation as in the following example (note that the function declaration header is terminated by `;`).
```js
contract feline {
  function utterance() returns (bytes32);
}
```
Such contracts cannot be compiled (even if they contain implemented functions alongside non-implemented functions), but they can be used as base contracts:
```js
contract Cat is feline {
  function utterance() returns (bytes32) { return "miaow"; }
}
```
If a contract inherits from an abstract contract and does not implement all non-implemented functions by overriding, it will itself be abstract.

## Visibility Specifiers

Functions and state variables can be specified as being `public`, `internal` or `private`, where the default for functions is `public` and `internal` for state variables. In addition, functions can also be specified as `external`.

`external`: External functions are part of the contract interface and they can be called from other contracts and via transactions. An external function `f` cannot be called internally (i.e. `f()` does not work, but `this.f()` works). Furthermore, all function parameters are immutable.

`public`: Public functions are part of the contract interface and can be either called internally or via messages. For public state variables, an automatic accessor function (see below) is generated.

`internal`: Those functions and state variables can only be accessed internally, i.e. from within the current contract or contracts deriving from it without using `this`.

`private`: Private functions and state variables are only visible for the contract they are defined in and not in derived contracts.

```js
contract c {
  function f(uint a) private returns (uint b) { return a + 1; }
  function setData(uint a) internal { data = a; }
  uint public data;
}
```

Other contracts can call `c.data()` to retrieve the value of data in state storage, but are not able to call `f`. Contracts derived from `c` can call `setData` to alter the value of `data` (but only in their own state).

## Accessor Functions

The compiler automatically creates accessor functions for all public state variables. The contract given below will have a function called `data` that does not take any arguments and returns a uint, the value of the state variable `data`. The initialization of state variables can be done at declaration.

```js
contract test {
     uint public data = 42;
}
```

The next example is a bit more complex:

```js
contract complex {
  struct Data { uint a; bytes3 b; mapping(uint => uint) map; }
  mapping(uint => mapping(bool => Data[])) public data;
}
```

It will generate a function of the following form:
```js
function data(uint arg1, bool arg2, uint arg3) returns (uint a, bytes3 b)
{
  a = data[arg1][arg2][arg3].a;
  b = data[arg1][arg2][arg3].b;
}
```

Note that the mapping in the struct is omitted because there is no good way to provide the key for the mapping.

## Fallback Functions

A contract can have exactly one unnamed
function. This function cannot have arguments and is executed on a call to the contract if
none of the other functions matches the given function identifier (or if no data was supplied at all).

```js
contract Test {
  function() { x = 1; }
  uint x;
}

contract Caller {
  function callTest(address testAddress) {
    Test(testAddress).call(0xabcdef01); // hash does not exist
    // results in Test(testAddress).x becoming == 1.
  }
}
```

## Function Modifiers

Modifiers can be used to easily change the behaviour of functions, for example to automatically check a condition prior to executing the function. They are inheritable properties of contracts and may be overridden by derived contracts.

```js
contract owned {
  function owned() { owner = msg.sender; }
  address owner;

  // This contract only defines a modifier but does not use it - it will
  // be used in derived contracts.
  // The function body is inserted where the special symbol "_" in the
  // definition of a modifier appears.
  modifier onlyowner { if (msg.sender == owner) _ }
}
contract mortal is owned {
  // This contract inherits the "onlyowner"-modifier from "owned" and
  // applies it to the "kill"-function, which causes that calls to "kill"
  // only have an effect if they are made by the stored owner.
  function kill() onlyowner {
    suicide(owner);
  }
}
contract priced {
  // Modifiers can receive arguments:
  modifier costs(uint price) { if (msg.value >= price) _ }
}
contract Register is priced, owned {
  mapping (address => bool) registeredAddresses;
  uint price;
  function Register(uint initialPrice) { price = initialPrice; }
  function register() costs(price) {
    registeredAddresses[msg.sender] = true;
  }
  function changePrice(uint _price) onlyowner {
    price = _price;
  }
}
```

Multiple modifiers can be applied to a function by specifying them in a whitespace-separated list and will be evaluated in order. Explicit returns from a modifier or function body immediately leave the whole function, while control flow reaching the end of a function or modifier body continues after the "_" in the preceding modifier. Arbitrary expressions are allowed for modifier arguments and in this context, all symbols visible from the function are visible in the modifier. Symbols introduced in the modifier are not visible in the function (as they might change by overriding).

## Constants

State variables of value type can be declared as constant.
```js
contract C {
  uint constant x = 32;
  bytes3 constant text = "abc";
}
```

This has the effect that the compiler does not reserve a storage slot
for these variables and every occurrence is replaced by their constant value.

## Events

Events allow the convenient usage of the EVM logging facilities. Events are inheritable members of contracts. When they are called, they cause the arguments to be stored in the transaction's log. Up to three parameters can receive the attribute `indexed` which will cause the respective arguments to be treated as log topics instead of data. The hash of the signature of the event is one of the topics except if you declared the event with `anonymous` specifier. All non-indexed arguments will be stored in the data part of the log. Example:

```js
contract ClientReceipt {
  event Deposit(address indexed _from, bytes32 indexed _id, uint _value);
  function deposit(bytes32 _id) {
    Deposit(msg.sender, _id, msg.value);
  }
}
```
Here, the call to `Deposit` will behave identical to
`log3(msg.value, 0x50cb9fe53daa9737b786ab3646f04d0150dc50ef4e75f59509d83667ad5adb20, sha3(msg.sender), _id);`. Note that the large hex number is equal to the sha3-hash of "Deposit(address,bytes32,uint256)", the event's signature.

### Additional Resources for Understanding Events:

- Javascript documentation: <https://github.com/ethereum/wiki/wiki/JavaScript-API#contract-events>
- Example usage of events: <https://github.com/debris/smart-exchange/blob/master/lib/contracts/SmartExchange.sol>
- How to access them in js: <https://github.com/debris/smart-exchange/blob/master/lib/exchange_transactions.js>

# Miscellaneous

## Layout of State Variables in Storage

Statically-sized variables (everything except mapping and dynamically-sized array types) are laid out contiguously in storage starting from position `0`. Multiple items that need less than 32 bytes are packed into a single storage slot if possible, according to the following rules:

- The first item in a storage slot is stored lower-order aligned.
- Elementary types use only that many bytes that are necessary to store them.
- If an elementary type does not fit the remaining part of a storage slot, it is moved to the next storage slot.
- Structs and array data always start a new slot and occupy whole slots (but items inside a struct or array are packed tightly according to these rules).

The elements of structs and arrays are stored after each other, just as if they were given explicitly.

Due to their unpredictable size, mapping and dynamically-sized array types use a `sha3`
computation to find the starting position of the value or the array data. These starting positions are always full stack slots.

The mapping or the dynamic array itself
occupies an (unfilled) slot in storage at some position `p` according to the above rule (or by
recursively applying this rule for mappings to mappings or arrays of arrays). For a dynamic array, this slot stores the number of elements in the array. For a mapping, the slot is unused (but it is needed so that two equal mappings after each other will use a different hash distribution).
Array data is located at `sha3(p)` and the value corresponding to a mapping key
`k` is located at `sha3(k . p)` where `.` is concatenation. If the value is again a
non-elementary type, the positions are found by adding an offset of `sha3(k . p)`.

So for the following contract snippet:
```js
contract c {
  struct S { uint a; uint b; }
  uint x;
  mapping(uint => mapping(uint => S)) data;
}
```
The position of `data[4][9].b` is at `sha3(uint256(9) . sha3(uint256(4) . uint(256(1))) + 1`.

## Esoteric Features

There are some types in Solidity's type system that have no counterpart in the syntax. One of these types are the types of functions. But still, using `var` it is possible to have local variables of these types:

```js
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


## Internals - the Optimizer

The Solidity optimizer operates on assembly, so it can be and also is used by other languages. It splits the sequence of instructions into basic blocks at JUMPs and JUMPDESTs. Inside these blocks, the instructions are analysed and every modification to the stack, to memory or storage is recorded as an expression which consists of an instruction and a list of arguments which are essentially pointers to other expressions. The main idea is now to find expressions that are always equal (on every input) and combine them into an expression class. The optimizer first tries to find each new expression in a list of already known expressions. If this does not work, the expression is simplified according to rules like `constant` + `constant` = `sum_of_constants` or `X` * 1 = `X`. Since this is done recursively, we can also apply the latter rule if the second factor is a more complex expression where we know that it will always evaluate to one. Modifications to storage and memory locations have to erase knowledge about storage and memory locations which are not known to be different: If we first write to location x and then to location y and both are input variables, the second could overwrite the first, so we actually do not know what is stored at x after we wrote to y. On the other hand, if a simplification of the expression x - y evaluates to a non-zero constant, we know that we can keep our knowledge about what is stored at x.

At the end of this process, we know which expressions have to be on the stack in the end and have a list of modifications to memory and storage. This information is stored together with the basic blocks and is used to link them. Furthermore, knowledge about the stack, storage and memory configuration is forwarded to the next block(s). If we know the targets of all JUMP and JUMPI instructions, we can build a complete control flow graph of the program. If there is only one target we do not know (this can happen as in principle, jump targets can be computed from inputs), we have to erase all knowledge about the input state of a block as it can be the target of the unknown JUMP. If a JUMPI is found whose condition evaluates to a constant, it is transformed to an unconditional jump.

As the last step, the code in each block is completely re-generated. A dependency graph is created from the expressions on the stack at the end of the block and every operation that is not part of this graph is essentially dropped. Now code is generated that applies the modifications to memory and storage in the order they were made in the original code (dropping modifications which were found not to be needed) and finally, generates all values that are required to be on the stack in the correct place.

These steps are applied to each basic block and the newly generated code is used as replacement if it is smaller. If a basic block is split at a JUMPI and during the analysis, the condition evaluates to a constant, the JUMPI is replaced depending on the value of the constant, and thus code like
```js
var x = 7;
data[7] = 9;
if (data[x] != x + 2)
  return 2;
else
  return 1;
```
is simplified to code which can also be compiled from
```js
data[7] = 9;
return 1;
```
even though the instructions contained a jump in the beginning.

## Using the Commandline Compiler

One of the build targets of the Solidity repository is `solc`, the solidity commandline compiler.



TODO, also speak about "import".

## Tips and Tricks

 * Use `delete` on arrays to delete all its elements.
 * Use shorter types for struct elements and sort them such that short types are grouped together. This can lower the gas costs as multiple SSTORE operations might be combined into a single (SSTORE costs 5000 or 20000 gas, so this is what you want to optimise). Use the gas price estimator (with optimiser enabled) to check!
 * Make your state variables public - the compiler will create [getters](#accessor-functions) for you for free.
 * If you end up checking conditions on input or state a lot at the beginning of your functions, try using [modifiers](#function-modifiers)
 * If your contract has a function called `send` but you want to use the built-in send-function, use `address(contractVariable).send(amount)`.
 * If you want your contracts to receive ether when called via `send`, you have to implement the [fallback function](#fallback-functions).
 * Initialise storage structs with a single assignment: `x = MyStruct({a: 1, b: 2});`

## Pitfalls

Unfortunately, there are some subtleties the compiler does not yet warn you about.

 - If you use `StructName x` or `uint[] x` as a local variable, it **has** to be assigned from a state variable,
   otherwise it behaves like a "null pointer" to storage, so you cannot use it on its own.
   Please read about [data locations](#data-location). The most common solution is to use `StructName memory x` or `uint[] memory x`.

## Cheatsheet

### Global Variables

 - `block.coinbase` (`address`): current block miner's address
 - `block.difficulty` (`uint`): current block difficulty
 - `block.gaslimit` (`uint`): current block gaslimit
 - `block.number` (`uint`): current block number
 - `block.blockhash` (`function(uint) returns (bytes32)`): hash of the given block
 - `block.timestamp` (`uint`): current block timestamp
 - `msg.data` (`bytes`): complete calldata
 - `msg.gas` (`uint`): remaining gas
 - `msg.sender` (`address`): sender of the message (current call)
 - `msg.value` (`uint`): number of wei sent with the message
 - `now` (`uint`): current block timestamp (alias for `block.timestamp`)
 - `tx.gasprice` (`uint`): gas price of the transaction
 - `tx.origin` (`address`): sender of the transaction (full call chain)
 - `sha3(...) returns (bytes32)`: compute the Ethereum-SHA3 hash of the (tightly packed) arguments
 - `sha256(...) returns (bytes32)`: compute the SHA256 hash of the (tightly packed) arguments
 - `ripemd160(...) returns (bytes20)`: compute RIPEMD of 256 the (tightly packed) arguments
 - `ecrecover(bytes32, byte, bytes32, bytes32) returns (address)`: recover public key from elliptic curve signature
 - `this` (current contract's type): the current contract, explicitly convertible to `address`
 - `super`: the contract one level higher in the inheritance hierarchy
 - `suicide(address)`: suicide the current contract, sending its funds to the given address
 - `<address>.balance`: balance of the address in Wei
 - `<address>.send(uint256) returns (bool)`: send given amount of Wei to address, returns `false` on failure.

### Function Visibility Specifiers

```js
function myFunction() <visibility specifier> returns (bool) {
    return true;
}
```

 - `public`: visible externally and internally (creates accessor function for storage/state variables)
 - `private`: only visible in the current contract
 - `external`: only visible externally (only for functions) - i.e. can only be message-called (via `this.fun`)
 - `internal`: only visible internally

### Modifiers

 - `constant` for state variables: Disallows assignment (except initialisation), does not occupy storage slot.
 - `constant` for functions: Disallows modification of state - this is not enforced yet.
 - `anonymous` for events: Does not store event signature as topic.
 - `indexed` for event parameters: Stores the parameter as topic.

### Types

TODO