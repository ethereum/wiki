---
name: Useful DAPP Patterns
category: 
---

The following page is a collection of useful patterns, Ðapps can use, such as talking to the blockchain reliably.

The example patterns can possibly change, so don't rely fully on them as of yet.

## Examples

- Contract deployment by code:    
(Outdated, use `web3.contract(abiArray).new({}, function(e, res){...})`)
https://gist.github.com/frozeman/655a9325a93ac198416e

- Test a contract transaction with a `call` before actually sending:
https://gist.github.com/ethers/2d8dfaaf7f7a2a9e4eaa
