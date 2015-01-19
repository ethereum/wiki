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