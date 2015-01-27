This is a list to explain and demonstrate new Solidity features as soon as they are completed.

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
    test() {
        data = 42;
    }
    uint256 data;
}
```

For example in the above contract if you tried to call test's `data()` method then you would obtain the result 42.

```
contract test {
    test() {
        data = 42;
    }
private:
    uint256 data;
}
```
On the other hand on the above contract there is no accessor generated since the state variable is private.