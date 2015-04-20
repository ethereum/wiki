# Introduction

To make your Ðapp work with on Ethereum, you can use the `web3` object provided by the [web3.js library](https://github.com/ethereum/web3.js). Under the hood it communicates to a local node through [RPC calls](https://github.com/ethereum/wiki/wiki/JSON-RPC). web3.js works with AlethZero, geth and Mist, and also in an external browser if one of the former nodes are running locally.

`web3` contains the `eth` object - `web3.eth` (for specifically Ethereum blockchain interactions) and the `shh` object - `web3.shh` (for Whisper interaction). Over time we'll introduce other objects for each of the other web3 protocols.

## Using callbacks

As this API is designed to work with a local RPC node and all its functions are by default use synchronous HTTP requests.

If you want to make asynchronous request, you can pass an optional callback as the last parameter to most functions.
All callbacks are using an [error first callback](http://fredkschott.com/post/2014/03/understanding-error-first-callbacks-in-node-js/) style:

```js
web3.eth.getBlock(48, function(error, result){
    if(!error)
        console.log(result)
    else
        console.error(error);
})
```

## A note on big numbers in JavaScript

You will always get a BigNumber object for balance values as JavaScript is not able to handle big numbers correctly.
Look at the following examples:

```js
"101010100324325345346456456456456456456"
// "101010100324325345346456456456456456456"
101010100324325345346456456456456456456
// 1.0101010032432535e+38
```

web3.js depends on the [BigNumber Library](https://github.com/MikeMcl/bignumber.js/) and adds it automatically.

```js
var balance = new BigNumber('131242344353464564564574574567456');
// or var balance = web3.eth.getBalance(someAddress);

balance.plus(21).toString(10); // toString(10) converts it to a number string
// "131242344353464564564574574567477"
```

The next example wouldn't work as we have more than 20 floating points, therefore it is recommended to keep you balance always in *wei* and only transform it to other units when presenting to the user:
```js
var balance = new BigNumber('13124.234435346456466666457455567456');

balance.plus(21).toString(10); // toString(10) converts it to a number string, but can only show max 20 floating points 
// "13145.23443534645646666646" // you number would be cut after the 20 floating point
```

# API

* [web3](#web3)
  * [version](#web3versionapi)
     * [api](#web3versionapi) -> string e.g. '0.2.0' (the web3.js version)
     * [client](#web3versionclient) -> string e.g. 'AlethZero/1.0.0' (the client ID)
     * [network](#web3versionnetwork) -> number e.g. 58 (the network version)
     * [ethereum](#web3versionethereum) -> number e.g. 60 (the ethereum protocol version)
     * [whisper](#web3versionwhisper) -> number e.g. 20 (the shh version)
  * [port](#) -> number e.g. 8080 (not available yet)
  * [setProvider(provider)](#web3setprovider)
  * [reset()](#web3reset)
  * [sha3(string)](#web3sha3) -> hexString
  * [toHex(stringOrNumber)](#web3tohex) -> textString
  * [toAscii(hexString)](#web3toascii) -> textString
  * [fromAscii(textString, [padding])](#web3fromascii) -> hexString
  * [toDecimal(hexString)](#web3todecimal) -> number
  * [fromDecimal(number)](#web3fromdecimal) -> hexString
  * [fromWei(numberStringOrBigNumber, unit)](#web3fromwei) -> string|BigNumber (depending on the input)
  * [toWei(numberStringOrBigNumber, unit)](#web3toWei) -> string|BigNumber (depending on the input)
  * [toBigNumber(numberOrHexString)](#web3tobignumber) -> BigNumber
  * [isAddress(hexString)](#web3isAddress) -> boolean
  * [net](#)
    * [listening](#web3netlistening) -> boolean
    * [peerCount](#web3ethpeercount) -> Integer
  * [eth](#web3eth)
    * [coinbase](#web3ethcoinbase) -> hexString
    * [gasPrice](#web3ethgasprice) -> BigNumber
    * [accounts](#web3ethaccounts) -> array of hexStrings
    * [mining](#web3ethmining) -> boolean
    * [register(hexString)](#web3ethregister)
    * [unRegister(hexString)](#web3ethunregister)
    * [defaultBlock](#web3ethdefaultblock) -> Integer
    * [blockNumber](#web3ethblocknumber) -> Integer
    * [getBalance(address)](#web3ethgetbalance) -> BigNumber
    * [getStorageAt(address, position)](#web3ethgetstorageat) -> hexString
    * [getCode(address)](#web3ethgetcode) -> hexString
    * [getBlock(hash/number)](#web3ethgetblock) -> headerObject
    * [getBlockTransactionCount(hash/number)](#web3ethgetblocktransactioncount) -> Integer
    * [getUncle(hash/number)](#web3ethgetuncle) -> headerObject
    * [getBlockUncleCount(hash/number)](#web3ethgetblockunclecount) -> Integer
    * [getTransaction(hash)](#web3ethgettransaction) -> transactionObject
    * [getTransactionFromBlock(hashOrNumber, indexNumber)](#web3ethgettransactionfromblock) -> transactionObject
    * [getTransactionCount(address)](#web3ethgettransactioncount) -> Integer
    * [sendTransaction(object)](#web3ethsendtransaction)
    * [contract(abiArray)](#web3ethcontract) -> contractObject
    * [call(object)](#web3ethcall) -> hexString
    * [filter(array (, options) )](#web3ethfilter)
        - [watch(callback)](#web3ethfilter)
        - [stopWatching(callback)](#web3ethfilter)
        - [get()](#web3ethfilter)
    * [getCompilers()](#web3ethgetcompilers) -> array of strings
    * [compile.lll(string)](#web3ethcompilelll) -> hexString
    * [compile.solidity(string)](#web3ethcompilesolidity) -> hexString
    * [compile.serpent(string)](#web3ethcompileserpent) -> hexString
    * [flush](#web3ethflush)
  * [db](#web3db)
    * [putString(name, key, value)](#web3dbputstring)
    * [getString(name, key)](#web3dbgetstring)
    * [putHex(name, key, value)](#web3dbputhex)
    * [getHex(name, key)](#web3dbgethex)
  * [shh](#web3shh)
    * [post(postObject)](#web3shhpost)
    * [newIdentity()](#web3shhnewidentity)
    * [hasIdentity(hexString)](#web3shhhaveidentity)
    * [newGroup(_id, _who)](#web3shhnewgroup)
    * [addToGroup(_id, _who)](#web3shhaddtogroup)
    * [filter(object/string)](#web3shhfilter)
      * [watch(callback)](#web3shhfilter)
      * [stopWatching(callback)](#web3shhfilter)
      * [get(callback)](#web3shhfilter)

# Usage

#### web3
The `web3` object provides all methods.

##### Example

```js
var web3 = require('web3')
```

***

#### web3.version.api

    web3.version.api

##### Returns

`String` - The ethereum js api version.

##### Example

```js
var version = web3.version.api;
console.log(api); // "0.2.0"
```

***

#### web3.version.client

    web3.version.client

##### Returns

`String` - The client/node version.

##### Example

```js
var version = web3.version.client;
console.log(version); // "Mist/v0.9.3/darwin/go1.4.1"
```

***

#### web3.version.network

    web3.version.network


##### Returns

`String` - The network protocol version.

##### Example

```js
var version = web3.version.network;
console.log(version); // 54
```

***

#### web3.version.ethereum

    web3.version.ethereum


##### Returns

`String` - The ethereum protocol version.

##### Example

```js
var version = web3.version.ethereum;
console.log(version); // 60
```

***

#### web3.version.whisper

    web3.version.whisper


##### Returns

`String` - The whisper protocol version.

##### Example

```js
var version = web3.version.whisper;
console.log(version); // 20
```

***

#### web3.setProvider

    web3.setProvider(providor)

Should be called to set provider.

##### Parameters
none

##### Returns

`undefined`

##### Example

```js
web3.setProvider(new web3.providers.HttpProvider('http://localhost:8545')); // 8080 for cpp/AZ, 8545 for go/mist
// or
web3.setProvider(new web3.providers.QtSyncProvider());
```

***

#### web3.reset

    web3.reset()

Should be called to reset state of web3. Resets everything except manager. Uninstalls all filters. Stops polling.

##### Parameters
none

##### Returns

`undefined`

##### Example

```js
web3.reset();
```

***

#### web3.sha3

    web3.sha3(string [, callback])

##### Parameters

1. `String` - The string to hash using the SHA3 algorithm
2. `Function` - (optional) If you pass a callback the HTTP request is made asynchronous. See [this note](#using-callbacks) for details.


##### Returns

`String` - The SHA3 of the given data.

##### Example

```js
var str = web3.sha3("Some ASCII string to be hashed");
console.log(str); // "0x536f6d6520415343494920737472696e6720746f20626520686173686564"

var hash = web3.sha3(str);
console.log(hash); // "0xb21dbc7a5eb6042d91f8f584af266f1a512ac89520f43562c6c1e37eab6eb0c4"
```

***

#### web3.toHex

    web3.toHex(mixed);

Converts any value into HEX.

##### Parameters

1. `String|Number|Object|Array|BigNumber` - The value to parse to HEX. If its an object or array it will be `JSON.stringify` first. If its a BigNumber it will make it the HEX value of a number.

##### Returns

`String` - The hex string of `mixed`.

##### Example

```js
var str = web3.toHex({test: 'test'});
console.log(str); // '0x7b2274657374223a2274657374227d'
```

***

#### web3.toAscii

    web3.toAscii(hexString);

Converts a HEX string into a ASCII string.

##### Parameters

1. `String` - A HEX string to be converted to ascii.

##### Returns

`String` - An ASCII string made from the given `hexString`.

##### Example

```js
var str = web3.toAscii("0x657468657265756d000000000000000000000000000000000000000000000000");
console.log(str); // "ethereum"
```

***

#### web3.fromAscii

    web3.fromAscii(string [, padding]);

Converts any ASCII string to a HEX string.

##### Parameters

1. `String` - An ASCII string to be converted to HEX.
2. `Number` - The number of bytes the returned HEX string should have. 

##### Returns

`String` - The converted HEX string.

##### Example

```js
var str = web3.fromAscii('ethereum');
console.log(str); // "0x657468657265756d"

var str2 = web3.fromAscii('ethereum', 32);
console.log(str2); // "0x657468657265756d000000000000000000000000000000000000000000000000"
```

***

#### web3.toDecimal

    web3.toDecimal(hexString);

Converts a HEX string to its number representation.

##### Parameters

1. `String` - An HEX string to be converted to a number.


##### Returns

`Number` - The number representing the data `hexString`.

##### Example

```js
var number = web3.toDecimal('0x15');
console.log(number); // 21
```

***

#### web3.fromDecimal

    web3.fromDecimal(number);

Converts a number or number string to its HEX representation.

##### Parameters

1. `Number|String` - A number to be converted to a HEX string.

##### Returns

`String` - The HEX string representing of the given `number`.

##### Example

```js
var value = web3.fromDecimal('21');
console.log(value); // "0x15"
```

***

#### web3.fromWei

    web3.fromWei(number, unit)

Converts a number of wei into the following ethereum units:

- `kwei`/`ada`
- `mwei`/`babbage`
- `gwei`/`shannon`
- `szabo`
- `finney`
- `ether`
- `kether`/`grand`/`einstein`
- `mether`
- `gether`
- `tether`

##### Parameters

1. `Number|String|BigNumber` - A number or BigNumber instance.
2. `String` - One of the above ether units.


##### Returns

`String|BigNumber` - Either a number string, or a BigNumber instance, depending on the given `number` parameter.

##### Example

```js
var value = web3.fromWei('21000000000000', 'finney');
console.log(value); // "0.021"
```

***

#### web3.toWei

    web3.toWei(number, unit)

Converts an ethereum unit into wei. Possible units are:

- `kwei`/`ada`
- `mwei`/`babbage`
- `gwei`/`shannon`
- `szabo`
- `finney`
- `ether`
- `kether`/`grand`/`einstein`
- `mether`
- `gether`
- `tether`

##### Parameters

1. `Number|String|BigNumber` - A number or BigNumber instance.
2. `String` - One of the above ether units.

##### Returns

`String|BigNumber` - Either a number string, or a BigNumber instance, depending on the given `number` parameter.

##### Example

```js
var value = web3.toWei('1', 'ether');
console.log(value); // "1000000000000000000"
```

***

#### web3.toBigNumber

    web3.toBigNumber(numberOrHexString);

Converts a given number into a BigNumber instance.

See the [note on BigNumber](#a-note-on-big-numbers-in-javascript).

##### Parameters

1. `Number|String` - A number, number string or HEX string of a number.


##### Returns

`BigNumber` - A BigNumber instance representing the given value.


##### Example

```js
var value = web3.toBigNumber('200000000000000000000001');
console.log(value); // instanceOf BigNumber
console.log(value.toNumber()); // 2.0000000000000002e+23
console.log(value.toString(10)); // '200000000000000000000001'
```

***

#### web3.net.listening

    web3.net.listening

This property is read only and says whether the node is actively listening for network connections or not.

##### Returns

`Boolean` - `true` if the client is actively listening for network connections, otherwise `false`.

##### Example

```js
var listening = web3.net.listening;
console.log(listening); // true of false
```

***

#### web3.net.peerCount

    web3.net.peerCount

This property is read only and returns the number of connected peers.

##### Returns

`Number` - The number of peers currently connected to the client.

##### Example

```js
var peerCount = web3.net.peerCount;
console.log(peerCount); // 4
```

***

#### web3.eth

Contains the ethereum blockchain related methods.

##### Example

```js
var eth = web3.eth;
```

***

#### web3.eth.coinbase

    web3.eth.coinbase

This property is read only and returns the coinbase address were the mining rewards go to.

##### Returns

`String` - The coinbase address of the client.

##### Example

```js
var coinbase = web3.eth.coinbase;
console.log(coinbase); // "0x407d73d8a49eeb85d32cf465507dd71d507100c1"
```

***

#### web3.eth.mining

    web3.eth.mining


This property is read only and says whether the node is mining or not.


##### Returns

`Boolean` - `true` if the client is mining, otherwise `false`.

##### Example

```js
var mining = web3.eth.mining;
console.log(mining); // true or false
```

***

#### web3.eth.gasPrice

    web3.eth.gasPrice


This property is read only and returns the current gas price.
The gas price is determined by the x latest blocks median gas price.

##### Returns

`BigNumber` - A BigNumber instance of the current gas price in wei.

See the [note on BigNumber](#a-note-on-big-numbers-in-javascript).

##### Example

```js
var gasPrice = web3.eth.gasPrice;
console.log(gasPrice.toString(10)); // "10000000000000"
```

***

#### web3.eth.accounts

    web3.eth.accounts

This property is read only and returns a list of accounts the node controls.

##### Returns

`Array` - An array of addresses controlled by client.

##### Example

```js
var accounts = web3.eth.accounts;
console.log(accounts); // ["0x407d73d8a49eeb85d32cf465507dd71d507100c1"] 
```

***

#### web3.eth.register

    web3.eth.register(addressHexString [, callback])

(Not Implemented yet)
Registers the given address to be included in `web3.eth.accounts`. This allows non-private-key owned accounts to be associated as an owned account (e.g., contract wallets).

##### Parameters

1. `String` - The address to register
2. `Function` - (optional) If you pass a callback the HTTP request is made asynchronous. See [this note](#using-callbacks) for details.


##### Returns

?


##### Example

```js
web3.eth.register("0x407d73d8a49eeb85d32cf465507dd71d507100ca")
```

***

#### web3.eth.unRegister

     web3.eth.unRegister(addressHexString [, callback])


(Not Implemented yet)
Unregisters a given address.

##### Parameters

1. `String` - The address to unregister.
2. `Function` - (optional) If you pass a callback the HTTP request is made asynchronous. See [this note](#using-callbacks) for details.


##### Returns

?


##### Example

```js
web3.eth.unregister("0x407d73d8a49eeb85d32cf465507dd71d507100ca")
```

***

#### web3.eth.defaultBlock

    web3.eth.defaultBlock

This default block is used for the following methods (optionally you can overwrite the defaultBlock by passing it as the last parameter):

- [web3.eth.getBalance()](#web3ethgetbalance)
- [web3.eth.getCode()](#web3ethgetcode)
- [web3.eth.getTransactionCount()](#web3ethgettransactioncount)
- [web3.eth.getStorageAt()](#web3ethgetstorageat)
- [web3.eth.call()](#web3ethcall)

##### Values

Default block parameters can be one of the following:

- `Number` - a block number
- `String - `'latest'`, which would be the latest minded block
- `String` - `'pending'`, which would the currently minded block including pending transactions

*Default is* `latest`

##### Returns

`Number|String` - The default block number to use when querying a state.

##### Example

```js
var defaultBlock = web3.eth.defaultBlock;
console.log(defaultBlock); // 'latest'

// set the default block
web3.eth.defaultBlock = 231;
```

***

#### web3.eth.blockNumber

    web3.eth.blockNumber

This property is read only and returns the current block number.

##### Returns

`Number` - The number of the most recent block.

##### Example

```js
var number = web3.eth.blockNumber;
console.log(number); // 2744
```

***

#### web3.eth.getBalance

    web3.eth.getBalance(addressHexString [, defaultBlock] [, callback])

Get the balance of an address at a given block.

##### Parameters

1. `String` - The address to get the balance of.
2. `Number|String` - (optional) If you pass this parameter it will not use the default block set with [web3.eth.defaultBlock](#web3ethdefaultblock).
3. `Function` - (optional) If you pass a callback the HTTP request is made asynchronous. See [this note](#using-callbacks) for details.

##### Returns

`String` - A BigNumber instance of the current balance for the given address in wei.

See the [note on BigNumber](#a-note-on-big-numbers-in-javascript).

##### Example

```js
var balance = web3.eth.getBalance("0x407d73d8a49eeb85d32cf465507dd71d507100c1");
console.log(balance); // instanceof BigNumber
console.log(balance.toString(10)); // '1000000000000'
console.log(balance.toNumber()); // 1000000000000
```

***

#### web3.eth.getStorageAt

    web3.eth.getStorageAt(addressHexString, position [, defaultBlock] [, callback])

Get the storage at a specific position of an address.

##### Parameters

1. `String` - The address to get the storage from.
2. `Number` - The index position of the storage.
3. `Number|String` - (optional) If you pass this parameter it will not use the default block set with [web3.eth.defaultBlock](#web3ethdefaultblock).
4. `Function` - (optional) If you pass a callback the HTTP request is made asynchronous. See [this note](#using-callbacks) for details.


##### Returns

`String` - The value in storage at the given position.

##### Example

```js
var state = web3.eth.getStorageAt("0x407d73d8a49eeb85d32cf465507dd71d507100c1", 0);
console.log(state); // "0x03"
```

***

#### web3.eth.getCode

    web3.eth.getCode(addressHexString [, defaultBlock] [, callback])

Get the code at a specific address.

##### Parameters

1. `String` - The address to get the code from.
2. `Number|String` - (optional) If you pass this parameter it will not use the default block set with [web3.eth.defaultBlock](#web3ethdefaultblock).
3. `Function` - (optional) If you pass a callback the HTTP request is made asynchronous. See [this note](#using-callbacks) for details.

##### Returns

`String` - The data at given address `addressHexString`.

##### Example

```js
var code = web3.eth.getCode("0xd5677cf67b5aa051bb40496e68ad359eb97cfbf8");
console.log(code); // "0x600160008035811a818181146012578301005b601b6001356025565b8060005260206000f25b600060078202905091905056"
```

***

#### web3.eth.getBlock

     web3.eth.getBlock(blockHashOrBlockNumber [, returnTransactionObjects] [, callback])

Returns a block matching the block number or block hash.

##### Parameters

1. `String|Number` - The block number or hash.
2. `Boolean` - (optional, default `false`) If `true`, the returned block will contain all transactions as objects, if `false` it will only contains the transaction hashes.
3. `Function` - (optional) If you pass a callback the HTTP request is made asynchronous. See [this note](#using-callbacks) for details.

##### Returns

`Object` - The block object:

  - `number`: `Number` - the block number.
  - `hash`: `String`, 32 Bytes - hash of the block.
  - `parentHash`: `String`, 32 Bytes - hash of the parent block.
  - `nonce`: `String`, 8 Bytes - hash of the generated proof-of-work.
  - `sha3Uncles`: `String`, 32 Bytes - SHA3 of the uncles data in the block.
  - `logsBloom`: `String`, 256 Bytes - the bloom filter for the logs of the block.
  - `transactionsRoot`: `String`, 32 Bytes - the root of the transaction trie of the block
  - `stateRoot`: `String`, 32 Bytes - the root of the final state trie of the block.
  - `miner`: `String`, 20 Bytes - the address of the beneficiary to whom the mining rewards were given.
  - `difficulty`: `BigNumber` - integer of the difficulty for this block.
  - `totalDifficulty`: `BigNumber` - integer of the total difficulty of the chain until this block.
  - `extraData`: `String` - the "extra data" field of this block.
  - `size`: `Number` - integer the size of this block in bytes.
  - `gasLimit`: `Number` - the maximum gas allowed in this block.
  - `gasUsed`: `Number` - the total used gas by all transactions in this block.
  - `timestamp`: `Number` - the unix timestamp for when the block was collated.
  - `transactions`: `Array` - Array of transaction objects, or 32 Bytes transaction hashes depending on the last given parameter.
  - `uncles`: `Array` - Array of uncle hashes.

##### Example

```js
var info = web3.eth.block(3150);
console.log(info);
/*
{
  "number": 3,
  "hash": "0xef95f2f1ed3ca60b048b4bf67cde2195961e0bba6f70bcbea9a2c4e133e34b46",
  "parentHash": "0x2302e1c0b972d00932deb5dab9eb2982f570597d9d42504c05d9c2147eaf9c88",
  "nonce": "0xfb6e1a62d119228b",
  "sha3Uncles": "0x1dcc4de8dec75d7aab85b567b6ccd41ad312451b948a7413f0a142fd40d49347",
  "logsBloom": "0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
  "transactionsRoot": "0x3a1b03875115b79539e5bd33fb00d8f7b7cd61929d5a3c574f507b8acf415bee",
  "stateRoot": "0xf1133199d44695dfa8fd1bcfe424d82854b5cebef75bddd7e40ea94cda515bcb",
  "miner": "0x8888f1f195afa192cfee860698584c030f4c9db1",
  "difficulty": BigNumber,
  "totalDifficulty": BigNumber,
  "size": 616,
  "extraData": "0x",
  "gasLimit": 3141592,
  "gasUsed": 21662,
  "timestamp": 1429287689,
  "transactions": [
    "0x9fc76417374aa880d4449a1f7f31ec597f00b1f6f3dd2d66f4c9c6c445836d8b"
  ],
  "uncles": []
}
*/
```

***

#### web3.eth.getBlockTransactionCount

    web3.eth.getBlockTransactionCount(hashStringOrBlockNumber [, callback])

Returns the number of transaction in a given block.

##### Parameters

1. `String|Number` - The block number or hash.
2. `Function` - (optional) If you pass a callback the HTTP request is made asynchronous. See [this note](#using-callbacks) for details.

##### Returns

`Number` - The number of transactions in the given block.

##### Example

```js
var number = web3.eth.getBlockTransactionCount("0x407d73d8a49eeb85d32cf465507dd71d507100c1");
console.log(number); // 1
```

***

#### web3.eth.getUncle

    web3.eth.getUncle(blockHashStringOrNumber, uncleNumber [, returnTransactionObjects] [, callback])

Returns a blocks uncle by a given uncle index position.

##### Parameters

1. `String|Number` - The block number or hash.
2. `Number` - The index position of the uncle.
3. `Boolean` - (optional, default `false`) If `true`, the returned block will contain all transactions as objects, if `false` it will only contains the transaction hashes.
4. `Function` - (optional) If you pass a callback the HTTP request is made asynchronous. See [this note](#using-callbacks) for details.


##### Returns

`Object` - the returned uncle. For a return value see [web3.eth.getBlock()](#web3ethgetblock).

**Note**: An uncle doesn't contain individual transactions.

##### Example

```js
var uncle = web3.eth.getUncle(500, 0);
console.log(uncle); // see web3.eth.getBlock

```

***

##### web3.eth.getTransaction

    web3.eth.getTransaction(transactionHash [, callback])

If you pass an optional callback the HTTP request is made asynchronous. See [this note](#using-callbacks) for details.

##### Returns

a transaction object its hash `transactionHash`:

  * `status`: "mined" or "pending"
  * `hash` (32-byte hash): The hash of the transaction.
  * `nonce` (32-byte hash): The transaction nonce.
  * `blockHash` (32-byte hash): the blocks hash, where this transaction appeared, `null` when pending
  * `blockNumber` (integer):  the blocks number, where this transaction appeared, `null` when pending
  * `transactionIndex` (integer):  the index a t which this transaction appeared in the block
  * `to` (20-byte address): The address to which the transaction was sent. This may be the null address (address 0) if it was a contract-creation transaction.
  * `from` (20-byte address): The cryptographically verified address from which the transaction was sent .
  * `gas` (integer): The amount of GAS supplied for this transaction to happen.
  * `gasPrice` (BigNumber): The price offered to the miner to purchase this amount of GAS, in Wei per GAS.
  * `value` (BigNumber): The amount of ETH to be transferred to the recipient with the transaction in wei.
  * `input` (byte array): The binary data that formed the input to the transaction, either the input data if it was a message call or the contract initialisation if it was a contract creation.

##### Example

```js
var blockNumber = 668;
var indexOfTransaction = 0

var transaction = web3.eth.getTransaction(blockNumber, indexOfTransaction);
console.log(transaction);
/*
{
    "hash":"0xc6ef2fc5426d6ad6fd9e2a26abeab0aa2411b7ab17f30a99d3cb96aed1d1055b",
    "nonce":"0x",
    "blockHash": "0x6fd9e2a26abeab0aa2411c6ef2fc5426d6adb7ab17f30a99d3cb96aed1d1055b",
    "blockNumber": 5599
    "transactionIndex":  1
    "from":"0x407d73d8a49eeb85d32cf465507dd71d507100c1",
    "gas":520464,
    "gasPrice": instanceof BigNumber,
    "hash":"0xc6ef2fc5426d6ad6fd9e2a26abeab0aa2411b7ab17f30a99d3cb96aed1d1055b",
    "input":"0x603880600c6000396000f3006001600060e060020a600035048063c6888fa114601857005b6021600435602b565b8060005260206000f35b600081600702905091905056",
    "to":"0x0000000000000000000000000000000000000000",
    "value": instanceof BigNumber
}
*/

```

***

#### web3.eth.getTransactionFromBlock

    getTransactionFromBlock(hashStringOrNumber, indexNumber [, callback])

If you pass an optional callback the HTTP request is made asynchronous. See [this note](#using-callbacks) for details.

##### Returns

a transaction object by block number or hash `hashStringOrNumber` with transaction index `indexNumber`:

```js
var transaction = web3.eth.getTransactionFromBlock('0x4534534534', 2);
console.log(transaction); // see web3.eth.getTransaction

```

***

#### web3.eth.getTransactionCount

    web3.eth.getTransactionCount(addressHexString [, defaultBlock] [, callback])

- If you pass an optional defaultBlock it will not use the default [web3.eth.defaultBlock](#web3ethdefaultblock).
- If you pass an optional callback the HTTP request is made asynchronous. See [this note](#using-callbacks) for details.

##### Returns

The number of transactions send from the given address `addressHexString`.

##### Example

```js
var number = web3.eth.getTransactionCount("0x407d73d8a49eeb85d32cf465507dd71d507100c1");
console.log(number); // 1
```

***

#### web3.eth.sendTransaction

    web3.eth.sendTransaction(transactionObject [, callback])

If you pass an optional callback the HTTP request is made asynchronous. See [this note](#using-callbacks) for details.

Creates a new message-call transaction.
  * `transactionObject`, an anonymous object specifying the parameters of the transaction.
    * `from` (hexString), the address for the sending account;
    * `to` (hexString), the destination address of the message, left undefined for a contract-creation transaction
    * `value (number|hexString|BigNumber)`, (optional) the value transferred for the transaction in Wei, also the endowment if it's a contract-creation transaction;
    * `gas (number|hexString|BigNumber)`, (optional, default: To-Be-Determined) the amount of gas to purchase for the transaction (unused gas is refunded), defaults to the most gas your ether balance allows; and
    * `gasPrice (number|hexString|BigNumber)`, (optional, default: To-Be-Determined) the price of gas for this transaction in wei, defaults to the mean network gasPrice.
    * `data  (hexString)`, (optional) either a [byte string](https://github.com/ethereum/wiki/wiki/Solidity,-Docs-and-ABI) containing the associated data of the message, or in the case of a contract-creation transaction, the initialisation code;
    * `code`, (optional) a synonym for `data`;

##### Example

```js

// compile solidity source code
var source = "" + 
"contract test {\n" +
"   function multiply(uint a) returns(uint d) {\n" +
"       return a * 7;\n" +
"   }\n" +
"}\n";

var compiled = web3.eth.solidity(source);

web3.eth.sendTransaction({data: compiled}, function(err, address) {
  if (!err) console.log(address); // "0x7f9fade1c0d57a7af66ab4ead7c2eb7b11a91385"
});
```

***

#### web3.eth.contract

    web3.eth.contract(abiArray)

##### Returns

a contract object, which can be used to initiate use contracts on an address.

This method creates a contract object for a solidity contract. It has the same methods available as solidity contract description itself.

You can read more about events [here](https://github.com/ethereum/wiki/wiki/Ethereum-Contract-ABI#example-javascript-usage).

##### Example

```js
// contract abi
var abi = [{
     name: 'myMethod',
     type: 'function',
     inputs: [{ name: 'a', type: 'string' }],
     outputs: [{name: 'd', type: 'string' }]
}, {
     name: 'myEvent',
     type: 'event',
     inputs: [{name: 'a', type: 'int', indexed: true},{name: 'b', type: 'bool', indexed: false]
}];

// creation of contract object
var MyContract = web3.eth.contract(abi);

// initiate contract for an address
var myContractInstance = new MyContract('0x43gg423k4h4234235345j3453');

myContractInstance.myMethod('this is test string param for call'); // myMethod call (implicit, default)
myContractInstance.call().myMethod('this is test string param for call'); // myMethod call (explicit)
myContractInstance.sendTransaction().myMethod('this is test string param for transact'); // myMethod sendTransaction

var filter = myContractInstance.myEvent({a: 5});
filter.watch(function (result) {
    console.log(result);
    /*
    {
        address: '0x0123123121',
        topics: "0x12345678901234567890123456789012", "0x0000000000000000000000000000000000000000000000000000000000000005",
        data: "0x0000000000000000000000000000000000000000000000000000000000000001",
        ...
    }
    */
});
```

##### Contract Events

You can use events like [filters](#web3ethfilter) and they have the same methods. Though the first parameter for an event are the indexed arguments as an object:

```js
var MyContract = web3.eth.contract(abi);
var myContractInstance = new MyContract('0x43gg423k4h4234235345j3453');

// watch for an event
var myEvent = myContractInstance.MyEvent({some: 'args'}, filterObject);
myEvent.watch(function(result){
   ...
});

var myResults = myEvent.get();

...

myEvent.stopWatching();
```

***

#### web3.eth.call

    web3.eth.call(callObject [, defaultBlock] [, callback])

- If you pass an optional defaultBlock it will not use the default [web3.eth.defaultBlock](#web3ethdefaultblock).
- If you pass an optional callback the HTTP request is made asynchronous. See [this note](#using-callbacks) for details.

##### Returns

The data which matches the call.

Executes a new message-call immediately without creating a transaction on the block chain. `callObject` is an object specifying the parameters of the transaction.

##### Example

```js
var options = {
    to: "0xc4abd0339eb8d57087278718986382264244252f", 
    data: "0xc6888fa10000000000000000000000000000000000000000000000000000000000000003"
};
var result = web3.eth.call(options);
console.log(result); // "0x0000000000000000000000000000000000000000000000000000000000000015"
```

***

#### web3.eth.filter

```js
// can be 'latest' or 'pending'
web3.eth.filter(filterString)
// object is a log filter 
web3.eth.filter(options)
// object is an event object 
web3.eth.filter(eventObject [, eventArguments [, options]])
// an array of events object belonging to specific contract objects (not implemented yet)
web3.eth.filter(eventArray [, options])
// object is a contract object (not implemented yet)
web3.eth.filter(contractObject [, options])
```
   * `filterString`:  `'latest'` or `'pending'` to watch for changes in the latest block or pending transactions respectively
   * `options`
       * `fromBlock`: The number of the earliest block (`latest` may be given to mean the most recent and `pending` currently mining, block).
       * `toBlock`: The number of the latest block (`latest` may be given to mean the most recent and `pending` currently mining, block).
       * `address`: An address or a list of addresses to restrict log entries by requiring them to be made from a particular account.
       * `topics`: An array of values which must each appear in the log entries.
   * `eventArguments` is an object with keys of one or more indexed arguments for the event(s) and values of either one (directly) or more (in an array) e.g. {'a': 1, 'b': [myFirstAddress, mySecondAddress]}.

##### Returns

a filter object with the following methods:

##### Filter Methods:
  * `filter.get()`: Returns all of the log entries that fit the filter.
  * `filter.watch(callback)`: Watches for state changes that fit the filter and calls the callback. See [this note](#using-callbacks) for details.
  * `filter.stopWatching()`: Stops the watch and uninstalls the filter in the node. Should always be called once it is done.

##### Callback return values

If its a log filter it returns a list of log entries; each includes the following fields:

* `status`: "pending" or "mined"
* `address`: The address of the account whose execution of the message resulted in the log entry being made.
* `topics`: The topic(s) of the log.
* `data`: The associated data of the log.
* `blockNumber`: The block number from at which this event happened.
* `blockHash`: The block hash from at which this event happened.
* `transactionHash`: The transaction hash from at which this event happened.
* `transactionIndex`: The transaction index from at which this event happened.
* `logIndex`: The log index

If its an event filter it returns a filter object with the return values of event:

* `status`: "pending" or "mined"
* `args`: The arguments coming from the event
* `topics`: The topic(s) of the log.
* `blockNumber`: The block number from at which this event happened.
* `blockHash`: The block hash from at which this event happened.
* `transactionHash`: The transaction hash from at which this event happened.
* `transactionIndex`: The transaction index from at which this event happened.
* `logIndex`: The log index

##### Example

```js
var filter = web3.eth.filter('pending');

filter.watch(function (err, log) {
  console.log(log); //  {"address":"0x0000000000000000000000000000000000000000","data":"0x0000000000000000000000000000000000000000000000000000000000000000", ...}
});

var myResults = filter.get();

...

filter.stopWatching();

```

***

#### web3.eth.getCompilers

    web3.eth.getCompilers([callback])

If you pass an optional callback the HTTP request is made asynchronous. See [this note](#using-callbacks) for details.

##### Returns

An array of available compilers.


##### Example

```js
var number = web3.eth.getCompilers();
console.log(number); // ["lll", "solidity", "serpent"]
```

***

#### web3.eth.compile.solidity

    web3.eth.compile.solidity(sourceString [, callback])

If you pass an optional callback the HTTP request is made asynchronous. See [this note](#using-callbacks) for details.

##### Returns

The compiled solidity code as HEX string.


Compiles the solidity source code `sourceString` and returns the output data.

##### Example

```js
var source = "" + 
    "contract test {\n" +
    "   function multiply(uint a) returns(uint d) {\n" +
    "       return a * 7;\n" +
    "   }\n" +
    "}\n";
var code = web3.eth.compile.solidity(source);
console.log(code); // "0x603880600c6000396000f3006001600060e060020a600035048063c6888fa114601857005b6021600435602b565b8060005260206000f35b600081600702905091905056"
```

***

#### web3.eth.compile.lll

    web3. eth.compile.lll(sourceString [, callback])

If you pass an optional callback the HTTP request is made asynchronous. See [this note](#using-callbacks) for details.

##### Returns

The compiled lll code as HEX string.


Compiles the LLL source code `sourceString` and returns the output data.

##### Example

```js
var source = "...";

var code = web3.eth.compile.lll(source);
console.log(code); // "0x603880600c6000396000f3006001600060e060020a600035048063c6888fa114601857005b6021600435602b565b8060005260206000f35b600081600702905091905056"
```

***

#### web3.eth.compile.serpent

    web3.eth.compile.serpent(sourceString [, callback])

If you pass an optional callback the HTTP request is made asynchronous. See [this note](#using-callbacks) for details.

##### Returns

The compiled serpent code as HEX string.


Compiles the serpent source code `sourceString ` and returns the output data.

```js
var source = "...";

var code = web3.eth.compile.serpent(source);
console.log(code); // "0x603880600c6000396000f3006001600060e060020a600035048063c6888fa114601857005b6021600435602b565b8060005260206000f35b600081600702905091905056"
```

***

#### web3.eth.flush

##### Example

```js
// TODO:
```

***

#### web3.db.putString
This method should be called, when we want to store a string in a local leveldb database.
First

##### Example
 param is db name, second is the key, and third is the string value.
```js
var result = web3.db.putString('testDB', 'key', 'myString')
console.log(result); // true
```

***

#### web3.db.getString
This method should be called, when we want to get string from local leveldb database.
First

##### Example
 param is db name and second is the key of string value.
```js
var value = web3.db.getString('testDB', 'key');
console.log(value); // "myString"
```

***

#### web3.db.putHex
This method should be called, when we want to store HEX in a local leveldb database.
First

##### Example
 param is db name, second is the key, and third is the value.
```js
var result = web3.db.putHex('testDB', 'key', '0x4f554b443');
console.log(result); // true

```

***

#### web3.db.getHex
This method should be called, when we want to get a HEX value from a local leveldb database.
First

##### Example
 param is db name and second is the key of value.
```js
var value = web3.db.getHex('testDB', 'key');
console.log(value); // "0x4f554b443"
```

***

#### web3.shh
[Whisper  Overview](https://github.com/ethereum/wiki/wiki/Whisper-Overview)

##### Example

```js
var shh = web3.shh;
```

***

#### web3.shh.post

   web3.shh.post(object [, callback])

If you pass an optional callback the HTTP request is made asynchronous. See [this note](#using-callbacks) for details.

This method should be called, when we want to post whisper message. `_message` object may have following fields:
  * `from`: identity of sender as hexString
  * `to`: identity of receiver as hexString
  * `payload`: message payload
  * `ttl`: time to live in seconds
  * `workToProve`: // or priority TODO
  * `topics`: array of strings or hexStrings, with message topics
```js
var identity = web3.shh.newIdentity();
var topic = 'example';
var payload = 'hello whisper world!';

var message = {
  from: identity,
  topics: [topic],
  payload: payload,
  ttl: 100,
  workToProve: 100 // or priority TODO
};

web3.shh.post(message);
```

***

#### web3.shh.newIdentity

    web3.shh.newIdentity([callback])

If you pass an optional callback the HTTP request is made asynchronous. See [this note](#using-callbacks) for details.

Should be called to create new identity.

##### Returns

a new identity hex string.

```js
var identity = web3.shh.newIdentity();
console.log(identity); // "0xc931d93e97ab07fe42d923478ba2465f283f440fd6cabea4dd7a2c807108f651b7135d1d6ca9007d5b68aa497e4619ac10aa3b27726e1863c1fd9b570d99bbaf"
```

***

#### web3.shh.hasIdentity

    web3.shh.hasIdentity([callback])

If you pass an optional callback the HTTP request is made asynchronous. See [this note](#using-callbacks) for details.

Should be called, if we want to check if user has given identity. Accepts one param. Returns true if he has, otherwise false.

##### Returns

a boolean whether or not the identity exists.


##### Example

```js
var identity = web3.shh.newIdentity();
var result = web3.shh.hasIdentity(identity);
console.log(result); // true

var result2 = web3.shh.hasIdentity(identity + "0");
console.log(result2); // false
```

***

#### web3.shh.newGroup

##### Example
```js
// TODO: not implemented yet
```

***

#### web3.shh.addToGroup

##### Example
```js
// TODO: not implemented yet
```

***

#### web3.shh.filter

    wev3.shh.filter(options)

This method should be used, when you want to watch whisper messages.
Available filter options are:
* `topics`: array of strings. filter messages with by this topic(s)
* `to`: filter the identity of receiver of the message

##### Example

```js
var topic = 'example';

var options = {
  topics: [topic],
  to: '0x32jkh23kjh4j23h5j34h5j3464'
};

var filter = web3.shh.filter(options);

filter.watch(function(res) {
  console.log(res);
  /* {
  "expiry":1422565026,
  "from":"0x3ec052fc3376f8a218b24b652803a5892038c39419a9a44923a61b113b7785d16ed1572df628859af3504670e4df31dcd8b3ee9a2110fd710c948690f0557394",
  "hash":"0x33eb2da77bf3527e28f8bf493650b1879b08c4f2a362beae4ba2f71bafcd91f9",
  "payload": 'The send message',
  "payloadRaw": "0xjk5h34kj5h34kj6kj346456547trhfghfdgh",
  "sent": 1422564926,
  "to":"0x32jkh23kjh4j23h5j34h5j3464",
  "topics":['myTopic'],
  "ttl":100,
  "workProved":0
  } 
  */
});
```

# Example

A simple HTML snippet that will display the user's primary account balance of Ether:
```html
<html><body>
<div>You have <span id="ether">?</span> Weis</div>
<script>
web3.eth.filter('pending').watch(function() {
    var balance = web3.eth.getBalance(web3.eth.coinbase);
    document.getElementById("ether").innerText = balance.toString(10);
});

...

filter.stopWatching();
</script>
</body></html>
```

To test it, just put it in file and save. Load it in AlethZero and point the URL to file:///WHEREVER_YOU_SAVED_IT

Job done. Now go create.

more examples can be found [here](https://github.com/ethereum/web3.js/tree/master/example)