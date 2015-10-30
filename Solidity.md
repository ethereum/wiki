---
name: Solidity
category: 
---

## This page might be outdated.

Solidity is probably the first example of a Contract-Oriented Language, a slight reworking of the well-established group of Object-Oriented Languages. Here's an example of Solidity:

```
contract PriceFeed is owned, mortal, priced, named("GoldFeed")
{
   function updateInfo(uint newInfo) onlyowner {
      info = newInfo;
   }
	
   function get() constant costs returns(uint r) {
      r = info;
   }

   uint info;
}
```

From the syntax, it will feel mostly similar to something between JavaScript and C++ but with a number of  additions to make it suitable for writing contracts within Ethereum. Language additions include:

* static typing;
* property-based language transforms;
* contracts as first-class entities;
* state as part of a contract able to map & segment information into the permanent storage;
* invariants, pre- and post-conditions as part of a contract;
* variadic return values with named components;
* a safe language subset allowing guaranteed static analysis & proofs;
* an inbuilt test-generation and execution environment for situations where formal proofing is not practical.

### Documentation

It is expected that each method, together with the contract itself, will be documented in several ways. Aside from informal descriptions, each method should come with a formalised method to describe exactly its effect on the state of Ethereum. It should also include, where possible, pre- and post-conditions on the contract state, and the contract itself should include invariants over its own state, again each translated into natural language.

For GavCoin, a meta coin that that is sub-divisible down to 1,000, and includes two functions `balance` and `send`, we might see the basic, undocumented contract as:

```
contract GavCoin is named("GavCoin")
{
  function GavCoin() {
    balances[transaction.sender] = 100000000000;
  }
  function send(address to, uint256 valueInmGAV) {
    balances[to] += valueInmGAV;
    balances[transaction.sender] -= valueInmGAV;
  }
  function balance(address who) constant returns (uint256 balanceInmGAV) {
    balanceInmGAV = balances[who];
  }

  invariant reduce(0, +, map(valueOf, balances)) == 100000000000;

  mapping (address => uint256) balances;
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
contract GavCoin is named("GavCoin")
{
  /// @notice Endows `message.caller.address()` with 1m GAV.
  function GavCoin() {
    balances[transaction.sender] = 100000000000;
  }

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

  /// @notice The sum total amount of GAV in the system is 1 million.
  invariant reduce(0, add, map(valueOf, balances)) == 100000000000;

  mapping (address => uint256) balances;
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

