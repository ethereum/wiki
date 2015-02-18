## Requirements

Need an abstraction mechanism to allow ÐApps like the Wallet provide their services in an integrated manner without compromising the neutrality of the browser or placing undue burden on third-party ÐApp developers to support them.

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

The first is `eth_registerAddressHandler`; it takes one argument which is an array of addresses and returns an integer identifier. Any otherwise unsignable transactions whose from address is included in this array should be, at some time later, returned to this session as a result of polling the RPC function `eth_checkProxyTransactions`.

`eth_checkProxyTransactions` takes one argument - the previous integer identifier and returns an array of transactions, each of the form that are passed to `web3.eth.transact`.

### Core

Cores must maintain queues of proxy transactions together with sets of addresses, one each per ethereum.js `web3.eth` object (identified through the integer returned to `eth_registerAddressHandler` and provided by `eth_checkProxyTransactions`). These transactions are to be returned to the session via `eth_checkProxyTransactions`. They are to be filled whenever an `eth_transact` is called (from any session) with a `from` address that is contained in the corresponding set of proxyable addresses.


### Notes

If it's called twice, all previous state associated with it is replaced. If two ethereum.js `web3.eth` objects both try to handle the same address, the first one wins and the second silently fails.


