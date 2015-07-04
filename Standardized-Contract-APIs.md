Although Ethereum allows developers to create absolutely any kind of application without restriction to specific feature types, and prides itself on its "lack of features", there is nevertheless a need to standardize certain very common use cases in order to allow users and applications to more easily interact with each other. This includes sending currency units, registering names, making offers on exchanges, and other similar functions. A standard typically consists of a set of function signatures for a few methods, eg. `send`, `register`, `delete`, providing the set of arguments and their formats in the [Ethereum contract ABI](https://github.com/ethereum/wiki/wiki/Ethereum-Contract-ABI) language.

The standards described below have sample implementations available at https://github.com/ethereum/pyethereum/blob/develop/ethereum/tests/test_solidity.py

All function names are in lower camelCase (eg. `sendCoin`) and all event names are in upper CamelCase (eg. `CoinSent`). Input variables are in underscore-prefixed lower camelCase (eg. `_offerId`), and output variables are `_r` for pure getter (ie. constant) functions, `_success` (always boolean) when denoting success or failure, and other values (eg. `_offerId`) for methods that perform an action but need to return a value as an identifier. Addresses are referred to using `_addr` when generic, and otherwise if a more specific description exists (eg. `from`, `_to`).

### Currency

* `sendCoin(uint _value, address _to) returns (bool _success)`: send currency
* `sendCoinFrom(address _from, uint _value, address _to) returns (bool _success)`: send currency from another account
* `function coinBalance() constant returns (uint _r)`: get your balance
* `coinBalanceOf(address _addr) constant returns (uint _r)`: get the balance of another account

The `sendCoinFrom` is used for a "direct debit" workflow, allowing contracts to charge fees in sub-currencies; the command should fail unless the `from` account has deliberately authorized the sender of the message via some mechanism; we propose these standardized APIs for approval:

* `approve(address _addr)`: allow `addr` to direct debit from your account
* `isApproved(address _proxy) constant returns (bool _r)`: returns 1 if `proxy` is allowed to direct debit from your account
* `isApprovedFor(address _target, address _proxy) constant returns (bool _r)`: returns 1 if `proxy` is allowed to direct debit from `target`
* `approveOnce(address _addr, uint256 _maxValue)`: makes a one-time approval to send a maximum amount of currency equal to `_maxval`

Events:

* `event CoinSent(address indexed from, address indexed to, uint256 value)`: triggered when money is sent

### Decentralized exchanges

* `placeOrder(address _offerCurrency, uint256 _offerValue, address _wantCurrency, uint256 _wantValue) returns (uint256 _offerId)`: express a desire to give up `_offerValue` units of `_offerCurrency` in exchange for `_wantValue` units of `_wantCurrency`. The exchange may or may not fill orders partially. Optionally returns an ID for the offer. `_offerCurrency` and `_wantCurrency` are the addresses of the master contracts for the currencies in question.
* `claimOrder(uint256 _offerId)`: claim a particular offer.
* `deleteOrder(uint256 _offerId)`: delete a particular offer
* `deleteOrder(address _offerCurrency, address _wantCurrency)`: removes the caller's best offer on the order book to exchange `_offerCurrency` for `_wantCurrency`
* `price(bytes32 _offerCurrency, string32 _wantCurrency) returns (real128x128 _r)`: returns the best price (irrespective of volume) for how much `_offerCurrency` is required to return one unit of `_wantCurrency`

Note that individual exchanges may support only a subset of these commands; for example, for efficiency's sake some may only support `placeOrder` and `claimOrder`, and require sorting logic to be done off-chain, whereas others will support `placeOrder` only and have orders match against each other automatically.

Events:

* `event Traded(bytes32 indexed currencyPair, address indexed seller, uint256 offerValue, address indexed buyer, uint256 wantValue)`: triggered when a trade takes place, where `currencyPair = offerCurrency[:16] + wantCurrency[:16]` (this is done to limit the number of indexed parameters to 3)

### Registries

Registries (eg. domain name systems) have the following API:

* `reserve(bytes32 _name) returns (bool _success)`: reserves a name and sets its owner to you if it is not yet reserved
* `owner(bytes32 _name) constant returns (address _r)`: get the owner of a particular name
* `transfer(bytes32 _name, address _newOwner)`: transfer ownership of a name
* `setAddr(bytes32 _name, address _addr)`: set the primary address associated with a name (similar to an A record in traditional DNS)
* `addr(bytes32 _name) constant returns (address _r)`: get the primary address associated with a name 
* `setContent(bytes32 _name, bytes32 _content)`: if you are the owner of a name, sets its associated content
* `content(bytes32 _name) constant returns (bytes32 _r)`: get the content associated with a name
* `setSubRegistrar(bytes32 _name, address _subRegistrar)`: records the name as referring to a sub-registrar at the given address
* `subRegistrar(bytes32 _name) constant returns (address _r)`: gets the sub-registrar associated with the given name
* `disown(bytes32 _name)`: relinquishes control over a name that you currently control

Events:

* `event Changed(bytes32 indexed name)`: triggered when changed to a domain happen

### Data feeds

The data feed standard is a _templated standard_, ie. in the below descriptions one should be free to replace `<t>` with any desired data type, eg. `uint256`, `bytes32`, `address`, `real192x64`.  

* `get(bytes32 _key) returns (<t> _r)`: get the value associated with a key
* `set(bytes32 _key, <t> _value)`: set the value associated with a key if you are the owner
* `setFee(uint256 _fee)`: sets the fee
* `setFeeCurrency(address _feeCurrency)`: sets the currency that the fee is paid in

The latter two methods are optional; also, note that the fee may be charged either in ether or subcurrency; if the contract charges in ether then the `setFeeCurrency` method is unnecessary.