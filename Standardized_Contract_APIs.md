Although Ethereum allows developers to create absolutely any kind of application without restriction to specific feature types, and prides itself on its "lack of features", there is nevertheless a need to standardize certain very common use cases in order to allow users and applications to more easily interact with each other. This includes sending currency units, registering names, making offers on exchanges, and other similar functions. A standard typically consists of a set of function signatures for a few methods, eg. `send`, `register`, `delete`, providing the set of arguments and their formats in the [Ethereum contract ABI](https://github.com/ethereum/wiki/wiki/Ethereum-Contract-ABI) language.

The standards described below have sample implementations available [here](https://github.com/ethereum/dapp-bin/tree/master/standardized_contract_apis).

All function names are in lower camelCase (eg. `sendCoin`) and all event names are in upper CamelCase (eg. `CoinTransfer`). Input variables are in underscore-prefixed lower camelCase (eg. `_offerId`), and output variables are `_r` for pure getter (ie. constant) functions, `_success` (always boolean) when denoting success or failure, and other values (eg. `_maxValue`) for methods that perform an action but need to return a value as an identifier. Addresses are referred to using `_addr` when generic, and otherwise if a more specific description exists (eg. `_from`, `_to`).

# Transferable Fungibles

Also known as tokens, coins and sub-currencies.

## Token

### Methods

#### transfer
    transfer(uint _value, address _to) returns (bool _success)
Send `_value` amount of coins to address `_to`

#### transferFrom
    trasnferFrom(address _from, uint _value, address _to) returns (bool _success)
Send `_value` amount of coins from address `_from` to address `_to`

#### balanceOf
    balanceOf(address _addr) constant returns (uint _r)
Get the account balance of another account with address `_addr`

---
The `transferFrom` method is used for a "direct debit" workflow, allowing contracts to send coins on your behalf, for example to "deposit" to a contract address and/or to charge fees in sub-currencies; the command should fail unless the `_from` account has deliberately authorized the sender of the message via some mechanism; we propose these standardized APIs for approval:

#### approve
    approve(address _addr) returns (bool _success)
Allow `_addr` to direct debit from your account with full custody. Only implement if absolutely required and use carefully. See `approveOnce` below for a more limited method.

#### unapprove
    unapprove(address _addr) returns (bool _success)
Unapprove address `_addr` to direct debit from your account if it was previously approved. Must reset both one-time and full custody approvals.

#### isApprovedFor
    isApprovedFor(address _target, address _proxy) constant returns (bool _r)
Returns 1 if `_proxy` is allowed to direct debit from `_target`

#### approveOnce
    approveOnce(address _addr, uint256 _maxValue) returns (bool _success)
Makes a one-time approval for `_addr` to send a maximum amount of currency equal to `_maxValue`

#### isApprovedOnceFor
    isApprovedOnceFor(address _target, address _proxy) returns (uint _maxValue)
Returns `_maxValue` if `_proxy` is allowed to direct debit the returned `_maxValue` from address `_target` only once. The approval must be reset on any transfer by `_proxy` of `_maxValue` or less.

### Events
#### Transfer
    Transfer(address indexed from, address indexed to, uint256 value)
Triggered when coins are transferred.

#### AddressApproval
    AddressApproval(address indexed address, address indexed proxy, bool result)
Triggered when an `address` approves `proxy` to direct debit from their account.

#### AddressApprovalOnce
    AddressApprovalOnce(address indexed address, address indexed proxy, uint256 value)
Triggered when an `address` approves `proxy` to direct debit from their account only once for a maximum of `value`

## TF Registries

Token registries contain information about tokens. There is at least one global registry (though other may create more like the global Registry) to which you can add your token. Adding your token to it would increase the experience of the user that the GUI Client can use or not.

#### symbol
    setSymbol(string _s)
    symbol(address _token) constant returns (string)
Sets or returns a short sequence of letters that are used to represent the unit of the coin. When setting, it assumes the `msg.sender` is the token. Solidity string is on UTF-8 format so this should support any character supported by UTF-8. Symbols are chosen by the contract and it's up to the client to decide how to handle different currencies with similar or identical symbols.

Examples or symbols: `USDX`, `BOB$`, `Ƀ`, `% of shares`.

#### name
    setName(string _s)
    name(address _token) constant returns (string)
Sets or returns the name of a token. Solidity string is on UTF-8 format so this should support any character supported by UTF-8. Names are chosen by the contract and it's up to the client to decide how to handle different currencies with similar or identical names.

Examples of names: `e-Dollar`, `BobToken`, `Bitcoin-Eth`.

#### baseUnit
    setBaseUnit(uint _s)
    baseUnit(address _token) constant returns (uint)
Sets or returns the base unit of a token. Although most tokens are displayed to the final user as containing decimal points, token values are unsigned integers counting in the smallest possible unit. The client should always display the total units divided by `baseUnit`. Base units can be any integer but we suggest only using powers of 10. At the moment there is no support for multiple sub-units.

Example: Bob has a balance of 100000 BobTokens, whose base unit is 100. His balance will be displayed on the client as **BOB$100.00**

## Registries

Registries (eg. domain name systems) have the following API:

### Methods
#### reserve
    reserve(string _name) returns (bool _success)
Reserves a name and sets its owner to you if it is not yet reserved.

#### owner
    owner(string _name) constant returns (address _r)
Get the owner of a particular name.

#### transfer
    transfer(string _name, address _newOwner)
Transfer ownership of a name.

#### setAddr
    setAddr(string _name, address _addr)
Set the primary address associated with a name (similar to an A record in traditional DNS.)

#### addr
    addr(string _name) constant returns (address _r)
Get the primary address associated with a name.

#### setContent
    setContent(string _name, bytes32 _content)
If you are the owner of a name, sets its associated content.

#### content
    content(string _name) constant returns (bytes32 _r)
Get the content associated with a name.

#### setSubRegistrar
    setSubRegistrar(string _name, address _subRegistrar)
Records the name as referring to a sub-registrar at the given address.

#### subRegistrar
    subRegistrar(string _name) constant returns (address _r)
Gets the sub-registrar associated with the given name.

#### disown
    disown(string _name)
Relinquishes control over a name that you currently control.

### Events

#### Changed
    event Changed(string name, bytes32 indexed __hash_name)
Triggered when changed to a domain happen.


## Data feeds

The data feed standard is a _templated standard_, ie. in the below descriptions one should be free to replace `<t>` with any desired data type, eg. `uint256`, `bytes32`, `address`, `real192x64`.

### Methods
#### get
    get(bytes32 _key) returns (<t> _r)
Get the value associated with a key.

#### set
    set(bytes32 _key, <t> _value)
Set the value associated with a key if you are the owner.

#### setFee
    setFee(uint256 _fee)
Sets the fee.

#### setFeeCurrency
    setFeeCurrency(address _feeCurrency)
Sets the currency that the fee is paid in

The latter two methods are optional; also, note that the fee may be charged either in ether or subcurrency; if the contract charges in ether then the `setFeeCurrency` method is unnecessary.


## Forwarding contracts (eg. multisig)

Forwarding contracts will likely work very differently depending on what the authorization policy of each one is. However, there are some standard workflows that should be used as much as possible:

### Methods
#### execute
    execute(address _to, uint _value, bytes _data) returns (bytes32 _id)
Create a message with the desired recipient, value and data. Returns a "pending ID" for the transaction.

#### confirm
    confirm(bytes32 _id) returns (bool _success)
Confirm a pending message with a particular ID using your account; returns success or failure. If enough confirmations are made, sends the message along.

Access policies can be of any form, eg. multisig, an arbitrary CNF boolean formula, a scheme that depends on the _value_ or _contents_ of a transaction, etc.