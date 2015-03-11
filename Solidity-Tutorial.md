Solidity is a high-level language whose syntax is similar to that of JavaScript and it is designed to compile to code for the Ethereum Virtual Machine. This
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
variable. So to return the balance, we could also just use `balance =
balances[addr];` without any return statement.

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

## Ether and Time Units

A literal number can take a suffix of `wei`, `finney`, `szabo` or `ether` to convert between the subdenominations of ether, where Ether currency numbers without a postfix are assumed to be "wei", e.g. `2 ether == 2000 finney` evaluates to `true`.

Furthermore, suffixes of `seconds`, `minutes`, `hours`, `days`, `weeks` and `years` can be used to convert between units of time where seconds are the base unit and units are converted naively (i.e. a year is always exactly 356 days, etc.).

## Control Structures

Most of the control structures from C/JavaScript are available in Solidity
except for `switch` (not planned) and `goto` (note that it's called Solidity). So
there is: `if`, `else`, `while`, `for`, `break`, `continue`, `return`. Note that there
is no type conversion from non-boolean to boolean types as there is in C and
JavaScript, so `if (1) { ... }` is _not_ valid Solidity.

## Function Calls

Functions of the current contract can be called directly, also recursively, as seen in
this nonsensical example:

```
contract c {
  function g(uint a) returns (uint ret) { return f(); }
  function f() returns (uint ret) { return g(7) + f(); }
}
```

The expression `this.g(8);` is also a valid function call, but this time, the function
will be called via a message call and not directly via jumps. When calling functions
of other contracts, the amount of Wei sent with the call and the gas can be specified:
```
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
this does not execute a constructor. Be careful in that `feed.info.value(10).gas(800)`
only (locally) set the value and amount of gas sent with the function call and only the
parentheses at the end perform the actual call.

Function call arguments can also be given by name, in any order:
```
contract c {
function f(uint key, uint value) { ... }
function g() {
  f({value: 2, key: 3});
}
}
```
The names for function parameters and return parameters are optional.
```
contract test {
  function func(uint k, uint) returns(uint){
    return k;
  }
}
```

## Special Variables and Functions

There are special variables and functions which always exist in the global
namespace.

### Block and Transaction Properties

 - `block.coinbase` (`address`): current block miner's address
 - `block.difficulty` (`uint`): current block difficulty
 - `block.gaslimit` (`uint`): current block gaslimit
 - `block.number` (`uint`): current block number
 - `block.blockhash` (`function(uint) returns (hash)`): hash of the given block
 - `block.timestamp` (`uint`): current block timestamp
 - `msg.data` (`bytes`): complete calldata
 - `msg.gas` (`uint`): remaining gas
 - `msg.sender` (`address`): sender of the message (current call)
 - `msg.value` (`uint`): number of wei sent with the message
 - `tx.gasprice` (`uint`): gas price of the transaction
 - `tx.origin` (`address`): sender of the transaction (full call chain)

### Cryptographic Functions

 - `sha3(...) returns (hash)`: compute the SHA3 hash of the (tightly packed) arguments
 - `sha256(...) returns (hash)`: compute the SHA256 hash of the (tightly packed) arguments
 - `ripemd160(...) returns (hash160)`: compute RIPEMD of 256 the (tightly packed) arguments
 - `ecrecover(hash, hash8, hash, hash) returns (address)`: recover public key from elliptic curve signature

In the above, "tightly packed" means that the arguments are concatenated without padding, i.e.
`sha3("ab", "c") == sha3("abc") == sha3(0x616263) == sha3(6382179) = sha3(97, 98, 99)`. If padding is needed, explicit type conversions can be used.

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

Furthermore, to interface with contracts that do not adhere to the ABI (like the classic NameReg contract),
the function `call` is provided which takes an arbitrary number of arguments of any type. These arguments are ABI-serialized (i.e. also padded to 32 bytes). One exception is the case where the first argument is encoded to exactly four bytes. In this case, it is not padded to allow the use of function signatures here.

```
address nameReg = 0x72ba7d8e73fe8eb666ea66babc8116a41bfb10e2;
nameReg.call("register", "MyName");
nameReg.call(string4(string32(sha3("fun(uint256)"))), a);
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

Both variably and fixed size arrays are supported in storage and as parameters of external
functions:

```
contract ArrayContract {
  uint[2**20] m_aLotOfIntegers;
  bool[2][] m_pairsOfFlags;
  function setAllFlagPairs(bool[2][] newPairs) {
    // assignment to array replaces the complete array
    m_pairsOfFlags = newPairs;
  }
  function setFlagPair(uint index, bool flagA, bool flagB) {
    // access to a non-existing index will stop execution
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
  function byteArrays(bytes data) external {
    // byte arrays ("bytes") are different as they are stored without padding,
    // but can be treated identical to "uint8[]"
    m_byteData = data;
    m_byteData.length += 7;
    m_byteData[3] = 8;
    delete m_byteData[2];
  }
}
```

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

## Enums

Enums are another way to create a user-defined type in Solidity. They are explicitly convertible
to and from all integer types but implicit conversion is not allowed.

```
contract test {
    enum ActionChoices { GoLeft, GoRight, GoStraight, SitStill };
    ActionChoices choices;
    function setGoStraight()
    {
        choices = ActionChoices.GoStraight;
    }
    function getChoice() returns (uint)
    {
        return uint(choices);
    }
}
```

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

## Contract Inheritance

Solidity supports multiple inheritance by copying code including polymorphism.
Details are given in the following example.

```
contract owned {
    function owned() { owner = msg.sender; }
    address owner;
}

// Use "is" to derive from another contract. Derived contracts can access all members
// including private functions and storage variables.
contract mortal is owned {
    function kill() { if (msg.sender == owner) suicide(owner); }
}

// These are only provided to make the interface known to the compiler.
contract Config { function lookup(uint id) returns (address adr) {} }
contract NameReg { function register(string32 name) {} function unregister() {} }

// Multiple inheritance is possible. Note that "owned" is also a base class of
// "mortal", yet there is only a single instance of "owned" (as for virtual
// inheritance in C++).
contract named is owned, mortal {
    function named(string32 name) {
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

// If a constructor takes an argument, it needs to be provided in the header.
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
```
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
```
contract mortal is owned {
    function kill() { if (msg.sender == owner) suicide(owner); }
}
contract Base1 is mortal {
    function kill() { /* do cleanup 1 */ super.kill(); }
}
contract Base2 is mortal {
    function kill() { /* do cleanup 2 */ super.kill(); }
}
contract Final is Base1, Base2 {
}
```

If `Base1` calls a function of `super`, it does not simply call this function on one of its
base contracts, it rather calls this function on the next base contract in the final
inheritance graph, so it will call `Base2.kill()`. Note that the actual function that
is called when using super is not known in the context of the class where it is used,
although its type is known. This is similar for ordinary virtual method lookup.

### Multiple Inheritance and Linearization

Languages that allow multiple inheritance have to deal with several problems, one of them being the [Diamond Problem](https://en.wikipedia.org/wiki/Multiple_inheritance#The_diamond_problem). Solidity follows the path of Python and uses "[C3 Linearization](https://en.wikipedia.org/wiki/C3_linearization)" to force a specific order in the DAG of base classes. This results in the desirable property of monotonicity but disallows some inheritance graphs. Especially, the order in which the base classes are given in the `is` directive is important. In the following code, Solidity will give the error "Linearization of inheritance graph impossible".
```
contract X {}
contract A is X {}
contract C is A, X {}
```
The reason for this is that `C` requests `X` to override `A` (by specifying `A, X` in this order), but `A` itself requests to override `X`, which is a contradiction that cannot be resolved.

A simple rule to remember is to specify the base classes in the order from "most base-like" to "most derived".

## Visibility Specifiers

Functions and storage variables can be specified as being `public`, `inherited` or `private`, where the default for functions is `public` and `inherited` for storage variables. In addition, functions can also be specified as `external`.

External: External functions are part of the contract interface and they can be called from other contracts and via transactions. An external function `f` cannot be called internally (i.e. `f()` does not work, but `this.f()` works). Furthermore, all function parameters are immutable.

Public: Public functions are part of the contract interface and can be either called internally or via messages. For public storage variables, an automatic accessor function (see below) is generated.

Inherited: Those functions and storage variables can only be accessed internally.

Private: Private functions and storage variables are only visible for the contract they are defined in and not in derived contracts.

```
contract c {
  function f(uint a) private returns (uint b) { return a + 1; }
  function setData(uint a) inherited { data = a; }
  uint public data;
}
```

Other contracts can call `c.data()` to retrieve the value of data in storage, but are not able to call `f`. Contracts derived from `c` can call `setData` to alter the value of `data` (but only in their own storage).

## Accessor Functions

The compiler automatically creates accessor functions for all public state variables. The contract given below will have a function called `data` that does not take any arguments and returns a uint, the value of the state variable `data`. The initialization of state variables can be done at declaration.

```
contract test {
     uint public data = 42;
}
```

The next example is a bit more complex:

```
contract complex {
  struct Data { uint a; string3 b; mapping(uint => uint) map; }
  mapping(uint => mapping(bool => Data)) public data;
}
```

It will generate a function of the following form:
```
function data(uint arg1, bool arg2) returns (uint a, string3 b)
{
  a = data[arg1][arg2].a;
  b = data[arg1][arg2].b;
}
```

Note that the mapping in the struct is omitted because there is no good way to provide the key for the mapping.

## Fallback Functions

A contract can have exactly one unnamed
function. This function cannot have arguments and is executed on a call to the contract if
none of the other functions matches the given function identifier (or if no data was supplied at all).

```
contract Test {
  function() { x = 1; }
  uint x;
}

contract Caller {
  function callTest(address testAddress) {
    Test(testAddress).send(0);
    // results in Test(testAddress).x becoming == 1.
  }
}
```

## Function Modifiers

Modifiers can be used to easily change the behaviour of functions, for example to automatically check a condition prior to executing the function. They are inheritable properties of contracts and may be overridden by derived contracts.

```
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

## Events

Events allow the convenient usage of the EVM logging facilities. Events are inheritable members of contracts. When they are called, they cause
the arguments to be stored in the transaction's log. Up to three parameters can receive the
attribute `indexed` which will cause the respective arguments to be treated as log topics instead
of data. The hash of the signature of the event is always one of the topics. All non-indexed
arguments will be stored in the data part of the log. Example:
```
contract ClientReceipt {
  event Deposit(address indexed _from, hash indexed _id, uint _value);
  function deposit(hash _id) {
    Deposit(msg.sender, _id, msg.value);
  }
}
```
Here, the call to `Deposit` will behave identical to
`log3(msg.value, 0x50cb9fe53daa9737b786ab3646f04d0150dc50ef4e75f59509d83667ad5adb20, sha3(msg.sender), _id);`. Note that the large hex number is equal to the sha3-hash of "Deposit(address,hash256,uint256)", the event's
signature.

## Layout of Storage

Variables of finite size (everything except mapping types) are laid out contiguously in storage
starting from position `0`. Due to their unpredictable size, mapping types use a `sha3`
computation to find the position of the value in the following way: The mapping itself
occupies an (unfilled) slot in storage at some position `p` according to the above rule (or by
recursively applying this rule for mappings to mappings). The value corresponding to key
`k` is located at `sha3(k . p)` where `.` is concatenation. If the value is again a
non-elementary type, the positions are found by adding an offset of `sha3(k . p)`.

So for the following contract snippet:
```
contract c {
  struct S { uint a; uint b; }
  uint x;
  mapping(uint => mapping(uint => S)) data;
}
```
The position of `data[4][9].b` is at `sha3(uint256(9) . sha3(uint256(4) . uint(256(1))) + 1`.

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