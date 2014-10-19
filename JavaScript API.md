To make your ÐApp work with on Ethereum, you'll need to know about the Ethereum Javascript bindings, or, if you like, magic Javascript objects.

There are currently only three such objects; the `dev` object, containing data handling functions (commonly used for all other APIs), the `eth` object (for specifically Ethereum interaction) and the `shh` object for Whisper interaction, however over time we'll introduce other objects for each of the other ÐΞVp2p protocols.

### Parameters

Parameters are always data represented as hex, prefixed with an `0x`. There's automatic conversion from decimal strings to the hex representation (interpreted as a big-endian as is standard for Ethereum). So, the following two forms are identically interpreted:

* `"0x414243"`
* `"4276803"`

In each case, they are interpreted as the number 4276803. To convert to or from other datatypes, there are a number of conversion functions, detailed later.

### dev

**Data Handling**: The `dev` object can be used for general data handling. It contains the following methods:

* `sha3(_s)`: Returns the SHA3 of the given data.
* `sha3(_s1, _s2)`: Returns the SHA3 of the given data when concatenated.
* `sha3(_s1, _s2, _s3)`: Returns the SHA3 of the given data when concatenated.
* `toAscii(_s)`: Returns an ASCII string made from the data `_s`.
* `fromAscii(_s, _padding = 32)`: Returns data of the ASCII string `_s`, auto-padded to `_padding` bytes (default to 32) and left-aligned.
* `toDecimal(_s)`: Returns the decimal string representing the data `_s` (when interpreted as a big-endian integer).
* `toFixed(_s)`: Returns the floating-point number representing the data `_s` (when interpreted as a fixed-point value divided by 2^128).
* `fromFixed(_s)`: Returns data representing the floating-point number `_s` (when interpreted as a fixed-point value divided by 2^128).
* `offset(_s, _o)`: Returns data representing the data `_s` when its numerical value is offset by the integer `_o`. e.g. `dev.offset("0x10", 10)` evaluates to `"0x1a"`.

### eth

**Properties**: For each such item, there is also an asynchronous method, taking a parameter of the callback function, itself taking a single parameter of the property's return value and of the same name but prefixed with get and recapitalised, e.g. `getCoinbase(_fn)`.

* `coinbase` Returns the coinbase address of the client.
* `listening` Returns true if and only if the client is actively listening for network connections.
* `mining` Returns true if and only if the client is actively mining new blocks.
* `gasPrice` Returns the special 256-bit number equal to the hard-coded testnet price of gas.
* `accounts` Returns the special key-pair list object corresponding to the address of each of the accounts owned by the client that this ÐApp has access to.
* `peerCount` Returns the number of peers currently connected to the client.
* `defaultBlock` The default block number/age to use when querying state. When positive this is a block number, when 0 or negative it is a block age. -1 therefore means the most recently mined block, 0 means the block being currently mined (i.e. to include pending transactions). Defaults to -1.
* `number` Returns the number of the most recent block.

**Synchronous Getters**: For each such item, there is also an asynchronous method, taking an additional parameter of the callback function, itself taking a single parameter of the synchronous method's return value and of the same name but prefixed with get and recapitalised, e.g. `getBalanceAt(_a, _fn)`.

* `balanceAt(_a)` Returns the balance of the account of address given by the address `_a`. For example, `eth.toDecimal(eth.balanceAt('0x1d916bed61249f6c12f3ca8b3f78b8f4cedbe24b'))` will return the balance (in Wei) for that account's address.
* `stateAt(_a, _s)` Returns the value in storage at position given by the string `_s` of the account of address given by the address `_a`.
* `countAt(_a)` Returns the number of transactions send from the account of address given by `_a`.
* `codeAt(_a)` Returns true if the account of address given by `_a` is a contract-account.

The block number you wish to query can be given either as an extra parameter (or age if less than 1: you may use 0 to include pending transactions, use -1 to include only mined transactions &c.), or alternatively, you may use these without the extra parameter, in which case the state at the end of the most recently mined block will be used. This can be altered with the `defaultBlock` property.

**Transactions**

* `transact(_params, _fn)` Creates a new message-call transaction.
  * `_params`, an anonymous object specifying the parameters of the transaction.
    * `from`, the address for the sending account;
    * `value`, the value transferred for the transaction (in Wei), also the endowment if it's a contract-creation transaction;
    * `endowment`, synonym for `value`;
    * `to`, the destination address of the message, left undefined for a contract-creation transaction;
    * `data`, either a byte array or an array of values, to be 32-byte aligned, containing the associated data of the message, or in the case of a contract-creation transaction, the initialisation code;
    * `code`, a synonym for `data`;
    * `gas`, the amount of gas to purchase for the transaction (unused gas is refunded), defaults to the most gas your ether balance allows; and
    * `gasPrice`, the price of gas for this transaction, defaults to the mean network gasPrice.
  * `_fn`, the callback function, called on completion of the transaction. If the transaction was a contract-creation transaction, it is passed with a single argument; the address of the new account.
* `call(_params, _fn)` Executes a new message-call immediately without creating a transaction on the block chain.
  * `_params`, an anonymous object specifying the parameters of the transaction, similar to that above.
  * `_fn`, the callback function, called on completion of the message call. A single argument is passed equal to the output data of the message call.

**The Blockchain** Three distinct methods are available for querying the blockchain and retrieving blocks, transactions and uncles.

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

**Watches and Message Filtering** Past messages may be filtered and their attributes inspected, and future messages (and the changes they implicitly bring) may be notified of.

* `messages(_filter)`: Returns the list of messages in Ethereum matching the given `_filter`. The filter is an object including fields:
  * `earliest`: The number of the earliest block (-1 may be given to mean the most recent, currently mining, block).
  * `latest`: The number of the latest block (-1 may be given to mean the most recent, currently mining, block).
  * `max`: The maximum number of messages to return.
  * `skip`: The number of messages to skip before the list is constructed. May be used with `max` to paginate messages into multiple calls.
  * `from`: Either an address or a list of addresses to restrict messages by requiring them to be made from a particular account.
  * `to`: Either an address or a list of addresses to restrict messages by requiring them to be made to a particular account.
  * `altered`: Either an address, or an address/location object, or a mixed list of such values, to restrict messages by requiring them to have made an alteration, respectively either to an account, a particular contract's storage location, or one of a number of these. An address/location object is an object that contains two fields:
    * `id`: The address of the contract.
    * `at`: The location of a point in contract's storage.
  * Returns a list of past message objects; each includes the following fields:
    * `from`: The sender address of the message.
    * `to`: The recipient address of the message (or '' if it was a contract-creation message).
    * `input`: The input data to the message (the initialisation code if it was a contract-creation message).
    * `output`: The output data of the message (the body code if it was a contract-creation message).
    * `value`: The value associated with the message (in Wei, the endowment if it was a contract-creation message). [ C++ : TODO ]
    * `path`: The call-path of the message. The first entry is the transaction index into the block. The second, if there is one, is the index of the message within the transaction, and so on.
    * `origin`: The original sender of the transaction.
    * `timestamp`: The timestamp of the block in which the message takes place.
    * `coinbase`: The coinbase of the block in which the message takes place.
    * `block`: The hash of the block in which the message takes place.
    * `number`: The number of the block in which the message takes place.
* `watch(_filter)`: Creates a watch object to notify when the state changes in a particular way, given by `_filter`. Filter may be a message filter object, as defined above. It may also be either `'chain'` or `'pending'` to watch for changes in the chain or pending transactions respectively. Returns a watch object with the following methods:
  * `changed(_f)`: Installs a handler, `_f`, which is called when the state changes due to messages that fit `_filter`.
  * `messages()`: Returns the messages that fit `_filter`.
  * `uninstall()`: Uninstalls the watch. Should always be called once it is done with.

**Ethereum Misc** The `eth` object contain two additional methods for compilation and key management.

* `lll(_s)`: Compiles the LLL source code `_s` and returns the output data.

## Example

A simple HTML snippet that will display the user's primary account balance of Ether:
```html
<div>You have <span id="ether">?</span>.</div>
<script>
eth.watch({altered: eth.secretToAddress(eth.key)}).changed(function() {
    document.getElementById("ether").innerText = dev.toDecimal(eth.balanceAt(eth.accounts[0]))
});
</script>
```

To test it, just put `<html><body>` before it and `</body></html>` after, then save the file. Load it in AlethZero and point the URL to file:///WHEREVER_YOU_SAVED_IT

Job done. Now go create.

### Recent Changes
- Moved "Misc" into dev.* object.
- Removed all secret keys from the JS API.

### Upcoming Changes
- Add p2p.* and shh.* objects.
- Proscribe particular bigint objects for numerical manipulation.
- Integrate Paperscript-style preprocessing to allow for operator overloading.