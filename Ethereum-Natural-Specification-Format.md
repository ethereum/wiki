---
name: Ethereum Natural Specification Format
category: 
---

Solidity contracts can have a special form of comments that form the basis of the Ethereum Natural Specification Format. For a usage example please check [here](https://github.com/ethereum/wiki/wiki/Natspec-Example/).

# Documentation Example

Documentation is inserted above the function following the doxygen notation of either one or multiple lines starting with `///` or a multiline comment starting with `/**` and ending with `*/`.

As an example consider the documentation of the following function:

```
  /// @notice Send `(valueInmGAV / 1000).fixed(0,3)` GAV from the account of 
  /// `message.caller.address()`, to an account accessible only by `to.address()
  /// @dev This should be the documentation of the function for the developer docs
  /// @param to The address of the recipient of the GavCoin
  /// @param valueInmGav The GavCoin value to send
  function send(address to, uint256 valueInmGAV) {
    if (balances[message.caller] >= valueInmGAV) {
      balances[to] += valueInmGAV;
      balances[message.caller] -= valueInmGAV;
    }
```

There are a few things to note about the above example.
- Natspec format uses doxygen tags with some special meaning. These are:
    + @title: This is a title that should describe the contract and go above the contract definition
    + @author: The name of the author of the contract. Should also go above the contract definition.
    + @notice: Represents user documentation. This is the text that will appear to the user to notify him
      of what the function he is about to execute is doing
    + @dev: Represents developer documentation. This is documentation that would only be visible to the
      developer.
    + @param: Documents a parameter just like in doxygen. Has to be followed by the parameter name.
    + @return: Documents the return type of a contract's function.

- If any of the above are missing they are simply considered as blank, and it's not illegal to omit any of them.
 
- `(valueInmGAV / 1000).fixed(0,3)` A dynamic expression. This should be a valid Javascript/Paperscript expression, which when evaluated in an EVM Javascript environment initialised with various system values (such as parameters).


# Documentation Output

When parsed, documentation such as the one from the above example will produce 2 different json files. One is meant to be consumed by the user as a notice when a function is executed and the other to be used by the developer.

Let us see a more full contract example.

```
/// @title This is the contract title.
/// @author Homer Simpson
contract GavCoin
{
  /// @notice Send `(valueInmGAV / 1000).fixed(0,3)` GAV from the account of 
  /// `message.caller.address()`, to an account accessible only by `to.address()
  /// @dev This should be the documentation of the function for the developer docs
  /// @param to The address of the recipient of the GavCoin
  /// @param valueInmGav The GavCoin value to send
  function send(address to, uint256 valueInmGAV) {
    if (balances[message.caller] >= valueInmGAV) {
      balances[to] += valueInmGAV;
      balances[message.caller] -= valueInmGAV;
    }
  }

  /// @notice `(balanceInmGAV / 1000).fixed(0,3)` GAV is the total funds available to `who.address()`.
  /// @param who The address of the person whose balance we check
  /// @return The balance of the user provided as argument
  function balance(address who) constant returns (uint256 balanceInmGAV) {
    balanceInmGAV = balances[who];
  }

invariants:
  /// @notice The sum total amount of GAV in the system is 1 million.
  /// @dev This is the invariant development documentation
  reduce(0, add, map(valueOf, balances)) == 100000000000;

construction:
  /// @notice Endows `message.caller.address()` with 1m GAV.
  balances[message.caller] = 100000000000;

state:
  mapping balances(address) returns uint256 with function(address a) returns uint256 { return a; };
};
```

## User Documentation

The above documentation will produce the following user documentation json file as output:

```
{
  "source": "...",
  "language": "Solidity",
  "languageVersion": 1,
  "methods": {
    "send(address,uint256)": { "notice": "Send `(valueInmGAV / 1000).fixed(0,3)` GAV from the account of `message.caller.address()`, to an account accessible only by `to.address()`." },
    "balance(address)": { "notice": "`(balanceInmGAV / 1000).fixed(0,3)` GAV is the total funds available to `who.address()`." }
  },
  "invariants": [
    { "notice": "The sum total amount of GAV in the system is 1 million." }
  ],
  "construction": [
    { "notice": "Endows `message.caller.address()` with 1m GAV." }
  ]
}
```

Note that the key by which to find the methods is the function's canonical signature as defined in the [Contract ABI](https://github.com/ethereum/wiki/wiki/Ethereum-Contract-ABI#signature) and not simply the function's name.

## Developer Documentation

Apart from the user documentation file, a developer documentation json file should also be produced and should look like this:

```
{
  "author": "Homer Simpson",
  "title": "This is the contract title.",
  "methods": {
    "send(uint256)": {
      "details": "This should be the documentation of the function for the developer docs"
    },
    "balance": {
      "details": ""
    }
  },
  "invariants": [
     { "details": "This is the invariant development documentation"}
  ],
  "construction": {
     "details": ""
  }
}
```

## Example usage

There is a detailed example of using the Natspec feature with the cpp client [here](https://github.com/ethereum/wiki/wiki/Natspec-Example/).