---
name: SendTransaction Return Value Proposal
category: 
---

After talking to Marek we came to the conclusion, that the current return value of `eth_sendTransaction` is not enough to be able to work with self send transactions.

If you send a contract creation transaction, you get the contract address back, but not the transaction hash. 

Additional with the new proposal, we could even let non-constant contract functions return values, like gavin wanted to do it in https://github.com/ethereum/dapp-bin/blob/master/wallet/wallet2.sol#L237 (Currently thats impossible and that contract would be wrong)

## Proposal

Instead of returning the hash or a contract address lets return a object contain everything:

```js
{
   transactionHash: '0x234567..',
   contractAddress: '0x123456' // or null
   returnValue: '0x342567' // or null
}
```