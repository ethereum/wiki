---
name: Javascript API
category: 
---

# Introduction

Dapp を Ethereum 上でうごかすために、`web3` オブジェクトが使えます。これは [web3.js library](https://github.com/ethereum/web3.js)で提供されています。
この、まるでパーカーのフードのように、 ethereum を包み込んだライブラリのもとでは、
ローカルのノードと
[RPC calls](https://github.com/ethereum/wiki/wiki/JSON-RPC)を介して通信できます。
web3.js はすべての ethereum node と通信可能で、そのノードは RPC を発信しています。

`web3` オブジェクトは、 
`eth` オブジェクトと - `web3.eth` ( Ethereum blockchain との通信に特化したオブジェクト) と 
`shh` オブジェクト - `web3.shh` ( Whisper 上のやりとりのためのオブジェクト) を保持します。
時間の経過とともに、web3 プロトコル上の他のオブジェクトも紹介していくつもりです。
使用例は [こちら](https://github.com/ethereum/web3.js/tree/master/example).

web3.js の洗練された使用例につきましては、[useful Ðapp patterns](https://github.com/ethereum/wiki/wiki/Useful-Ðapp-Patterns)をごらんください。

## Using callbacks

As this API is designed to work with a local RPC node and all its functions are by default use synchronous HTTP requests.con

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

## Batch requests

Batch requests allow queuing up requests and processing them at once.

```js
var batch = web3.createBatch();
batch.add(web3.eth.getBalance.request('0x0000000000000000000000000000000000000000', 'latest', callback));
batch.add(web3.eth.contract(abi).at(address).balance.request(address, callback2));
batch.execute();
```

## A note on big numbers in web3.js

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
     * [api](#web3versionapi)
     * [client](#web3versionclient)
     * [network](#web3versionnetwork)
     * [ethereum](#web3versionethereum)
     * [whisper](#web3versionwhisper)
  * [setProvider(provider)](#web3setprovider)
  * [currentProvider](#web3currentprovider)
  * [reset()](#web3reset)
  * [sha3(string)](#web3sha3)
  * [toHex(stringOrNumber)](#web3tohex)
  * [toAscii(hexString)](#web3toascii)
  * [fromAscii(textString, [padding])](#web3fromascii)
  * [toDecimal(hexString)](#web3todecimal)
  * [fromDecimal(number)](#web3fromdecimal)
  * [fromWei(numberStringOrBigNumber, unit)](#web3fromwei)
  * [toWei(numberStringOrBigNumber, unit)](#web3toWei)
  * [toBigNumber(numberOrHexString)](#web3tobignumber)
  * [isAddress(hexString)](#web3isAddress)
  * [net](#web3net)
    * [listening/getListening](#web3netlistening)
    * [peerCount/getPeerCount](#web3ethpeercount)
  * [eth](#web3eth)
    * [defaultAccount](#web3ethdefaultaccount)
    * [defaultBlock](#web3ethdefaultblock)
    * [coinbase/getCoinbase](#web3ethcoinbase)
    * [hashrate/getHashrate](#web3ethhashrate)
    * [gasPrice/getGasPrice](#web3ethgasprice)
    * [accounts/getAccounts](#web3ethaccounts)
    * [mining/getMining](#web3ethmining)
    * [blockNumber/getBlockNumber](#web3ethblocknumber)
    * [register(hexString)](#web3ethregister) (Not implemented yet)
    * [unRegister(hexString)](#web3ethunregister) (Not implemented yet)
    * [getBalance(address)](#web3ethgetbalance)
    * [getStorageAt(address, position)](#web3ethgetstorageat)
    * [getCode(address)](#web3ethgetcode)
    * [getBlock(hash/number)](#web3ethgetblock)
    * [getBlockTransactionCount(hash/number)](#web3ethgetblocktransactioncount)
    * [getUncle(hash/number)](#web3ethgetuncle)
    * [getBlockUncleCount(hash/number)](#web3ethgetblockunclecount)
    * [getTransaction(hash)](#web3ethgettransaction)
    * [getTransactionFromBlock(hashOrNumber, indexNumber)](#web3ethgettransactionfromblock)
    * [getTransactionReceipt(hash)](#web3ethgettransactionreceipt)
    * [getTransactionCount(address)](#web3ethgettransactioncount)
    * [sendTransaction(object)](#web3ethsendtransaction)
    * [call(object)](#web3ethcall)
    * [estimateGas(object)](#web3ethestimategas)
    * [filter(array (, options) )](#web3ethfilter)
        - [watch(callback)](#web3ethfilter)
        - [stopWatching(callback)](#web3ethfilter)
        - [get()](#web3ethfilter)
    * [contract(abiArray)](#web3ethcontract)
    * [contract.myMethod()](#contract-methods)
    * [contract.myEvent()](#contract-events)
    * [getCompilers()](#web3ethgetcompilers)
    * [compile.lll(string)](#web3ethcompilelll)
    * [compile.solidity(string)](#web3ethcompilesolidity)
    * [compile.serpent(string)](#web3ethcompileserpent)
    * [namereg](#web3ethnamereg)
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

## Usage

#### web3
The `web3` object provides all methods.

##### Example

```js
var web3 = require('web3')
```

***

#### web3.version.api

    web3.version.api
    // or async
    web3.version.getApi(callback(error, result){ ... })

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
    // or async
    web3.version.getClient(callback(error, result){ ... })


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
    // or async
    web3.version.getNetwork(callback(error, result){ ... })


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
    // or async
    web3.version.getEthereum(callback(error, result){ ... })


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
    // or async
    web3.version.getWhisper(callback(error, result){ ... })


##### Returns

`String` - The whisper protocol version.

##### Example

```js
var version = web3.version.whisper;
console.log(version); // 20
```

***

#### web3.setProvider

    web3.setProvider(provider)

Should be called to set provider.

##### Parameters
none

##### Returns

`undefined`

##### Example

```js
web3.setProvider(new web3.providers.HttpProvider('http://localhost:8545')); // 8080 for cpp/AZ, 8545 for go/mist
```

***

#### web3.currentProvider

    web3.currentProvider

Will contain the current provider, if one is set. This can be used to check if mist etc. set already a provider.


##### Returns

`Object` - The provider set or `null`;

##### Example

```js
// Check if mist etc. already set a provider
if(!web3.currentProvider)
    web3.setProvider(new web3.providers.HttpProvider("http://localhost:8545"));

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

### web3.net

#### web3.net.listening

    web3.net.listening
    // or async
    web3.net.getListening(callback(error, result){ ... })

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
    // or async
    web3.net.getPeerCount(callback(error, result){ ... })

This property is read only and returns the number of connected peers.

##### Returns

`Number` - The number of peers currently connected to the client.

##### Example

```js
var peerCount = web3.net.peerCount;
console.log(peerCount); // 4
```

***

### web3.eth

Contains the ethereum blockchain related methods.

##### Example

```js
var eth = web3.eth;
```

***

#### web3.eth.defaultAccount

    web3.eth.defaultAccount

This default address is used for the following methods (optionally you can overwrite it by specifying the `from` property):

- [web3.eth.sendTransaction()](#web3ethsendtransaction)
- [web3.eth.call()](#web3ethcall)

##### Values

`String`, 20 Bytes - Any address you own, or where you have the private key for.

*Default is* `undefined`.

##### Returns

`String`, 20 Bytes - The currently set default address.

##### Example

```js
var defaultAccount = web3.eth.defaultAccount;
console.log(defaultAccount); // ''

// set the default block
web3.eth.defaultAccount = '0x8888f1f195afa192cfee860698584c030f4c9db1';
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
- `String` - `"earliest"`, the genisis block
- `String` - `"latest"`, the latest block (current head of the blockchain)
- `String` - `"pending"`, the currently mined block (including pending transactions)

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

#### web3.eth.coinbase

    web3.eth.coinbase
    // or async
    web3.eth.getCoinbase(callback(error, result){ ... })

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
    // or async
    web3.eth.getMining(callback(error, result){ ... })


This property is read only and says whether the node is mining or not.


##### Returns

`Boolean` - `true` if the client is mining, otherwise `false`.

##### Example

```js
var mining = web3.eth.mining;
console.log(mining); // true or false
```

***

#### web3.eth.hashrate

    web3.eth.hashrate
    // or async
    web3.eth.getHashrate(callback(error, result){ ... })

This property is read only and returns the number of hashes per second that the node is mining with.


##### Returns

`Number` - number of hashes per second.

##### Example

```js
var hashrate = web3.eth.hashrate;
console.log(hashrate); // 493736
```

***

#### web3.eth.gasPrice

    web3.eth.gasPrice
    // or async
    web3.eth.getGasPrice(callback(error, result){ ... })


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
    // or async
    web3.eth.getAccounts(callback(error, result){ ... })

This property is read only and returns a list of accounts the node controls.

##### Returns

`Array` - An array of addresses controlled by client.

##### Example

```js
var accounts = web3.eth.accounts;
console.log(accounts); // ["0x407d73d8a49eeb85d32cf465507dd71d507100c1"] 
```

***

#### web3.eth.blockNumber

    web3.eth.blockNumber
    // or async
    web3.eth.getBlockNumber(callback(error, result){ ... })

This property is read only and returns the current block number.

##### Returns

`Number` - The number of the most recent block.

##### Example

```js
var number = web3.eth.blockNumber;
console.log(number); // 2744
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

1. `String|Number` - The block number or hash. Or the string `"earliest"`, `"latest"` or `"pending"` as in the [default block parameter](#web3ethdefaultblock).
2. `Boolean` - (optional, default `false`) If `true`, the returned block will contain all transactions as objects, if `false` it will only contains the transaction hashes.
3. `Function` - (optional) If you pass a callback the HTTP request is made asynchronous. See [this note](#using-callbacks) for details.

##### Returns

`Object` - The block object:

  - `number`: `Number` - the block number. `null` when its pending block.
  - `hash`: `String`, 32 Bytes - hash of the block. `null` when its pending block.
  - `parentHash`: `String`, 32 Bytes - hash of the parent block.
  - `nonce`: `String`, 8 Bytes - hash of the generated proof-of-work. `null` when its pending block.
  - `sha3Uncles`: `String`, 32 Bytes - SHA3 of the uncles data in the block.
  - `logsBloom`: `String`, 256 Bytes - the bloom filter for the logs of the block. `null` when its pending block.
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

1. `String|Number` - The block number or hash. Or the string `"earliest"`, `"latest"` or `"pending"` as in the [default block parameter](#web3ethdefaultblock).
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

1. `String|Number` - The block number or hash. Or the string `"earliest"`, `"latest"` or `"pending"` as in the [default block parameter](#web3ethdefaultblock).
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

Returns a transaction matching the given transaction hash.

##### Parameters

1. `String` - The transaction hash.
2. `Function` - (optional) If you pass a callback the HTTP request is made asynchronous. See [this note](#using-callbacks) for details.


##### Returns

`Object` - A transaction object its hash `transactionHash`:

  - `hash`: `String`, 32 Bytes - hash of the transaction.
  - `nonce`: `Number` - the number of transactions made by the sender prior to this one.
  - `blockHash`: `String`, 32 Bytes - hash of the block where this transaction was in. `null` when its pending.
  - `blockNumber`: `Number` - block number where this transaction was in. `null` when its pending.
  - `transactionIndex`: `Number` - integer of the transactions index position in the block. `null` when its pending.
  - `from`: `String`, 20 Bytes - address of the sender.
  - `to`: `String`, 20 Bytes - address of the receiver. `null` when its a contract creation transaction.
  - `value`: `BigNumber` - value transferred in Wei.
  - `gasPrice`: `BigNumber` - gas price provided by the sender in Wei.
  - `gas`: `Number` - gas provided by the sender.
  - `input`: `String` - the data send along with the transaction.


##### Example

```js
var blockNumber = 668;
var indexOfTransaction = 0

var transaction = web3.eth.getTransaction(blockNumber, indexOfTransaction);
console.log(transaction);
/*
{
  "hash": "0x9fc76417374aa880d4449a1f7f31ec597f00b1f6f3dd2d66f4c9c6c445836d8b",
  "nonce": 2,
  "blockHash": "0xef95f2f1ed3ca60b048b4bf67cde2195961e0bba6f70bcbea9a2c4e133e34b46",
  "blockNumber": 3,
  "transactionIndex": 0,
  "from": "0xa94f5374fce5edbc8e2a8697c15331677e6ebf0b",
  "to": "0x6295ee1b4f6dd65047762f924ecd367c17eabf8f",
  "value": BigNumber,
  "gas": 314159,
  "gasPrice": BigNumber,
  "input": "0x57cb2fc4"
}
*/

```

***

#### web3.eth.getTransactionFromBlock

    getTransactionFromBlock(hashStringOrNumber, indexNumber [, callback])

Returns a transaction based on a block hash or number and the transactions index position.

##### Parameters

1. `String` - A block number or hash. Or the string `"earliest"`, `"latest"` or `"pending"` as in the [default block parameter](#web3ethdefaultblock).
2. `Number` - The transactions index position.
3. `Function` - (optional) If you pass a callback the HTTP request is made asynchronous. See [this note](#using-callbacks) for details.

##### Returns

`Object` - A transaction object, see [web3.eth.getTransaction](#web3ethgettransaction):


##### Example

```js
var transaction = web3.eth.getTransactionFromBlock('0x4534534534', 2);
console.log(transaction); // see web3.eth.getTransaction

```

***

#### web3.eth.getTransactionReceipt

    web3.eth.getTransactionReceipt(hashString [, callback])

Returns the receipt of a transaction by transaction hash.

**Note** That the receipt is not available for pending transactions.


##### Parameters

1. `String` - The transaction hash.
2. `Function` - (optional) If you pass a callback the HTTP request is made asynchronous. See [this note](#using-callbacks) for details.

##### Returns

`Object` - A transaction receipt object, or `null` when no receipt was found:

  - `blockHash`: `String`, 32 Bytes - hash of the block where this transaction was in.
  - `blockNumber`: `Number` - block number where this transaction was in.
  - `transactionHash`: `String`, 32 Bytes - hash of the transaction.
  - `transactionIndex`: `Number` - integer of the transactions index position in the block.
  - `from`: `String`, 20 Bytes - address of the sender.
  - `to`: `String`, 20 Bytes - address of the receiver. `null` when its a contract creation transaction.
  - `cumulativeGasUsed `: `Number ` - The total amount of gas used when this transaction was executed in the block.
  - `gasUsed `: `Number ` -  The amount of gas used by this specific transaction alone.
  - `contractAddress `: `String` - 20 Bytes - The contract address created, if the transaction was a contract creation, otherwise `null`.
  - `logs `:  `Array` - Array of log objects, which this transaction generated.

##### Example
```js
var receipt = web3.eth.getTransactionReceipt('0x9fc76417374aa880d4449a1f7f31ec597f00b1f6f3dd2d66f4c9c6c445836d8b');
console.log(receipt);
{
  "transactionHash": "0x9fc76417374aa880d4449a1f7f31ec597f00b1f6f3dd2d66f4c9c6c445836d8b",
  "transactionIndex": 0,
  "blockHash": "0xef95f2f1ed3ca60b048b4bf67cde2195961e0bba6f70bcbea9a2c4e133e34b46",
  "blockNumber": 3,
  "contractAddress": "0xa94f5374fce5edbc8e2a8697c15331677e6ebf0b",
  "cumulativeGasUsed": 314159,
  "gasUsed": 30234,
  "logs": [{
         // logs as returned by getFilterLogs, etc.
     }, ...]
}
```

***

#### web3.eth.getTransactionCount

    web3.eth.getTransactionCount(addressHexString [, defaultBlock] [, callback])

Get the numbers of transactions send from this address.

##### Parameters

1. `String` - The address to get the numbers of transactions from.
2. `Number|String` - (optional) If you pass this parameter it will not use the default block set with [web3.eth.defaultBlock](#web3ethdefaultblock).
3. `Function` - (optional) If you pass a callback the HTTP request is made asynchronous. See [this note](#using-callbacks) for details.

##### Returns

`Number` - The number of transactions send from the given address.

##### Example

```js
var number = web3.eth.getTransactionCount("0x407d73d8a49eeb85d32cf465507dd71d507100c1");
console.log(number); // 1
```

***

#### web3.eth.sendTransaction

    web3.eth.sendTransaction(transactionObject [, callback])

Sends a transaction to the network.

##### Parameters

1. `Object` - The transaction object to send:
  - `from`: `String` - The address for the sending account. Uses the [web3.eth.defaultAccount](#web3ethdefaultaccount) property, if not specified.
  - `to`: `String` - (optional) The destination address of the message, left undefined for a contract-creation transaction.
  - `value`: `Number|String|BigNumber` - (optional) The value transferred for the transaction in Wei, also the endowment if it's a contract-creation transaction.
  - `gas`: `Number|String|BigNumber` - (optional, default: To-Be-Determined) The amount of gas to use for the transaction (unused gas is refunded).
  - `gasPrice`: `Number|String|BigNumber` - (optional, default: To-Be-Determined) The price of gas for this transaction in wei, defaults to the mean network gas price.
  - `data`: `String` - (optional) Either a [byte string](https://github.com/ethereum/wiki/wiki/Solidity,-Docs-and-ABI) containing the associated data of the message, or in the case of a contract-creation transaction, the initialisation code.
  - `nonce`: `Number`  - (optional) Integer of a nonce. This allows to overwrite your own pending transactions that use the same nonce.
2. `Number|String` - (optional) If you pass this parameter it will not use the default block set with [web3.eth.defaultBlock](#web3ethdefaultblock).
3. `Function` - (optional) If you pass a callback the HTTP request is made asynchronous. See [this note](#using-callbacks) for details.

##### Returns

`String` - The 32 Bytes transaction hash as HEX string.

If the transaction was a contract creation use [web3.eth.getTransactionReceipt()](#web3gettransactionreceipt) to get the contract address, after the transaction was mined.

##### Example

```js

// compiled solidity source code using https://chriseth.github.io/cpp-ethereum/
var code = "603d80600c6000396000f3007c01000000000000000000000000000000000000000000000000000000006000350463c6888fa18114602d57005b600760043502
8060005260206000f3";

web3.eth.sendTransaction({data: code}, function(err, address) {
  if (!err)
    console.log(address); // "0x7f9fade1c0d57a7af66ab4ead7c2eb7b11a91385"
});
```

***

#### web3.eth.call

    web3.eth.call(callObject [, defaultBlock] [, callback])

Executes a message call transaction, which is directly executed in the VM of the node, but never mined into the blockchain.

##### Parameters

1. `Object` - A transaction object see [web3.eth.sendTransaction](#web3ethsendtransaction), with the difference that for calls the `from` property is optional as well.
2. `Number|String` - (optional) If you pass this parameter it will not use the default block set with [web3.eth.defaultBlock](#web3ethdefaultblock).
3. `Function` - (optional) If you pass a callback the HTTP request is made asynchronous. See [this note](#using-callbacks) for details.

##### Returns

`String` - The returned data of the call, e.g. a codes functions return value.

##### Example

```js
var result = web3.eth.call({
    to: "0xc4abd0339eb8d57087278718986382264244252f", 
    data: "0xc6888fa10000000000000000000000000000000000000000000000000000000000000003"
});
console.log(result); // "0x0000000000000000000000000000000000000000000000000000000000000015"
```

***

#### web3.eth.estimateGas

    web3.eth.estimateGas(callObject [, defaultBlock] [, callback])

Executes a message call or transaction, which is directly executed in the VM of the node, but never mined into the blockchain and returns the amount of the gas used.

##### Parameters

See [web3.eth.sendTransaction](#web3ethsendtransaction), expect that all properties are optional.

##### Returns

`Number` - the used gas for the simulated call/transaction.

##### Example

```js
var result = web3.eth.estimateGas({
    to: "0xc4abd0339eb8d57087278718986382264244252f", 
    data: "0xc6888fa10000000000000000000000000000000000000000000000000000000000000003"
});
console.log(result); // "0x0000000000000000000000000000000000000000000000000000000000000015"
```

***

#### web3.eth.filter

```js
// can be 'latest' or 'pending'
var filter = web3.eth.filter(filterString);
// OR object are log filter options
var filter = web3.eth.filter(options);

// watch for changes
filter.watch(function(error, result){
  if (!error)
    console.log(result);
});

// Additionally you can start watching right away, by passing a callback:
web3.eth.filter(options, function(error, result){
  if (!error)
    console.log(result);
});
```

##### Parameters

1. `String|Object` - The string `"latest"` or `"pending"` to watch for changes in the latest block or pending transactions respectively. Or a filter options object as follows:
  * `fromBlock`: `Number|String` - The number of the earliest block (`latest` may be given to mean the most recent and `pending` currently mining, block). By default `latest`.
  * `toBlock`: `Number|String` - The number of the latest block (`latest` may be given to mean the most recent and `pending` currently mining, block). By default `latest`.
  * `address`: `String` - An address or a list of addresses to only get logs from particular account(s).
  * `topics`: `Array of Strings` - An array of values which must each appear in the log entries. The order is important, if you want to leave topics use `null`, e.g. `[null, '0x00...']`.

##### Returns

`Object` - A filter object with the following methods:

  * `filter.get()`: Returns all of the log entries that fit the filter.
  * `filter.watch(callback)`: Watches for state changes that fit the filter and calls the callback. See [this note](#using-callbacks) for details.
  * `filter.stopWatching()`: Stops the watch and uninstalls the filter in the node. Should always be called once it is done.

##### Watch callback return value

- `String` - When using the `"latest"` parameter, it returns the block hash of the last incoming block.
- `String` - When using the `"pending"` parameter, it returns a transaction hash of the last add pending transaction.
- `Object` - When using manual filter options, it returns a log object as follows:
    - `logIndex`: `Number` - integer of the log index position in the block. `null` when its pending log.
    - `transactionIndex`: `Number` - integer of the transactions index position log was created from. `null` when its pending log.
    - `transactionHash`: `String`, 32 Bytes - hash of the transactions this log was created from. `null` when its pending log.
    - `blockHash`: `String`, 32 Bytes - hash of the block where this log was in. `null` when its pending. `null` when its pending log.
    - `blockNumber`: `Number` - the block number where this log was in. `null` when its pending. `null` when its pending log.
    - `address`: `String`, 32 Bytes - address from which this log originated.
    - `data`: `String` - contains one or more 32 Bytes non-indexed arguments of the log.
    - `topics`: `Array of Strings` - Array of 0 to 4 32 Bytes `DATA` of indexed log arguments. (In *solidity*: The first topic is the *hash* of the signature of the event (e.g. `Deposit(address,bytes32,uint256)`), except you declared the event with the `anonymous` specifier.)

**Note** For event filter return values see [Contract Events](#contract-events)

##### Example

```js
var filter = web3.eth.filter('pending');

filter.watch(function (error, log) {
  console.log(log); //  {"address":"0x0000000000000000000000000000000000000000", "data":"0x0000000000000000000000000000000000000000000000000000000000000000", ...}
});

// get all past logs again.
var myResults = filter.get();

...

// stops and uninstalls the filter
filter.stopWatching();

```

***

#### web3.eth.contract

    web3.eth.contract(abiArray)

Creates a contract object for a solidity contract, which can be used to initiate contracts on an address.
You can read more about events [here](https://github.com/ethereum/wiki/wiki/Ethereum-Contract-ABI#example-javascript-usage).

##### Parameters

1. `Array` - ABI array with descriptions of functions and events of the contract.

##### Returns

`Object` - A contract object, which can be initiated as follows:

```js
var MyContract = web3.eth.contract(abiArray);

// instantiate from an existing address
var myContractInstance = MyContract.at(myContractAddress);

// Create the contract async
var myContract = MyContract.new(param1, param2, {
   data: myContractCode,
   gas: 300000,
   from: mySenderAddress}, function(err, contract){
    if(!err) {
       // The callback will fire after the contract is mined
       // Note that the returned "myContract" === "myContractInstance"
       console.log(myContractInstance.address) // "0xc4abd0339eb8d57087278718986382264244252f"
       console.log(myContractInstance.transactionHash) // The hash of the transaction, which created the contract
    }
  });

// Create contract sync: Then the address will be added as soon as the contract is mined.
// Additionally you can watch the transaction by using the "transactionHash" property
var myContractInstance = MyContract.new(param1, param2, {data: myContractCode, gas: 300000, from: mySenderAddress});
myContractInstance.transactionHash // The hash of the transaction, which created the contract
myContractInstance.address // undefined at start, but will be auto-filled later
```

##### Example

```js
// contract abi
var abi = [{
     name: 'myConstantMethod',
     type: 'function',
     constant: true,
     inputs: [{ name: 'a', type: 'string' }],
     outputs: [{name: 'd', type: 'string' }]
}, {
     name: 'myStateChangingMethod',
     type: 'function',
     constant: false,
     inputs: [{ name: 'a', type: 'string' }, { name: 'b', type: 'int' }],
     outputs: []
}, {
     name: 'myEvent',
     type: 'event',
     inputs: [{name: 'a', type: 'int', indexed: true},{name: 'b', type: 'bool', indexed: false]
}];

// creation of contract object
var MyContract = web3.eth.contract(abi);

// initiate contract for an address
var myContractInstance = MyContract.at('0xc4abd0339eb8d57087278718986382264244252f');

// call constant function
var result = myContractInstance.myConstantMethod('myParam');
console.log(result) // '0x25434534534'

// send a transaction to a function
myContractInstance.myStateChangingMethod('someParam1', 23, {value: 200, gas: 2000});

// short hand style
web3.eth.contract(abi).at(address).myAwesomeMethod(...);

// create filter
var filter = myContractInstance.myEvent({a: 5}, function (error, result) {
  if (!error)
    console.log(result);
    /*
    {
        address: '0x8718986382264244252fc4abd0339eb8d5708727',
        topics: "0x12345678901234567890123456789012", "0x0000000000000000000000000000000000000000000000000000000000000005",
        data: "0x0000000000000000000000000000000000000000000000000000000000000001",
        ...
    }
    */
});
```

***

#### Contract Methods

```js
// Automatically determines the use of call or sendTransaction based on the method type
myContractInstance.myMethod(param1 [, param2, ...] [, transactionObject] [, callback]);

// Explicitly calling this method
myContractInstance.myMethod.call(param1 [, param2, ...] [, transactionObject] [, callback]);

// Explicitly sending a transaction to this method
myContractInstance.myMethod.sendTransaction(param1 [, param2, ...] [, transactionObject] [, callback]);
```

The contract object exposes the contracts methods, which can be called using parameters and a transaction object.

##### Parameters

- `String|Number` - (optional) Zero or more parameters of the function.
- `Object` - (optional) The (previous) last parameter can be a transaction object, see [web3.eth.sendTransaction](#web3ethsendtransaction) parameter 1 for more.
- `Function` - (optional) If you pass a callback as the last parameter the HTTP request is made asynchronous. See [this note](#using-callbacks) for details.

##### Returns

`String` - If its a call the result data, if its a send transaction a created contract address, or the transaction hash, see [web3.eth.sendTransaction](#web3ethsendtransaction) for details.


##### Example

```js
// creation of contract object
var MyContract = web3.eth.contract(abi);

// initiate contract for an address
var myContractInstance = MyContract.at('0x78e97bcc5b5dd9ed228fed7a4887c0d7287344a9');

var result = myContractInstance.myConstantMethod('myParam');
console.log(result) // '0x25434534534'

myContractInstance.myStateChangingMethod('someParam1', 23, {value: 200, gas: 2000}, function(err, result){ ... });
```

***


#### Contract Events

```js
var event = myContractInstance.MyEvent({valueA: 23}, additionalFilterObject)

// watch for changes
event.watch(function(error, result){
  if (!error)
    console.log(result);
});

// Or pass a callback to start watching immediately
var event = myContractInstance.MyEvent({valueA: 23}, additionalFilterObject, function(error, result){
  if (!error)
    console.log(result);
});

```

You can use events like [filters](#web3ethfilter) and they have the same methods, but you pass different objects to create the event filter.

##### Parameters

1. `Object` - Indexed return values you want to filter the logs by, e.g. `{'valueA': 1, 'valueB': [myFirstAddress, mySecondAddress]}`. By default all filter values are set to `null`. It means, that they will match any event of given type sent from this contract.
2. `Object` - Additional filter options, see [filters](#web3ethfilter) parameter 1 for more. By default filterObject has field 'address' set to address of the contract. Also first topic is the signature of event.

##### Callback return


`Object` - An event object as follows:

- `args`: `Object` - The arguments coming from the event.
- `event`: `String` - The event name.
- `logIndex`: `Number` - integer of the log index position in the block.
- `transactionIndex`: `Number` - integer of the transactions index position log was created from.
- `transactionHash`: `String`, 32 Bytes - hash of the transactions this log was created from.
- `address`: `String`, 32 Bytes - address from which this log originated.
- `blockHash`: `String`, 32 Bytes - hash of the block where this log was in. `null` when its pending.
- `blockNumber`: `Number` - the block number where this log was in. `null` when its pending.


##### Example

```js
var MyContract = web3.eth.contract(abi);
var myContractInstance = MyContract.at('0x78e97bcc5b5dd9ed228fed7a4887c0d7287344a9');

// watch for an event with {some: 'args'}
var myEvent = myContractInstance.MyEvent({some: 'args'}, {fromBlock: 0, toBlock: 'latest'});
myEvent.watch(function(error, result){
   ...
});

// would get all past logs again.
var myResults = myEvent.get();

...

// would stop and uninstall the filter
myEvent.stopWatching();
```

***

#### web3.eth.getCompilers

    web3.eth.getCompilers([callback])

Gets a list of available compilers.

##### Parameters

1. `Function` - (optional) If you pass a callback the HTTP request is made asynchronous. See [this note](#using-callbacks) for details.

##### Returns

`Array` - An array of strings of available compilers.

##### Example

```js
var number = web3.eth.getCompilers();
console.log(number); // ["lll", "solidity", "serpent"]
```

***

#### web3.eth.compile.solidity

    web3.eth.compile.solidity(sourceString [, callback])

Compiles solidity source code.

##### Parameters

1. `String` - The solidity source code.
2. `Function` - (optional) If you pass a callback the HTTP request is made asynchronous. See [this note](#using-callbacks) for details.

##### Returns

`Object` - Contract and compiler info.


##### Example

```js
var source = "" + 
    "contract test {\n" +
    "   function multiply(uint a) returns(uint d) {\n" +
    "       return a * 7;\n" +
    "   }\n" +
    "}\n";
var compiled = web3.eth.compile.solidity(source);
console.log(compiled); 
// {
  "test": {
    "code": "0x605280600c6000396000f3006000357c010000000000000000000000000000000000000000000000000000000090048063c6888fa114602e57005b60376004356041565b8060005260206000f35b6000600782029050604d565b91905056",
    "info": {
      "source": "contract test {\n\tfunction multiply(uint a) returns(uint d) {\n\t\treturn a * 7;\n\t}\n}\n",
      "language": "Solidity",
      "languageVersion": "0",
      "compilerVersion": "0.8.2",
      "abiDefinition": [
        {
          "constant": false,
          "inputs": [
            {
              "name": "a",
              "type": "uint256"
            }
          ],
          "name": "multiply",
          "outputs": [
            {
              "name": "d",
              "type": "uint256"
            }
          ],
          "type": "function"
        }
      ],
      "userDoc": {
        "methods": {}
      },
      "developerDoc": {
        "methods": {}
      }
    }
  }
}
```

***

#### web3.eth.compile.lll

    web3. eth.compile.lll(sourceString [, callback])

Compiles LLL source code.

##### Parameters

1. `String` - The LLL source code.
2. `Function` - (optional) If you pass a callback the HTTP request is made asynchronous. See [this note](#using-callbacks) for details.

##### Returns

`String` - The compiled LLL code as HEX string.


##### Example

```js
var source = "...";

var code = web3.eth.compile.lll(source);
console.log(code); // "0x603880600c6000396000f3006001600060e060020a600035048063c6888fa114601857005b6021600435602b565b8060005260206000f35b600081600702905091905056"
```

***

#### web3.eth.compile.serpent

    web3.eth.compile.serpent(sourceString [, callback])

Compiles serpent source code.

##### Parameters

1. `String` - The serpent source code.
2. `Function` - (optional) If you pass a callback the HTTP request is made asynchronous. See [this note](#using-callbacks) for details.

##### Returns

`String` - The compiled serpent code as HEX string.


```js
var source = "...";

var code = web3.eth.compile.serpent(source);
console.log(code); // "0x603880600c6000396000f3006001600060e060020a600035048063c6888fa114601857005b6021600435602b565b8060005260206000f35b600081600702905091905056"
```

***

#### web3.eth.namereg

    web3.eth.namereg

Returns GlobalRegistrar object.

##### Usage

see [namereg](https://github.com/ethereum/web3.js/blob/master/example/namereg.html) example

***

### web3.db

#### web3.db.putString

    web3.db.putString(db, key, value)

This method should be called, when we want to store a string in the local leveldb database.

##### Parameters

1. `String` - The database to store to.
2. `String` - The name of the store.
3. `String` - The string value to store.

##### Returns

`Boolean` - `true` if successfull, otherwise `false`.

##### Example

 param is db name, second is the key, and third is the string value.
```js
web3.db.putString('testDB', 'key', 'myString') // true
```

***

#### web3.db.getString

    web3.db.getString(db, key)

This method should be called, when we want to get string from the local leveldb database.

##### Parameters

1. `String` - The database string name to retrieve from.
2. `String` - The name of the store.

##### Returns

`String` - The stored value.

##### Example
 param is db name and second is the key of string value.
```js
var value = web3.db.getString('testDB', 'key');
console.log(value); // "myString"
```

***

#### web3.db.putHex

    web3.db.putHex(db, key, value)

This method should be called, when we want to store binary data in HEX form in the local leveldb database.

##### Parameters

1. `String` - The database to store to.
2. `String` - The name of the store.
3. `String` - The HEX string to store.

##### Returns

`Boolean` - `true` if successfull, otherwise `false`.

##### Example
```js
web3.db.putHex('testDB', 'key', '0x4f554b443'); // true

```

***

#### web3.db.getHex

    web3.db.getHex(db, key)

This method should be called, when we want to get a binary data in HEX form from the local leveldb database.

##### Parameters

1. `String` - The database to store to.
2. `String` - The name of the store.

##### Returns

`String` - The stored HEX value.


##### Example
 param is db name and second is the key of value.
```js
var value = web3.db.getHex('testDB', 'key');
console.log(value); // "0x4f554b443"
```

***

### web3.shh

[Whisper  Overview](https://github.com/ethereum/wiki/wiki/Whisper-Overview)

##### Example

```js
var shh = web3.shh;
```

***

#### web3.shh.post

   web3.shh.post(object [, callback])

This method should be called, when we want to post whisper message to the network.

##### Parameters

1. `Object` - The post object:
  - `from`: `String`, 60 Bytes HEX - (optional) The identity of the sender.
  - `to`: `String`, 60 Bytes  HEX - (optional) The identity of the receiver. When present whisper will encrypt the message so that only the receiver can decrypt it.
  - `topics`: `Array of Strings` - Array of topics `Strings`, for the receiver to identify messages.
  - `payload`: `String|Number|Object` - The payload of the message. Will be autoconverted to a HEX string before.
  - `priority`: `Number` - The integer of the priority in a rang from ... (?).
  - `ttl`: `Number` - integer of the time to live in seconds.
2. `Function` - (optional) If you pass a callback the HTTP request is made asynchronous. See [this note](#using-callbacks) for details.

##### Returns

`Boolean` - returns `true` if the message was send, otherwise `false`.


##### Example

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

Should be called to create new identity.

##### Parameters

1. `Function` - (optional) If you pass a callback the HTTP request is made asynchronous. See [this note](#using-callbacks) for details.


##### Returns

`String` - A new identity HEX string.


##### Example

```js
var identity = web3.shh.newIdentity();
console.log(identity); // "0xc931d93e97ab07fe42d923478ba2465f283f440fd6cabea4dd7a2c807108f651b7135d1d6ca9007d5b68aa497e4619ac10aa3b27726e1863c1fd9b570d99bbaf"
```

***

#### web3.shh.hasIdentity

    web3.shh.hasIdentity(identity, [callback])

Should be called, if we want to check if user has given identity.

##### Parameters

1. `String` - The identity to check.
2. `Function` - (optional) If you pass a callback the HTTP request is made asynchronous. See [this note](#using-callbacks) for details.

##### Returns

`Boolean` - returns `true` if the identity exists, otherwise `false`.


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

```js
var filter = web3.shh.filter(options)

// watch for changes
filter.watch(function(error, result){
  if (!error)
    console.log(result);
});
```

Watch for incoming whisper messages.

##### Parameters

1. `Object` - The filter options:
  * `topics`: `Array of Strings` - Filters messages by this topic(s). You can use the following combinations:
    - `['topic1', 'topic2'] == 'topic1' && 'topic2'`
    - `['topic1', ['topic2', 'topic3']] == 'topic1' && ('topic2' || 'topic3')`
    - `[null, 'topic1', 'topic2'] == ANYTHING && 'topic1' && 'topic2'` -> `null` works as a wildcard
  * `to`: Filter by identity of receiver of the message. If provided and the node has this identity, it will decrypt incoming encrypted messages.
2. `Function` - (optional) If you pass a callback the HTTP request is made asynchronous. See [this note](#using-callbacks) for details.

##### Callback return

`Object` - The incoming message:

  - `from`: `String`, 60 Bytes - The sender of the message, if a sender was specified.
  - `to`: `String`, 60 Bytes - The receiver of the message, if a receiver was specified.
  - `expiry`: `Number` - Integer of the time in seconds when this message should expire (?).
  - `ttl`: `Number` -  Integer of the time the message should float in the system in seconds (?).
  - `sent`: `Number` -  Integer of the unix timestamp when the message was sent.
  - `topics`: `Array of String` - Array of `String` topics the message contained.
  - `payload`: `String` - The payload of the message.
  - `workProved`: `Number` - Integer of the work this message required before it was send (?).