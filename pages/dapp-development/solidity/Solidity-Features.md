---
name: Solidity Features
category: 
---

This is a list to explain and demonstrate new Solidity features as soon as they are completed.
It is used as a kind of changelog and items introduced at some point might be changed at a later point. The official reference is the [Documentation](https://ethereum.github.io/solidity/) which should always reflect the current state of the language.

## Special Type Treatment for Integer Literals

[PT](https://www.pivotaltracker.com/story/show/83393282) Expressions only involving integer literals are now essentially treated as "big integers" (i.e. they do not overflow) until they are actually used with a non-literal. The type of the expression is only determined at that point as the smallest integer type that can contain the resulting value. Example:

```
contract IntegerLiterals {
  function f() {
    // The folloing would have caused a type error in earlier versions (cannot add int8 to uint8),
    // now the value of x is set to 9 and the type to uint8.
    var x = -1 + 10;
    // It is even possible to use literals that do not fit any of the Solidity types as long as
    // the final value is small enough. The value of y will be 1, its type uint8.
    var y = (0x100000000000000000001 * 0x100000000000000000001 * 0x100000000000000000001) & 0xff;
  }
}
```

## Contracts Inherit all Members from Address

[PT](https://www.pivotaltracker.com/story/show/85006746) Contract types are implicitly convertible to `address` and explicitly convertible to and from all integer types. Furthermore, a contract type contains all members of the address type with the semantics applying to the contract's address, unless overwritten by the contract.

```
contract Helper {
  function getBalance() returns (uint bal) {
    return this.balance; // balance is "inherited" from the address type
  }
}
contract IsAnAddress {
  Helper helper;
  function setHelper(address a) {
    helper = Helper(a); // Explicit conversion, comparable to a downcast
  }
  function sendAll() {
    // send all funds to helper (both balance and send are address members)
    helper.send(this.balance);
  }
}
```

## ABI requires arguments to be padded to 32 bytes

[PT](https://www.pivotaltracker.com/story/show/85006670) The latest version of the ABI specification
requires arguments to be padded to multiples of 32 bytes. This is not a language feature that can be demonstrated as code examples. Please see the automated tests `SolidityEndToEndTests::packing_unpacking_types` and `SolidityEndToEndTests::packing_signed_types`.

## Specify value and gas for function calls

[PT](https://www.pivotaltracker.com/story/show/84983014) External functions have member functions "gas" and "value" that allow to change the default amount of gas (all) and wei (0) sent to the called contract. "new expressions" also have the value member.

```
contract Helper {
  function getBalance() returns (uint bal) { return this.balance; }
}
contract Main {
  Helper helper;
  function Main() { helper = new Helper.value(20)(); }
  /// @notice Send `val` Wei to `helper` and return its new balance.
  function sendValue(uint val) returns (uint balanceOfHelper) {
    return helper.getBalance.value(val)();
  }
}
```

## delete for structs

[PT](https://www.pivotaltracker.com/story/show/82574620) `delete` clears all members of a struct.

```
contract Contract {
  struct Data {
    uint deadline;
    uint amount;
  }
  Data data;
  function set(uint id, uint deadline, uint amount) {
    data.deadline = deadline;
    data.amount = amount;
  }
  function clear(uint id) { delete data; }
}
```

Note that, unfortunately, this only works directly on structs for now, so I would propose to not announce "delete" as a feature yet.

## Contract Inheritance

[PT1](https://www.pivotaltracker.com/story/show/84976094)
[PT2](https://www.pivotaltracker.com/story/show/86666936) Contracts can inherit from each other.

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

## Function Modifiers

[PT](https://www.pivotaltracker.com/story/show/85007072) Modifiers can be used to easily change the behaviour of functions, for example to automatically check a condition prior to executing the function. They are inheritable properties of contracts and may be overridden by derived contracts.
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

Multiple modifiers can be applied to a function by specifying them
in a whitespace-separated list and will be evaluated in order.
Explicit returns from a modifier or function
body immediately leave the whole function, while control flow reaching the
end of a function or modifier body continues after the "_" in the previous
modifier.
Arbitrary expressions are allowed for modifier arguments and in this context,
all symbols visible from the function are visible in the modifier. Symbols
introduced in the modifier are not visible in the function (as they might
change by overriding).


## Conversion between String and Hash types

[PT](https://www.pivotaltracker.com/story/show/85907772) The explicit conversion between `string` and `hash` types of equal size is now allowed. Example:

```
contract Test {
  function convert(hash160 h, string20 s) returns (string20 res_s, hash160 res_h) {
    res_s = string20(h);
    res_h = hash160(s);
  }
}
```

## Access to super

[PT](https://www.pivotaltracker.com/story/show/86688340) In the following contract, the function `kill` is overridden by sibling classes. Due to the fact that the sibling classes do not know of each other, they can only call `mortal.kill()` with the effect that one of the overrides is completely bypassed. A reasonable implementation would call the kill functions in all classes in the inheritance hierarchy.
```
contract mortal { function kill() { suicide(msg.sender); } }
contract named is mortal { function kill() { /*namereg.unregister();*/ mortal.kill(); } }
contract tokenStorage is mortal { function kill() { /*returnAllTokens();*/ mortal.kill(); } }
contract MyContract is named, tokenStorage {}
```

The `super` keyword solves this. Its type is the type of the current contract if it were empty, i.e. it contains all members of the current class' bases. Access to a member of super invokes the usual virtual member lookup, but it ends just above the current class. Using this keyword, the following works as expected:
```
contract mortal { function kill() { suicide(msg.sender); } }
contract named is mortal { function kill() { /*namereg.unregister();*/ super.kill(); } }
contract tokenStorage is mortal { function kill() { /*returnAllTokens();*/ super.kill(); } }
contract MyContract is named, tokenStorage {}
```
## State Variable Accessors
[PT](https://www.pivotaltracker.com/story/show/86308642) Public state variables now have accessors created for them. Basically any `public` state variable can be accessed by calling a function with the same name as the variable.

```
contract test {
    function test() {
        data = 42;
    }
    uint256 data;
}
```

For example in the above contract if you tried to call test's `data()` method then you would obtain the result 42.

```
contract test {
    function test() {
        data = 42;
    }
private:
    uint256 data;
}
```
On the other hand on the above contract there is no accessor generated since the state variable is private.

## Events

[PT](https://www.pivotaltracker.com/story/show/86896642) Events allow the convenient usage of the EVM logging facilities. Events are inheritable members of contracts. When they are called, they cause
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

## Fallback Functions

[PT](https://www.pivotaltracker.com/story/show/87035858) A contract can have exactly one unnamed
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

## Events in Exported Interfaces

[PT](https://www.pivotaltracker.com/story/show/87036508) Events are exported to the JSON and Solidity interfaces generated by the compiler. The contract
```
contract c {
    event ev(uint indexed a);
}
```
generates the JSON interface
```
[
   {
      "inputs" : [
         {
            "indexed" : true,
            "name" : "a",
            "type" : "uint256"
         },
         {
            "indexed" : false,
            "name" : "b",
            "type" : "uint256"
         }
      ],
      "name" : "ev",
      "type" : "event"
   }
]
```
and the Solidity interface
`contract c{event ev(uint256 indexed a,uint256 b);}`.

## Visibility Specifiers

[PT](https://www.pivotaltracker.com/story/show/86635568) Functions and storage variables can be specified as being `public`, `protected` or `private`, where the default for functions is `public` `protected` for storage variables. Public functions are part of the external interface and can be called externally, while for storage variables, an automatic accessor function is generated. Non-public functions are only visible inside a contract and its derived contracts (there is no distinction between `protected` and `private` for now).

```
contract c {
  function f(uint a) private returns (uint b) { return a + 1; }
  uint public data;
}
```
External functions can call `c.data()` to retrieve the value of `data` in storage, but are not able to call `f`.

## Numeric Literals with Ether Subdenominations
[PT](https://www.pivotaltracker.com/story/show/84986568) Numeric literals can also be followed by the common ether subdenominations and the value of the assigned to variable will be multiplied by the proper amount.
```
contract c {
  function c()
  {
      val1 = 1 wei;    // 1
      val2 = 1 szabo;  // 1 * 10 ** 12
      val3 = 1 finney; // 1 * 10 ** 15
      val4 = 1 ether;  // 1 * 10 ** 18
 }
  uint256 val1;
  uint256 val2;
  uint256 val3;
  uint256 val4;
}
```

## SHA3 with arbitrary arguments
[PT](https://www.pivotaltracker.com/story/show/86896766). `sha3()` can now take an arbitrary number and type of arguments.
```
contract c {
  function c()
  {
      val2 = 123;
      val1 = sha3("foo"); // sha3(0x666f6f)
      val3 = sha3(val2, "bar", 1031); //sha3(0x7b626172407)
  }
  uint256 val1;
  uint16 val2;
  uint256 val3;
}
```

## Optional Parameter Names
[PT](https://www.pivotaltracker.com/story/show/85594334). The names for function parameters and return parameters are now optional.
```
contract test {
  function func(uint k, uint) returns(uint){
    return k;
  }
}
```

## Generic call Method
[PT](https://www.pivotaltracker.com/story/show/86084248) Address types (and contracts by inheritance) have a method `call` that can receive an arbitrary number of arguments of arbitrary types (which can be serialized in memory) and will invoke a message call on that address while the arguments are ABI-serialized. If the first type has a memory-length of exactly four bytes, it is not padded to 32 bytes, so it is possible to specify a function signature.
```
contract test {
  function f(address addr, uint a) {
    addr.call(string4(string32(sha3("fun(uint256)"))), a);
  }
}
```

## Byte arrays
[PT](https://www.pivotaltracker.com/story/show/87037182) Basic support for variable-length byte arrays. This includes
 - `bytes` type for storage variables
 - `msg.data` is of `bytes` type and contains the calldata
 - functions taking arbitrary parameters (`call`, `sha3`, ...) can be called with `bytes` arguments.
 - copying between `msg.data` and `bytes` storage variables

What is not possible yet:
 - function parameters of `bytes` type
 - local variables of `bytes` type
 - index or slice access

```
contract c {
  bytes data;
  function() { data = msg.data; }
  function forward(address addr) { addr.call(data); }
  function getLength() returns (uint) { return addr.length; }
  function clear() { delete data; }
}
```

## Enums
[PT](https://www.pivotaltracker.com/story/show/86670106) Solidity now supports enums. Enums are explicitly convertible to all integer types but implicit conversion is not allowed.

```
contract test {
	enum ActionChoices { GoLeft, GoRight, GoStraight, SitStill };
	function test()
	{
	    choices = ActionChoices.GoStraight;
	}
	function getChoice() returns (uint d)
	{
	    d = uint256(choices);
	}
	ActionChoices choices;
	}
```

## Visibility Specifiers

[PT](https://www.pivotaltracker.com/story/show/86487946) The visibility of a function can be specified by giving at most one of the specifiers `external`, `public`, `inheritable` or `private`, where `public` is the default. "External" functions can only be called via message-calls, i.e. from other contracts or from the same contract using `this.function()` (note that this also prevents calls to overwritten functions in base classes). Furthermore, parameters of "external" functions are immutable. "Public" functions can be called from other contracts and from the same contract using stack-based calls. "Inheritable" and "private" functions can only be called via stack-based calls, while "inheritable" functions are only visible in the contract itself and its derived contracts and "private" functions are not even visible in derived contracts.

```
contract Base {
  function exte() external { }
  function publ() public /* can be omitted */ { }
  function inhe() inheritable { priv(); }
  function priv() private { }
}
contract Derived is Base {
  function g() {
    this.exte();
    // impossible: exte();
    this.publ();
    publ();
    // impossible: this.inhe();
    inhe();
    // impossible: this.priv();
    // impossible: priv();
  }
}
```

## Import Statement
[PT](https://www.pivotaltracker.com/story/show/87165660) We can now import other contracts and/or standard library contracts using the `import` keyword.
```
import "mortal";

contract Test is mortal {
    // since we import the standard library "mortal" contract and we inherit from it
    // we can call the kill() function that it provides
    function killMe() { kill();}
}
```

## Inline members initialization
[PT](https://www.pivotaltracker.com/story/show/84982976) Inline members can be initialized at declaration time.
```
contract test {
  function test(){
    m_b = 6;
  }
  uint m_a = 5;
  uint m_b;
}
```

## The arguments of the constructor of base contract
[PT](https://www.pivotaltracker.com/story/show/88454388) It is possible to pass arguments to the base contracts constructor. The arguments for the base constructor in the header will be optional later.
```
contract Base {
	function Base(uint i)
	{
		m_i = i;
	}
	uint public m_i;
}
contract Derived is Base(0) {
	function Derived(uint i) Base(i) {}
}
```

## Detect failed CALLs
[PT](https://github.com/ethereum/cpp-ethereum/pull/1212) If a CALL fails, do not just silently continue. Currently, this issues a STOP but it will throw an exception once we have exceptions.
```
contract C {
  function willFail() returns (uint) {
    address(709).call();
    return 1;
  }
}
```
`willFail` will always return an empty byte array (unless someone finds the correct private key...).

## Basic features for arrays
[PT](https://www.pivotaltracker.com/story/show/84119688) Byte arrays and generic arrays of fixed and dynamic size are supported in calldata and storage with the following features: Index access, copying (from calldata to storage, inside storage, both including implicit type conversion), enlarging and shrinking and deleting. Not supported are memory-based arrays (i.e. usage in non-external functions or local variables), array accessors and features like slicing.
Access to an array beyond its length will cause the execution to STOP (exceptions are planned for the future).
```
contract ArrayExample {
  uint[7][] data;
  bytes byteData;
  function assign(uint[4][] input, bytes byteInput) external {
    data = input; // will assign uint[4] to uint[7] correctly, would produce type error if reversed
    byteData = byteInput; // bytes are stored in a compact way
  }
  function indexAccess() {
    data.length += 20;
    data[3][5] = data[3][2];
    byteData[2] = byteData[7]; // this will access sigle bytes
  }
  function clear() {
    delete data[2]; // will clear all seven elements
    data.length = 2; // clears everything after the second element
    delete data; // clears the whole array
  }
}
```

## Now Variable
[PT](https://www.pivotaltracker.com/story/show/89728640) The global scope contains an immutable variable called `now` which is an alias to `block.timestamp`, i.e. it contains the timestamp of the current block.
```
contract TimedContract {
  uint timeout = now + 4 weeks;
}
```

## HashXX and StringXX to bytesXX
[Link to PT] (https://www.pivotaltracker.com/story/show/88146508)
+ We replace `hash(XX*8)` and `stringXX` by `bytesXX`.
+ `bytesXX` behaves as `hash(XX*8)` in terms of convertability and operators and as `stringXX` in terms of layout in memory (alignment, etc).

+ `byte` is an alias for `bytes1`.

+ `string` is reserved for future use.


## `msg.sig` returns the function's signature hash
[Link to PT] (https://www.pivotaltracker.com/story/show/86896308)
New magic type `msg.sig` that will provide the hash of the current function signature as `bytes4` type.

```
contract test {
	function foo(uint256 a) returns (bytes4 value) {
		return msg.sig;
	}
}
```

Calling that function will return `2FBEBD38` which is the hash of the signature of `foo(uint256)`.

## Constant variables
[PT](https://www.pivotaltracker.com/story/show/86670364)
Added `constant` specifier for uint, mapping and bytesXX types. Variables declared with `constant` specifier should be initialized at declaration time and can not be changed later. For now local variables can not be constant. Constant variables are not stored in Storage.

```
contract Foo {
	function getX() returns (uint r) { return x; }
	uint constant x = 56;
}
```

## Anonymous Events
[PT](https://www.pivotaltracker.com/story/show/89518344)
Added `anonymous` specifier for Event. For the event declared as anonymous the hash of the signature of the event will not be added as a first topic. The format is

```
event <name>([index list]) anonymous;
```
Anonymous property is also visible for ABI document.

## Tightly packed storage

[PT](https://www.pivotaltracker.com/story/show/90011896)
Items in storage are packed tighly as far as possible according to the following rules:

 - The first item in a storage slot is stored lower-order aligned.
 - Elementary types use only that many bytes that are necessary to store them.
 - If an elementary type does not fit the remaining part of a storage slot, it is moved to the next storage slot.
 - Structs and array data always start a new slot and occupy whole slots (but items inside a struct or array are packed tightly according to these rules).

Examples:
```
contract C {
  uint248 x; // 31 bytes: slot 0, offset 0
  uint16 y; // 2 bytes: slot 1, offset 0 (does not fit in slot 0)
  uint240 z; // 30 bytes: slot 1, offset 2 bytes
  uint8 a; // 1 byte: slot 2, offset 0 bytes
  struct S {
    uint8 a; // 1 byte, slot +0, offset 0 bytes
    uint256 b; // 32 bytes, slot +1, offset 0 bytes (does not fit)
  }
  S structData; // 2 slots, slot 3, offset 0 bytes (does not really apply)
  uint8 alpha; // 1 byte, slot 4 (start new slot after struct)
  uint16[3] beta; // 3*16 bytes, slots 5+6 (start new slot for array)
  uint8 gamma; // 1 byte, slot 7 (start new slot after array)
}
```

## Common Subexpression Elimination Excluding Memory and Storage

[PT](https://www.pivotaltracker.com/story/show/89148380)
The optimizer splits code into blocks (at all operations that have non-local side effects like JUMP, CALL, CREATE and for also all instructions that access or modify memory or storage), analyses these blocks by creating an expression graph and establishes equivalences in a bottom-up way, simplifying expressions that e.g. involve constants. In the following code-generation phase, it re-creates the set of instructions that transform a given initial stack configuration into a given target stack configuration utilizing the simplest representatives of these equivalence classes.
In conjunction with the already present jump-optimization, the two code snippets given below should be compiled into the same sequence of instructions:
```
contract test {
  function f(uint x, uint y) returns (uint z) {
    var c = x + 3;
    var b = 7 + (c * (8 - 7)) - x;
    return -(-b | 0);
  }
}
```
```
contract test {
  function f(uint x, uint y) returns (uint z) {
    return 10;
  }
}
```

## Common Subexpression Elimination for Memory and Storage

[PT](https://www.pivotaltracker.com/story/show/90785926)
This adds support for memory and storage operations to the common subexpression eliminator. This makes it possible to e.g. stretch the equality inference engine across SSTORE, MSTORE and even SHA3 computations (which go via memory). Without optimizer (because of packed storage), there are 4 SLOAD, 3 SSTORE and 4 SHA3 operations. The optimizer reduces those to a single SLOAD, SHA3 and SSTORE each.
```
contract test {
  struct s { uint8 a; uint8 b; uint8 c; }
  mapping(uint => s) data;
  function f(uint x, uint8 _a, uint8 _b, uint8 _c) {
    data[x].a = _a;
    data[x].b = _b;
    data[x].c = data[x].a;
  }
}
```
## External Types
[PT](https://www.pivotaltracker.com/story/show/88772706)
All functions with visibility more than internal should have external types (ABI types) otherwise raise an error.
For Contract type external type is address type.
```
contract Foo {}
contract Test {
    function func() {
        Foo arg;
        this.Poo(arg);
        Poo(arg);
    }
    function Poo(Foo c) external {}
}
```
the ABI interface for Poo is Poo(address) when the Solidity interface is still Poo(Foo).

## Accessor for Arrays
[PT](https://www.pivotaltracker.com/story/show/88500646)
For Arrays the accessor is generated which accepts the index as parameter and returns an array element
```
contract test {
    uint[3] public data;
    function test() {
        data[0] = 0;
        data[1] = 1;
        data[2] = 2;
    }
}
```
In the above contract if you tried to call the data(1) method of the test you would obtain the result 1.

## Overloading Functions
[PT](https://www.pivotaltracker.com/story/show/85511572) Contracts can have multiple functions of the same name as long as the parameters differ in number or type. If such an overloaded function is referenced, it has to be called immediately to resolve the ambiguity using the types of the arguments. It is an error if not exactly one of the possible functions can be called with the given arguments.

```
contract Base {
  function f(uint a) {}
}
contract Derived is Base {
  function f(uint8 b) {}
  function g() {
    // f(250); would create a type error since 250 can be implicitly
    // converted both to a uint8 and to a uint type
    f(2000); // calls f from Base
  }
}
```
Of course overloading a function does not need inheritance, i.e. `f(uint a)` could as well have been defined directly in `Derived`.

Overloaded functions are also present in the external interface. It is an error if two externally visible functions differ by their Solidity types but not by their external types, e.g. `f(Derived _d)` and `f(address _a)` both end up accepting an `address` type for the ABI although they are considered different inside Solidity.

## Merging of Basic Blocks
[PT](https://www.pivotaltracker.com/story/show/89148124) Blocks of assembly instructions that do not contain jumps, stops or returns are moved and modified according to the following rules:

* if the control never simply flows into a block, but it is jumped to unconditionally, the block is moved, eliminating the jump
* blocks that are never jumped to are removed

These optimizations might sound not very powerful, but together with "Common Subexpression Elimination" (which is does much more than its name might suggest), the following contract is optimized to store `8` in the mapping and return the value without any jump.

```
contract c {
  function () returns (uint) { return g(8); }
  function g(uint pos) internal returns (uint) { setData(pos, 8); return getData(pos); }
  function setData(uint pos, uint value) internal { data[pos] = value; }
  function getData(uint pos) internal { return data[pos]; }
  mapping(uint => uint) data;
}
```

## Interface contracts

[PT](https://www.pivotaltracker.com/story/show/88344782) Contracts can be marked as "not fully implemented" by containing at least one abstract function. A function is abstract if it does not have a body defined.

```
contract base { function foo(); }
contract derived is base { function foo() {} }
```

For example in the above, foo is an abstract function and as such the base contract is an interface contract. All non-interface contracts that derive from it must implement its abstract functions.

## Bare Callcode

[PT](https://www.pivotaltracker.com/story/show/94682212) The address type receives a method `callcode` which is similar to `call`, but uses `CALLCODE` instead of `CALL` when the function is invoked. This means that the code at the given address will be executed in the context of the current contract. Example:

```
contract Code {
  uint m_data;
  function (uint v) { m_data = v; }
}
contract ActualContract {
  uint public m_data;
  function f() { Code(0x12345).callcode(7); }
}
```
Assuming the contract `Code` is deployed at the address `0x12345`, calling `f()` of `ActualContract` will result in `m_data` of `ActualContract` being modified. The user has to ensure that the layout of storage in both contracts is suitable for callcode to be used.

## Gas Estimation

[PT](https://www.pivotaltracker.com/story/show/90098268) Solidity provides two ways to compute an upper bound on the gas usage of code: A structural one, which can be used to identify expensive statements and a functional one which tries to give an exact gas estimation for each function.
Some gas costs depend on the state of the virtual machine, e.g. on the cost of `sha3` depends on the length of the argument and writing to storage has different costs depending on whether the storage slot had the value zero or not.

For the structural gas estimation, the gas cost of each opcode is computed assuming the intersection of all states in which the VM could reach this opcode. These costs are accumulated for each opcode that results from a specific statement (in some situations also other AST nodes) in the AST. So in this mode, opcodes are not counted multiple times even if they occur in loops.

The functional gas estimation takes a different approach: For each function in a contract, the execution of this function is "simulated". As we want to provide an upper bound on the gas costs independent of the actual arguments, this is sometimes not accurate and may even result in "infinite" gas costs. Note that the gas costs of message-called functions are not included in the gas costs of a function.

## Re-introduce string type

[PT](https://www.pivotaltracker.com/story/show/95173586)
`string` is added as a type which behaves exactly like `bytes` with the following differences:
 - index access is not allowed
 - it does not have a length member

In the ABI encoding (wiki already changed), string is a dynamic type whose "number of elements" field is the number of bytes, not the number of characters.
The encoding of the string is assumed to be UTF-8, but is not yet used inside Solidity.

## In-memory types

[PT](https://www.pivotaltracker.com/n/projects/1189488/stories/88238440)
Variables of reference type (structs and arrays including `string` and `bytes`) can either point to memory, storage or calldata. The keywords `storage` and `memory` as part of their declaration are used to indicate that (calldata cannot be used explicitly). Parameters (not return parameters) of external functions are forced to point to calldata. Parameters (also return parameters) of public and return parameters of external functions are forced to point to memory. In all other cases, if neither storage nor memory is given, function parameters default to point to memory and local variables default to point to storage.

Note that this story enforces the constraints on memory-stored structs, but does not yet fully implement the code-generation part. Arrays, on the other hand, are fully implemented.

As part of this change, references to storage are also cleaned up: An assignment of a state variable to a local variable or temporary converts it from a reference to a pointer. Assignments to storage pointers do not modify storage but only change the pointer. This means that it is not possible to assign a memory array to a storage pointer. Furthermore, it is illegal to pass a memory-array as an argument to a function that requires a storage reference (note that storage is statically allocated, i.e. there is no place to put this value). Es an example:
```js
contract c {
  uint[] x;
  function f(uint[] memoryArray) {
    x = memoryArray; // works, copies the array to storage
    var y = x; // works, assigns a pointer
    y[7]; // fine, returns the 8th element
    y.length = 2; // fine, modifies storage
    delete x; // fine, clears the array
    // y = memoryArray; // does not work, would need to create a new temporary / unnamed array in storage, but storage is "statically" allocated
    // delete y; // does not work, would set pointer to zero and does not make sense for pointer
  }
}
```

If possible (i.e. from anything to memory and from anything to a storage reference that is not a pointer), conversions between these data locations are performed automatically by the compiler. Sometimes, this is still not possible, i.e. mappings cannot reside in memory (as their size is unknown) and for now, some types in memory are not yet implemented, this includes structs and multi-dimensional arrays.

Of course, once a storage array is converted to memory, modifications do not affect the array in storage. You can either assign the modified array back to storage (though this would be vastly inefficient) or you can pass around a storage pointer to begin with. As an example:

```js
contract BinarySearch {
  /// Finds the position of _value in the sorted list _data.
  /// Note that "internal" is important here, because storage references only work for internal or private functions
  function find(uint[] storage _data, uint _value) internal returns (uint o_position) {
    return find(_data, 0, _data.length, _value);
  }
  function find(uint[] storage _data, uint _begin, uint _len, uint _value) private returns (uint o_position) {
    if (_len == 0 || (_len == 1 && _data[_begin] != _value))
      return uint(-1); // failure
    uint halfLen = _len / 2;
    uint v = _data[_begin + halfLen];
    if (_value < v)
      return find(_data, _begin, halfLen, _value);
    else if (_value > v)
      return find(_data, _begin + halfLen + 1, halfLen - 1, _value);
    else
      return _begin + halfLen;
  }
}
```


On memory usage: Since memory is wiped after each external function call, the Solidity runtime does not include proper memory management. It includes a "level indicator" which points to the next free memory slot. If memory is needed (because a storage object is copied to memory or an external function is called), it is allocated starting from this pointer. Functions that return objects stored in memory will not reset this pointer. This means that temporary memory objects will still take up space in memory even if they are not needed anymore. On the other hand, if a function does not return any memory-stored object, it resets the pointer to the value it had upon function entry (this is not yet implemented).

The EVM does not allow `CALL` to be used with variably-sized return values. Because of this, return types of message-called functions which are dynamically sized are transparently changed to `void`. Clearing up the confusion which might arise in face of the resulting error message remains to do.

Memory-stored objects as local variables are correctly zero-initialised: Members of structs and elements of fixed-size arrays are recursively initialised, dynamic arrays are set to zero length. `delete x` assigns a new zero-initialised value to `x`.

## Positive integers conversion to signed

[PT](https://www.pivotaltracker.com/n/projects/1189488/stories/92691082)
Positive integer literals are now convertible to signed if in value range.
```
int8 x = 2;
```

## Exceptions in Solidity

[PT](https://www.pivotaltracker.com/n/projects/1189488/stories/92929256)
Currently, there are two situations, where exceptions can happen in Solidity: If you access an array beyond its length (i.e. x[i] where i >= x.length) or if a function called via a message call does not finish properly (i.e. it runs out of gas or throws an exception itself). In such cases, Solidity will trigger an "invalid jump" and thus cause the EVM to revert all changes made to the state.

It is planned to also throw and catch exceptions manually.

## Structs in Memory

[PT](https://www.pivotaltracker.com/n/projects/1189488/stories/84119690)
Structs can be passed around as function arguments, be returned from functions
and created in memory.

```js
contract C {
    struct S { uint a; uint b; }
    struct A { uint x; uint y; S s; }
    A data;
    function f() internal returns (A) {
        // Construct structs inline, pass to a function and return from it.
        // Memory is allocated only once, pointers are passed around.
        // Construction by member name is possible.
        return g(A(5, 7, S({b: 1, a: 2})));
    }
    function g(A _a) internal returns (A) {
        _a.s.b = 2;
        data = _a; // performs a copy
        return _a;
    }
}
```

## Flexible String Literals

[PT](https://www.pivotaltracker.com/n/projects/1189488/stories/98462528)
String literals can be implicitly converted to `bytesX` (if they are not too long),
`string` and `bytes`, especially, they can be much longer than 32 bytes.

```js
contract C {
  bytes32 x;
  function greet() returns (string) {
    x = "Hello, World!";
    return "Hello, World!";
  }
}
```

## Strings as Mapping Keys

Strings are allowed as keys for mappings.
```js
contract C {
  mapping (string => uint) counter;
  function inc(string _s) { counter[_s]++; }
}
```

## Libraries (without inheritance)

[PT](https://www.pivotaltracker.com/n/projects/1189488/stories/82180360)

Libraries are similar to contracts, but their purpose is that they are deployed only once at a specific address and their functions are called using CALLCODE, i.e. the library's code is called in the context of the calling contract.

```js
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
    value = Math.max(10, Math.min(100, value)); // clamp value to [10, 100]
    // ...
  }
}
```

The calls to `Math.max` and `Math.min` are both compiled as calls (CALLCODEs) to an external contract. as the compiler cannot know where the library will be deployed at, these addresses have to be filled into the final bytecode by a linker. If the addresses are not given as arguments to the compiler, the compiled hex code will contain placeholders of the form `__Math______` (where `Math` is the name of the library). The address can be filled manually by replacing all those 40 symbols by the hex encoding of the address of the library contract.

Restrictions for libraries in comparison to contracts:
 - no state variables
 - cannot inherit nor be inherited

(these might be lifted at a later point)

How to use the commandline compiler to link binaries:

New option `--libraries` to either give a file containing the library addresses or directly as a string (tries to open as a file). Syntax: `<libraryName>: <address> [, or Whitespace] ...`
The address is a hex string that is optionally prefixed with `0x`.

If solc is called with the option `--link`, all input files are interpreted to be unlinked binaries (hex-encoded) and are linked in-place (if the input is read from stdin, it is written to stdout).
All options except `--libraries` are ignored (including `-o`).

## Throw

[PT](https://www.pivotaltracker.com/story/show/96275370)

throw is a statement that triggers a solidity exception and thus can be used to revert changes made during the transaction. It does not take any parameters and jumps to the error tag.
```
contract Sharer {
    function sendHalf(address addr) returns (uint balance) {
        if (!addr.send(msg.value/2))
            throw; // also reverts the transfer to Sharer
        return address(this).balance;
    }
}
```

## Tightly Stored Byte Arrays and Strings

[PT](https://www.pivotaltracker.com/story/show/101758652)

Byte arrays (`bytes`) and strings (`string`) are stored more tightly packed in storage:
Short values (less than 32 bytes) are stored directly together with the length:
`<value><length * 2>` (the 31 higher-significant bytes contain the value, the least significant byte contains the doubled length)
Long values (at least 32 bytes) are stored as they were stored before, just that the length is doubled and the least significant bit is set to one to indicate "long string".

Example: "abcdef" is stored as `0x61626364656600000...000d` while `"abcabcabc....abc"` (of length 40) is stored as `0x0000000...0051` in the main slot, and `616263616263...` is stored in the data slots.

## Internal Types for Libraries

[PT](https://www.pivotaltracker.com/story/show/101774798) Storage reference types are allowed to be passed to library functions. Together with this change, it is now possible to access internal types of other contracts and libraries and a compiler version stamp is added at the beginning of library runtime code.

Example:

```
/// @dev Models a modifiable and iterable set of uint values.
library IntegerSet
{
  struct data
  {
    /// Mapping item => index (or zero if not present)
    mapping(uint => uint) index;
    /// Items by index (index 0 is invalid), items with index[item] == 0 are invalid.
    uint[] items;
    /// Number of stored items.
    uint size;
  }
  function insert(data storage self, uint value) returns (bool alreadyPresent)
  {
    uint index = self.index[value];
    if (index > 0)
      return true;
    else
    {
      if (self.items.length == 0) self.items.length = 1;
      index = self.items.length++;
      self.items[index] = value;
      self.index[value] = index;
      self.size++;
      return false;
    }
  }
  function remove(data storage self, uint value) returns (bool success)
  {
    uint index = self.index[value];
    if (index == 0)
      return false;
    delete self.index[value];
    delete self.items[index];
    self.size --;
  }
  function contains(data storage self, uint value) returns (bool)
  {
    return self.index[value] > 0;
  }
  function iterate_start(data storage self) returns (uint index)
  {
    return iterate_advance(self, 0);
  }
  function iterate_valid(data storage self, uint index) returns (bool)
  {
    return index < self.items.length;
  }
  function iterate_advance(data storage self, uint index) returns (uint r_index)
  {
    index++;
    while (iterate_valid(self, index) && self.index[self.items[index]] == index)
      index++;
    return index;
  }
  function iterate_get(data storage self, uint index) returns (uint value)
  {
      return self.items[index];
  }
}

/// How to use it:
contract User
{
  /// Just a struct holding our data.
  IntegerSet.data data;
  /// Insert something
  function insert(uint v) returns (uint size)
  {
    /// Sends `data` via reference, so IntegerSet can modify it.
    IntegerSet.insert(data, v);
    /// We can access members of the struct - but we should take care not to mess with them.
    return data.size;
  }
  /// Computes the sum of all stored data.
  function sum() returns (uint s)
  {
    for (var i = IntegerSet.iterate_start(data); IntegerSet.iterate_valid(data, i); i = IntegerSet.iterate_advance(data, i))
      s += IntegerSet.iterate_get(data, i);
  }
}
```

## Destructuring Assignments

[PT](https://www.pivotaltracker.com/story/show/99085194) Inline tuples can be created and assigned to newly declared local variables or already existing lvalues. This makes it possible to access multiple return values from functions.

``` function f() returns (uint, uint, uint) { return (1,2,3); }```
```
var (a,b,c) = f();
var (,x,) = f();
var (,y) = f();
var (z,) = f();
```
For newly declared variables it is not possible to specify the types of variables, they will be inferred from the assigned value. Any component in the assigned tuple can be left out. If the first or last element is left out, they can consume an arbitrary number of values. At the end of this code, we will have:
`a == 1`, `b == 2`, `c == 3`, `x == 2`, `y == 3`, `z == 1`.

For newly constructed tuples, elements may not be left out, except for one special case that allows to distiguish between 1-tuples and single expressions: `(x)` is equivalent to `x`, but `(x,)` is a 1-tuple containing `x`.

Assigning to pre-existing lvalues is similar to declaring multiple variables and also allows wildcards:

```
contract c {
  string s;
  struct Data {uint a; uint b;}
  mapping(uint => Data) data;
  function f() {
    (s, data[45]) = ("abc", Data(1, 2));
  }
}
```

## `.push()` for Dynamic Storage Arrays

Dynamically-sized storage arrays have a member function `push`, such that
`var l = arr.push(el);` is equivalent to `arr[arr.length++] = el; var l = arr.length;`.

```
contract c {
  struct Account { address owner; uint balance; }
  Account[] accounts;
  function newAccount(address _owner, uint _balance) {
    accounts.push(Account(_owner, _balance));
  }
}

```