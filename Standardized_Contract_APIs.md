Although Ethereum allows developers to create absolutely any kind of application without restriction to specific feature types, and prides itself on its "lack of features", there is nevertheless a need to standardize certain very common use cases in order to allow users and applications to more easily interact with each other. This includes sending currency units, registering names, making offers on exchanges, and other similar functions. A standard typically consists of a set of function signatures for a few methods, eg. `send`, `register`, `delete`, providing the set of arguments and their formats in the [Ethereum contract ABI](https://github.com/ethereum/wiki/wiki/Ethereum-Contract-ABI) language.

### Currency

* `sendCoin(uint _val, address _to) returns (bool _success)`: send currency
* `sendCoinFrom(address _from, uint _val, address _to) returns (bool _success)`: send currency from another account
* `function coinBalance() constant returns (uint _r)`: get your balance
* `coinBalanceOf(address _a) constant returns (uint _r)`: get the balance of another account

The `sendCoinFrom` is used for a "direct debit" workflow, allowing contracts to charge fees in sub-currencies; the command should fail unless the `from` account has deliberately authorized the sender of the message via some mechanism; we propose these standardized APIs for approval:

* `approve(address _a)`: allow `addr` to direct debit from your account
* `isApproved(address _proxy) constant returns (bool _r)`: returns 1 if `addr` is allowed to direct debit from your account
* `isApprovedFor(address _target, address _proxy) constant returns (bool _r)`: returns 1 if `proxy` is allowed to direct debit from `target`
* `approveOnce(address _a, uint256 _maxval)`: makes a one-time approval to send a maximum amount of currency equal to `_maxval`

### Exchanges

* `new(string32 offer_currency, uint256 offer_value, string32 want_currency, uint256 want_value) returns (uint256 offerid)`: express a desire to give up `offer_value` units of `offer_currency` in exchange for `want_value` units of `want_currency`. The exchange may or may not fill orders partially. Optionally returns an ID for the offer
* `claim(uint256 offerid)`: claim a particular offer.
* `delete(string32 offer_currency, string32 want_currency)`: removes the caller's best offer on the order book to exchange `offer_currency` for `want_currency`
* `delete(uint256 offerid)`: delete a particular offer
* `price(string32 offer_currency, string32 want_currency) returns (real128x128 price)`: returns the best price (irrespective of volume) for how much `offer_currency` is required to return one unit of `want_currency`

Note that individual exchanges may support only a subset of these commands; for example, for efficiency's sake some may only support `new` and `claim`, and require sorting logic to be done off-chain, whereas others will support `new` and have orders match against each other automatically.

### Registries

Registries (eg. domain name systems) have the following API:

* `reserve(bytes32 _name)`: reserves a name and sets its owner to you if it is not yet reserved
* `owner(bytes32 _name) constant returns (address o_owner)`: get the owner of a particular name
* `transfer(bytes32 _name, address _newOwner)`: transfer ownership of a name
* `setAddress(bytes32 _name, address _a, bool _primary)`: set the primary address associated with a name (similar to an A record in traditional DNS)
* `addr(bytes32 _name) constant returns (address o_address)`: get the primary address associated with a name 
* `setContent(bytes32 _name, bytes32 _content)`: if you are the owner of a name, sets its associated content
* `content(bytes32 _name) constant returns (bytes32)`: get the content associated with a name
* `setSubRegistrar(bytes32 _name, address _registrar)`: records the name as referring to a sub-registrar at the given address
* `subRegistrar(bytes32 _name) constant returns (address)`: gets the sub-registrar associated with the given name
* `disown(bytes32 _name)`: relinquishes control over a name that you currently control

Events:

* `event Changed(bytes32 indexed name)`: triggered when changed to a domain happen
* `event PrimaryChanged(bytes32 indexed name, address indexed addr)`: triggered when the primary address of a domain changes