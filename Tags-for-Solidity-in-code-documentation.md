At the moment pursuant to https://github.com/ethereum/wiki/wiki/Solidity,-Docs-and-ABI
the following tags are available to use after /// to provide in-code documentation.

Title

`/// @title Some title here.`

Author

`/// @author Homer Simpson`

Notice

```/// @notice Send `(valueInmGAV / 1000).fixed(0,3)` GAV from the account of
/// `message.caller.address()`, to an account accessible only by `to.address()`.```

Developer Documentation

`/// @dev This is the developer documentation.`

Docs for parameters

```/// @param to The docs for the first param.
/// @param valueInmGav The docs for the second param.```


Which in json would result in something like this:

{
  "source": "...",
"author": "Gav Wood",
"description": "Some description of this contract.",
  "language": "Solidity",
  "languageVersion": 1,
  "methods": {
    "send": { "notice": "Send `(valueInmGAV / 1000).fixed(0,3)` GAV from the account of `message.caller.address()`, to an account accessible only by `to.address()`." },
 "title": "Send some GAV.",
      "details": "..."
    "balance": { "notice": "`(balanceInmGAV / 1000).fixed(0,3)` GAV is the total funds available to `who.address()`." }
  },
  "invariants": [
 { "title": "...", "details": "Markdown description of the first invariant." }
    { "notice": "The sum total amount of GAV in the system is 1 million." }
  ],
  "construction": [
    { "notice": "Endows `message.caller.address()` with 1m GAV." }
"details": "Creates the contract with..."
  ]
}