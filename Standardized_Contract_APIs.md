Although Ethereum allows developers to create absolutely any kind of application without restriction to specific feature types, and prides itself on its "lack of features", there is nevertheless a need to standardize certain very common use cases in order to allow users and applications to more easily interact with each other. This includes sending currency units, registering names, making offers on exchanges, and other similar functions. A standard typically consists of a set of function signatures for a few methods, eg. `send`, `register`, `delete`, providing the set of arguments and their formats in the [Ethereum contract ABI](https://github.com/ethereum/wiki/wiki/Ethereum-Contract-ABI) language.

### Currency

* `balance(address addr) returns (uint256 bal)`: get balance
* `send(address to, uint256 value) returns (bool success)`: send currency
* `send(address to, uint256 value, address from) returns (bool success)`: send currency from another account

The third command is used for a "direct debit" workflow, allowing contracts to charge fees in sub-currencies; the second `send` command should fail unless the `from` account has deliberately authorized the sender of the message via some mechanism; we propose these standardized APIs for approval:

* `approve(address addr, bool status)`: status = 1 to allow `addr` to direct debit from your account, status = 0 to disapprove
* `approved(address addr) returns (bool status)`: returns 1 if `addr` is allowed to direct debit from your account
* `approve_once(address addr, bool status, uint256 maxval)`: makes a one-time approval to send a maximum amount of currency equal to `maxval`

### Exchanges

* `new(string32 offer_currency, uint256 offer_value, string32 want_currency, uint256 want_value) returns (uint256 offerid)`: express a desire to give up `offer_value` units of `offer_currency` in exchange for `want_value` units of `want_currency`. The exchange may or may not fill orders partially. Optionally returns an ID for the offer
* `claim(uint256 offerid)`: claim a particular offer.
* `delete(string32 offer_currency, string32 want_currency)`: removes the caller's best offer on the order book to exchange `offer_currency` for `want_currency`
* `delete(uint256 offerid)`: delete a particular offer
* `price(string32 offer_currency, string32 want_currency) returns (real128x128 price)`: returns the best price (irrespective of volume) for how much `offer_currency` is required to return one unit of `want_currency`

Note that individual exchanges may support only a subset of these commands; for example, for efficiency's sake some may only support `new` and `claim`, and require sorting logic to be done off-chain, whereas others will support `new` and have orders match against each other automatically.

### Registries

Registries (eg. domain name systems) have the following API:

* `register(string32 key) returns (bool success)`: registers a particular key as being owned by yourself
* `set_data(string32 key, string32 data)`: if you are the owner of a key, sets its associated data
* `get_data(string32 key)`: get the data associated with a key
* `get_owner(string32 key)`: get the owner of a particular key
* `get_key(address owner)`: get a key that has been registered by a particular owner
* `unregister(string32 key)`: unregisters a key that you currently control

