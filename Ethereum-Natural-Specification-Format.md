Solidity contracts can have a special form of comments that form the basis of the Ethereum Natural Specification Format. 

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
    + @notice: Represents user documentation. This is the text that will appear to the user to notify him
      of what the function he is about to execute is doing
    + @dev: Represents developer documentation. This is documentation that would only be visible to the
      developer.
    + @param: Documents a parameter just like in doxygen. Has to be followed by the parameter name.
 
- `(valueInmGAV / 1000).fixed(0,3)` A dynamic expression. This should be a valid Javascript/Paperscript expression, which when evaluated in an EVM Javascript environment initialised with various system values (such as parameters).
