Currently a filter created with `eth_newBlockFilter` and call `eth_getFilterLogs`, or `get_filterChanges` should return `[null]` or `[null, null,...] If multiple blocks came in since the last poll.

Thats fine as you can check for the current block e.g. using:

```js
web3.eth.filter('latest').watch(function(err, res){
   // i can get the current block here
   web3.eth.getBlock('latest');
});
```

The problem is that when a lot of blocks came in since the last time you polled with a return of `[null, null, null, ...]`, your callback will fire multiple times accordingly, but using `getBlock` inside will always give me the latest block, which is actually wrong.

It would be better if filters created with `eth_newBlockFilter` and calling `eth_getFilterLogs`, or `get_filterChanges` will give back the mined/imported block, or pending block it was fired from.

This way you can be sure to receive the actual block which caused the callback and do something on them, or with its transactions.