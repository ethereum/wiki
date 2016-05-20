**NOTE** The token API is currently debated as an ERC (Ethereum request for comment) and may be outdated: https://github.com/ethereum/EIPs/issues/20

***

Although Ethereum allows developers to create absolutely any kind of application without restriction to specific feature types, and prides itself on its "lack of features", there is nevertheless a need to standardize certain very common use cases in order to allow users and applications to more easily interact with each other. This includes sending currency units, registering names, making offers on exchanges, and other similar functions. A standard typically consists of a set of function signatures for a few methods, eg. `send`, `register`, `delete`, providing the set of arguments and their formats in the [Ethereum contract ABI](https://github.com/ethereum/wiki/wiki/Ethereum-Contract-ABI) language.

The standards described below have sample implementations available [here](https://github.com/ethereum/dapp-bin/tree/master/standardized_contract_apis).

All function names are in lower camelCase (eg. `sendCoin`) and all event names are in upper CamelCase (eg. `CoinTransfer`). Input variables are in underscore-prefixed lower camelCase (eg. `_offerId`), and output variables are `_r` for pure getter (ie. constant) functions, `_success` (always boolean) when denoting success or failure, and other values (eg. `_maxValue`) for methods that perform an action but need to return a value as an identifier. Addresses are referred to using `_address` when generic, and otherwise if a more specific description exists (eg. `_from`, `_to`).

## Transferable Fungibles (see [ERC 20](https://github.com/ethereum/EIPs/issues/20) for the latest)

Also known as tokens, coins and sub-currencies.


## TF Registries (see [ERC 22](https://github.com/ethereum/EIPs/issues/22) for the latest)

Token registries contain information about tokens. There is at least one global registry (though other may create more like the global Registry) to which you can add your token. Adding your token to it would increase the experience of the user that the GUI Client can use or not.

## Registries

Registries (eg. domain name systems) have the following API:

### Methods
#### reserve

```js
reserve(string _name) returns (bool _success)
```
Reserves a name and sets its owner to you if it is not yet reserved.

#### owner

```js
owner(string _name) constant returns (address _r)
```
Get the owner of a particular name.

#### transfer

```js
transfer(string _name, address _newOwner)
```
Transfer ownership of a name.

#### setAddr

```js
setAddr(string _name, address _address)
```

Set the primary address associated with a name (similar to an A record in traditional DNS.)

#### addr

```js
addr(string _name) constant returns (address _r)
```
Get the primary address associated with a name.

#### setContent

```js
setContent(string _name, bytes32 _content)
```

If you are the owner of a name, sets its associated content.

#### content

```js
content(string _name) constant returns (bytes32 _r)
```

Get the content associated with a name.

#### setSubRegistrar

```js
setSubRegistrar(string _name, address _subRegistrar)
```

Records the name as referring to a sub-registrar at the given address.

#### subRegistrar

```js
subRegistrar(string _name) constant returns (address _r)
```
Gets the sub-registrar associated with the given name.

#### disown

```js
disown(string _name)
```

Relinquishes control over a name that you currently control.

### Events

#### Changed

```js
event Changed(string name, bytes32 indexed __hash_name)
````

Triggered when changed to a domain happen.


## Data feeds

The data feed standard is a _templated standard_, ie. in the below descriptions one should be free to replace `<t>` with any desired data type, eg. `uint256`, `bytes32`, `address`, `real192x64`.

### Methods
#### get

```js
get(bytes32 _key) returns (<t> _r)
```

Get the value associated with a key.

#### set

```js
set(bytes32 _key, <t> _value)
```

Set the value associated with a key if you are the owner.

#### setFee

```js
setFee(uint256 _fee)
```

Sets the fee.

#### setFeeCurrency

```js
setFeeCurrency(address _feeCurrency)
```

Sets the currency that the fee is paid in

The latter two methods are optional; also, note that the fee may be charged either in ether or subcurrency; if the contract charges in ether then the `setFeeCurrency` method is unnecessary.


## Forwarding contracts (eg. multisig)

Forwarding contracts will likely work very differently depending on what the authorization policy of each one is. However, there are some standard workflows that should be used as much as possible:

### Methods
#### execute

```js
execute(address _to, uint _value, bytes _data) returns (bytes32 _id)
```

Create a message with the desired recipient, value and data. Returns a "pending ID" for the transaction.

#### confirm

```js
confirm(bytes32 _id) returns (bool _success)
```

Confirm a pending message with a particular ID using your account; returns success or failure. If enough confirmations are made, sends the message along.

Access policies can be of any form, eg. multisig, an arbitrary CNF boolean formula, a scheme that depends on the _value_ or _contents_ of a transaction, etc.