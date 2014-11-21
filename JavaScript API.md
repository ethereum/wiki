# Introduction

To make your ÐApp work with on Ethereum, you'll need to know about the Ethereum Javascript bindings, or, if you like, magic Javascript objects. These bindings may be used with AlethZero, Mist and external browser. These bindings can be found at [ethereum.js](https://github.com/ethereum/ethereum.js) repository.

There is, at the global scope, one objects; the `web3` object, containing data handling functions (commonly used for all other APIs). This object also contains other subprotocol objects including the `eth` object - `web3.eth` (for specifically Ethereum interaction) and the `shh` object - `web3.shh` (for Whisper interaction). Over time we'll introduce other objects for each of the other web3 protocols.

# API

* [web3](#web3)
  * [sha3](#web3.sha3) *(_s1, [_s2], [_s3])*
  * [eth](#eth)
    * [coinbase](#coinbase)
    * [listening](#listening)
    * [mining](#mining)
    * [gasPrice](#gasPrice)
    * [accounts](#accounts)
    * [peerCount](#peerCount)
    * [defaultBlock](#defaultBlock)
    * [number](#number)
    * [balanceAt (_address)](#balanceAt)
    * [stateAt (_address, _storage)](#stateAt)
    * [storageAt (_address)](#storageAt)
    * [countAt (_address)](#countAt)
    * [codeAt (_address)](#codeAt)
    * [transact (_object)](#transact)
    * [block (_hash/_number)](#block)
    * [transaction (_object, _number)](#transaction)
    * [uncle (_hash/number)](#uncle)
    * [compilers ()](#compilers)
    * [lll (_string)](#lll)
    * [solidity (_object)](#solidity)
    * [serpent (_object)](#serpent)
    * [logs (_object/_string)](#logs)
    * [watch (_object/_string)](#eth_watch)
      * [arrived (_callback)](#eth_arrived)
      * [changed (_callback)](#eth_changed)
      * [logs (_callback)](#eth_logs)
      * [uninstall (_callback)](#eth_uninstall)
  * [db](#db)
    * [put (_name, _key, _value)](#put)
    * [putString (__name, _key, _value)](#putString)
    * [get (_name, _key)](#get)
    * [getString (_name, _key)](#getString)
  * [shh](#shh)
    * [post (_object)](#post)
    * [newIdentity ()](#newIdentity)
    * [haveIdentity (_string)](#haveIdentity)
    * [newGroup (_id, _who)](#newGroup)
    * [addToGroup (_group, _who)](#addToGroup)
    * [watch (_object/_string)](#shh_watch)
      * [arrived (_callback)](#shh_arrived)
      * [changed (_callback)](#shh_changed)
      * [messages (_callback)](#shh_messages)
      * [uninstall (_callback)](#shh_uninstall)

# Parameters

Parameters are always data represented as hex, prefixed with an `0x`. There's automatic conversion from decimal strings to the hex representation (interpreted as a big-endian as is standard for Ethereum). So, the following two forms are identically interpreted:

* `"0x414243"`
* `"4276803"`

In each case, they are interpreted as the number 4276803. To convert to or from other datatypes, there are a number of conversion functions, detailed later.

##### web3
The `web3` object can be used for general data handling.
```javascript
var web3 = require('web3')
```

##### web3.sha3
Returns the SHA3 of the given data.
```javascript
// TODO (_s), (_s1, _s2), (_s1, _s2, _s3)
```

##### web3.toAscii
Returns an ASCII string made from the data `_s`.
```javascript
var str = web3.toAscii("0x657468657265756d000000000000000000000000000000000000000000000000");
console.log(str); // ethereum
```


##### web3.fromAscii
Returns data of the ASCII string `_s`, auto-padded to `_padding` bytes (default to 32) and left-aligned.
```javascript
var str = web3.fromAscii('ethereum');
var str2 = web3.fromAscii('ethereum', 32);
console.log(str); // "0x657468657265756d000000000000000000000000000000000000000000000000"
console.log(str2); // "0x657468657265756d000000000000000000000000000000000000000000000000"
```

##### web3.toDecimal
Returns the decimal string representing the data `_s` (when interpreted as a big-endian integer).
```javascript
var value = web3.toDecimal('0x15');
console.log(value); // 21
```

##### web3.toFixed
Returns the floating-point number representing the data `_s` (when interpreted as a fixed-point value divided by 2^128).
```javascript
// TODO (_s)
```

##### web3.fromFixed
Returns data representing the floating-point number `_s` (when interpreted as a fixed-point value divided by 2^128).
```javascript
// TODO (_s)
```

##### web3.offset
Returns data representing the data `_s` when its numerical value is offset by the integer `_o`. e.g. `dev.offset("0x10", 10)` evaluates to `"0x1a"`.
```javascript
// TODO (_s, _o)
```

### eth

**Properties**: All properties getters are asynchronous methods, returning promise 

* `coinbase` Returns the coinbase address of the client.
* `listening` Returns true if and only if the client is actively listening for network connections.
* `mining` Returns true if and only if the client is actively mining new blocks.
* `gasPrice` Returns the special 256-bit number equal to the hard-coded testnet price of gas.
* `accounts` Returns the special key-pair list object corresponding to the address of each of the accounts owned by the client that this ÐApp has access to.
* `peerCount` Returns the number of peers currently connected to the client.
* `defaultBlock` The default block number/age to use when querying state. When positive this is a block number, when 0 or negative it is a block age. -1 therefore means the most recently mined block, 0 means the block being currently mined (i.e. to include pending transactions). Defaults to -1.
* `number` Returns the number of the most recent block.

**Methods**: For each such item, promise is returned. Promise is being resolved with single parameter, which is result of the call.

* `balanceAt(_a)` Returns the balance of the account of address given by the address `_a`. For example, `eth.toDecimal(eth.balanceAt('0x1d916bed61249f6c12f3ca8b3f78b8f4cedbe24b'))` will return the balance (in Wei) for that account's address.
* `stateAt(_a, _s)` Returns the value in storage at position given by the string `_s` of the account of address given by the address `_a`.
* `storageAt(_a)` Dumps storage as json object.
* `countAt(_a)` Returns the number of transactions send from the account of address given by `_a`.
* `codeAt(_a)` Returns true if the account of address given by `_a` is a contract-account.

The block number you wish to query can be given either as an extra parameter (or age if less than 1: you may use 0 to include pending transactions, use -1 to include only mined transactions &c.), or alternatively, you may use these without the extra parameter, in which case the state at the end of the most recently mined block will be used. This can be altered with the `defaultBlock` property.

**Blockchain Getters** Three distinct methods are available for querying the blockchain and retrieving blocks, transactions and uncles. For each such item, promise is returned.

* `block(_number)` Returns the block with number `_number`. Return value is an object with the following keys:
  * `hash`: The block hash (i.e. the SHA3 of the RLP-encoded dump of the block's header). A 32-byte hash.
  * `parentHash`: The parent block's hash (i.e. the SHA3 of the RLP-encoded dump of the parent block's header). A 32-byte hash.
  * `sha3Uncles`: The SHA3 of the RLP-encoded dump of the uncles portion of the block (a 32-byte hash).
  * `miner`: The address of the account that was rewarded for mining this block (né the coinbase address). A 20-byte address.
  * `stateRoot`: The root of the state trie (a 32-byte hash).
  * `transactionsRoot`: The root of the block's transactions trie (a 32-byte hash).
  * `difficulty`: The PoW difficulty of this block (a big int).
  * `number`: The number of this block (an integer).
  * `minGasPrice`: The minimum price, in Wei, of one GAS, that the miner accepted for any transactions in this block (a big int).
  * `gasLimit`: The gas limit of this block (an integer).
  * `gasUsed`: The amount of gas used in this block (an integer).
  * `timestamp`: The timestamp of this block (an integer).
  * `extraData`: Any extra data this block contains (a byte array).
  * `nonce`: The block's PoW nonce (a 32-byte hash).
  * `children`: The hashes of any children this block has (an array of 32-byte hashes)
  * `totalDifficulty`: The total difficulty of the entire chain up and including this block (a bigint).
  * `bloom`: The bloom filter of this block (a 32-byte hash).
* `block(_hash)` Returns the block with hash `_hash`. Return value is an object with the keys as for the previous call.
* `transaction(_number, _i)` Returns the transaction number `_i` from block with number `_number`. Return value is an object with the following keys:
  * `hash`: The hash of the transaction. A 32-byte hash.
  * `input`: The binary data that formed the input to the transaction, either the input data if it was a message call or the contract initialisation if it was a contract creation. A byte array.
  * `to`: The address to which the transaction was sent. This may be the null address (address 0) if it was a contract-creation transaction (a 20-byte address).
  * `from`: The cryptographically verified address from which the transaction was sent (a 20-byte address).
  * `gas`: The amount of GAS supplied for this transaction to happen (an integer).
  * `gasPrice`: The price offered to the miner to purchase this amount of GAS, in Wei/GAS (a big int).
  * `nonce`: The transaction nonce (an integer).
  * `value`: The amount of ETH to be transferred to the recipient with the transaction (a big int).
* `transaction(_hash, _i)` Returns the transaction number `_i` from block with hash `_hash`. Return value is an object with the keys as for the previous call.
* `uncle(_number, _i)` Returns the uncle number `_i` from block with number `_number`. Return value is an object with the following keys:
  * `hash`: The block hash (i.e. the SHA3 of the RLP-encoded dump of the block's header). A 32-byte hash.
  * `parentHash`: The parent block's hash (i.e. the SHA3 of the RLP-encoded dump of the parent block's header). A 32-byte hash.
  * `sha3Uncles`: The SHA3 of the RLP-encoded dump of the uncles portion of the block (a 32-byte hash).
  * `miner`: The address of the account that was rewarded for mining this block (né the coinbase address). A 20-byte address.
  * `stateRoot`: The root of the state trie (a 32-byte hash).
  * `transactionsRoot`: The root of the block's transactions trie (a 32-byte hash).
  * `difficulty`: The PoW difficulty of this block (a big int).
  * `number`: The number of this block (an integer).
  * `minGasPrice`: The minimum price, in Wei, of one GAS, that the miner accepted for any transactions in this block (a big int).
  * `gasLimit`: The gas limit of this block (an integer).
  * `gasUsed`: The amount of gas used in this block (an integer).
  * `timestamp`: The timestamp of this block (an integer).
  * `extraData`: Any extra data this block contains (a byte array).
  * `nonce`: The block's PoW nonce (a 32-byte hash).
* `uncle(_hash, _i)` Returns the uncle number `_i` from block with hash `_hash`. Return value is an object with the keys as for the previous call.

**Transactions**

* `transact(_params, _fn)` Creates a new message-call transaction.
  * `_params`, an anonymous object specifying the parameters of the transaction.
    * `from`, the address for the sending account;
    * `value`, the value transferred for the transaction (in Wei), also the endowment if it's a contract-creation transaction;
    * `endowment`, synonym for `value`;
    * `to`, the destination address of the message, left undefined for a contract-creation transaction;
    * `data`, either a [byte string](https://github.com/ethereum/wiki/wiki/Solidity,-Docs-and-ABI) containing the associated data of the message, or in the case of a contract-creation transaction, the initialisation code;
    * `code`, a synonym for `data`;
    * `gas`, the amount of gas to purchase for the transaction (unused gas is refunded), defaults to the most gas your ether balance allows; and
    * `gasPrice`, the price of gas for this transaction, defaults to the mean network gasPrice.
  * `_fn`, the callback function, called on completion of the transaction. If the transaction was a contract-creation transaction, it is passed with a single argument; the address of the new account.
* `call(_params, _fn)` Executes a new message-call immediately without creating a transaction on the block chain.
  * `_params`, an anonymous object specifying the parameters of the transaction, similar to that above.
  * `_fn`, the callback function, called on completion of the message call. A single argument is passed equal to the output data of the message call.

**Watches and Log Filtering** Past messages may be filtered and their attributes inspected, and future messages (and the changes they implicitly bring) may be notified of.

* `logs(_filter)`: Returns the list of log entries in Ethereum matching the given `_filter`. The filter is an object including fields:
  * `earliest`: The number of the earliest block (-1 may be given to mean the most recent, currently mining, block).
  * `latest`: The number of the latest block (-1 may be given to mean the most recent, currently mining, block).
  * `max`: The maximum number of messages to return.
  * `skip`: The number of messages to skip before the list is constructed. May be used with `max` to paginate messages into multiple calls.
  * `address`: An address or a list of addresses to restrict log entries by requiring them to be made from a particular account.
  * `topic`: A set of values which must each appear in the log entries.
  * Returns a list of log entries; each includes the following fields:
    * `address`: The address of the account whose execution of the message resulted in the log entry being made.
    * `topics`: The topic(s) of the message.
    * `data`: The associated data of the message.
* `watch(_filter)`: Creates a watch object to notify when the state changes in a particular way, given by `_filter`. Filter may be a log filter object, as defined above. It may also be either `'chain'` or `'pending'` to watch for changes in the chain or pending transactions respectively. Returns a watch object with the following methods:
  * `changed(_f)`: Installs a handler, `_f`, which is called when the state changes due to messages that fit `_filter`.
  * `logs()`: Returns the log entries that fit `_filter`.
  * `uninstall()`: Uninstalls the watch. Should always be called once it is done with.

**Ethereum Misc** The `eth` object contains an additional method for compilation. This has an asynchronous version in fitting with the getter methods, which is prefixed with do and takes an additional function which is called with the result. e.g. `doLll(_s, _f)`.

* `lll(_s)`: Compiles the LLL source code `_s` and returns the output data.

## Example

A simple HTML snippet that will display the user's primary account balance of Ether:
```html
<div>You have <span id="ether">?</span> Weis</div>
<script>
web3.eth.watch({altered: web3.eth.coinbase}).changed(function() {
    web3.eth.balanceAt(web3.eth.coinbase).then(function (balance) {
        document.getElementById("ether").innerText = web3.toDecimal(balance);
    });
});
</script>
```

To test it, just put `<html><body>` before it and `</body></html>` after, then save the file. Load it in AlethZero and point the URL to file:///WHEREVER_YOU_SAVED_IT

Job done. Now go create.

### Recent Changes
- Moved "Misc" into dev.* object.
- Removed all secret keys from the JS API.
- Altered naming to web3.

### Upcoming Changes
- Add web3.p2p.* and web3.shh.* objects.
- Proscribe particular bigint objects for numerical manipulation.
- Integrate Paperscript-style preprocessing to allow for operator overloading.