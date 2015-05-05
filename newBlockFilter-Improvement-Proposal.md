Currently a filter created with `eth_newBlockFilter` and call `eth_getFilterLogs`, or `get_filterChanges` should return `[null]` or `[null, null,...]` If multiple blocks came in since the last poll.

Thats fine as you can check for the current block e.g. using:

```js
web3.eth.filter('latest').watch(function(err, res){
   // i can get the current block here
   web3.eth.getBlock('latest');
});
```

The problem is that when a lot of blocks came in since the last time you polled with a return of `[null, null, null, ...]`, your callback will fire multiple times accordingly, but using `eth.getBlock` inside will always give me the latest block, which is actually wrong.

*It would be better if filters created with `eth_newBlockFilter` and calling `eth_getFilterLogs`, or `eth_getFilterChanges` will give back the mined/imported block, or pending block it was fired from.*

This way you can be sure to receive the actual block which caused the callback and do something on them, or with its transactions.


# Summary

After discussing with Gavin we cam up with the following improvement:

```js
// eth_newBlockFilter will receive no parameter
{"jsonrpc":"2.0","method":"eth_newBlockFilter","params":[],"id":529}

// poll
{"jsonrpc":"2.0","method":"eth_getFilterChanges","params":["0xb"],"id":530} // we assume the filter ID is "0xb"

// should return one or more blocks, depending on how much arrived since the last poll
{
		"id": 530,
		"jsonrpc": "2.0",
		"result": [{ // block objects
                    number: "0x2",
                    ...
                 },
                 {
                    number: "0x3",
                    ...
                 }]
	}
```

```js
// eth_newTransactionFilter will receive no parameter
{"jsonrpc":"2.0","method":"eth_newTransactionFilter","params":[],"id":529}

// poll
{"jsonrpc":"2.0","method":"eth_getFilterChanges","params":["0xb"],"id":530} // we assume the filter ID is "0xb"

// should return one or more pending transactions, which were added to the pending state since the last poll
{
		"id": 530,
		"jsonrpc": "2.0",
		"result": [{ // tx objects
                    value: "0x2",
                    ...
                 },
                 {
                    value: "0x3",
                    ...
                 }]
	}
```