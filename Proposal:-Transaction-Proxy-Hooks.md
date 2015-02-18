## Basic Design

The ethereum.js interface may be utilised to allow the ÐApp client to register itself as the transaction handler for each of a number of accounts; in doing so it will provide an additional callback.

This is done through one addition to the ethereum.js `web3.eth` object, `registerProxyTransactor`:

```
var addresses = [];
// TODO: populate addresses

function f(tx) {
  // tx includes tx.from, tx.to, tx.data, tx.value, tx.gasPrice, tx.gas
  // and is *exactly* the same as what was passed to eth.transact, but probably not in this
  // JS environment.
  // tx.from is guaranteed to be an element of addresses.
  // TODO: do something
}

web3.eth.registerProxyTransactor(addresses, f);
```

That's the entire JS API.

In terms of JSON-RPC, there must be an alteration to `eth_accounts`: rather than just including the accounts that we have the secret key for (and that the user has accepted are valid for this dapp to use and know about), all accounts passed as the first argument to `registerProxyTransactor` should also be contained (with the same safeguards as per user acceptance).

There are also two additional calls in the JSON-RPC; one for polling the status of whether any transactions are waiting to be signed by our callback that we registered as the second argument of `registerProxyTransactor`, and one for letting the core know that our ethereum.js object is capable of proxying transactions sent from the addresses passed as the first argument of `registerProxyTransactor`.

The first is `eth_registerAddressHandler`; it takes one argument which is an array of addresses. Any otherwise unsignable transactions whose from address is included in this array should be routed to this ethereum.js object through the polling RPC function `eth_checkProxyTransactions`.

`eth_checkProxyTransactions` takes no arguments but returns an array of transactions, each of the form that are passed to `web3.eth.transact`.

### Notes

If it's called twice, all previous state associated with it is replaced. If two ethereum.js objects both try to handle the same address, the first one wins and the second silently fails.


