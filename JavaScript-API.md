**Note:** *The JS API is currently under heavy development, therefore not everything whats written here will work as expected with the latest develop branch. Please stay tuned while we fix that.*

# Introduction

To make your ÐApp work with on Ethereum, you can use the `web3` object provided by the [ethereum.js library](https://github.com/ethereum/ethereum.js). ethereumjs communicates to a local node through RPC calls which can be found [here](https://github.com/ethereum/wiki/wiki/JSON-RPC). ethereum.js works with AlethZero and Mist, and also in an external browser if one of the former nodes are running locally.

`web3` contains the `eth` object - `web3.eth` (for specifically Ethereum blockchain interactions) and the `shh` object - `web3.shh` (for Whisper interaction). Over time we'll introduce other objects for each of the other web3 protocols.

## A note on big numbers in JavaScript

You will always get a BigNumber object for balance values as JavaScript is not able to handle big numbers correctly.
Look at the following examples:

```js
"101010100324325345346456456456456456456"
// "101010100324325345346456456456456456456"
101010100324325345346456456456456456456
// 1.0101010032432535e+38
```

Ethereum.js depends on the [BigNumber Library](https://github.com/MikeMcl/bignumber.js/) dependency, so you need to add it to your project if not present.

To make easy calculations with *wei* balances use the following:

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
  * [version](#) (not available yet)
     * [network](#) -> string e.g. '0.0.1' (the node version)
     * [api](#) -> string e.g. '0.0.1' (the ethereum.js version)
     * [client](#) -> string e.g. 'AlethZero/1.0.0' (the client ID)
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
    * [getStorage(address)](#web3ethgetstorage) -> object
    * [getStorageAt(address, position)](#web3ethgetstorageat) -> hexString
    * [getData(address)](#web3ethgetdata) -> hexString
    * [getBlock(hash/number)](#web3ethgetblock) -> headerObject
    * [getBlockTransactionCount(hash/number)](#web3ethgetblocktransactioncount) -> Integer
    * [getUncle(hash/number)](#web3ethgetuncle) -> headerObject
    * [getBlockUncleCount(hash/number)](#web3ethgetblockunclecount) -> Integer
    * [getTransaction(object, number)](#web3ethgettransaction) -> transactionObject
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
    * [put(name, key, value)](#web3dbput)
    * [putString(name, key, value)](#web3dbputstring)
    * [get(name, key)](#web3dbget)
    * [getString(name, key)](#web3dbgetstring)
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

##### web3
The `web3` object can be used for general data handling.
```javascript
var web3 = require('web3')
```

***

##### web3.sha3

    web3.sha3(string [, callback])

If you pass an optional callback the HTTP request is made asynchronous.

**Returns** the SHA3 of the given data.

```javascript
var str = web3.sha3("Some ASCII string to be hashed");
console.log(str); // "0x536f6d6520415343494920737472696e6720746f20626520686173686564"

var hash = web3.sha3(str);
console.log(hash); // "0xb21dbc7a5eb6042d91f8f584af266f1a512ac89520f43562c6c1e37eab6eb0c4"
```

***

##### web3.setProvider

    web3.setProvider(providor)

**Returns** undefined

Should be called to set provider.
```javascript
web3.setProvider(new web3.providers.HttpProvider('http://localhost:8545')); // 8080 for cpp/AZ, 8545 for go/mist
// or
web3.setProvider(new web3.providers.QtSyncProvider());
```

***

##### web3.reset

    web3.reset()

**Returns** undefined

Should be called to reset state of web3. Resets everything except manager. Uninstalls all filters. Stops polling.
```javascript
web3.reset();
```

***

##### web3.toHex

    web3.toHex(mixed);

**Returns** the hex string of `mixed`.

Following input values are possible:

- string
- number or string of number
- BigNumber instance -> will be converted to a number
- hex string
- array (will be stringified before)
- object (will be stringified before)

```javascript
var str = web3.toHex({test: 'test'});
console.log(str); // '0x7b2274657374223a2274657374227d'
```

***
##### web3.toAscii

    web3.toAscii(hexString);

**Returns** an ASCII string made from the data `hexString`.
```javascript
var str = web3.toAscii("0x657468657265756d000000000000000000000000000000000000000000000000");
console.log(str); // ethereum
```

***

##### web3.fromAscii

    web3.fromAscii(string[, padding]);

**Returns** data of the ASCII string `string` and padded to `padding` bytes (default to 32) and left-aligned.
```javascript
var str = web3.fromAscii('ethereum');
console.log(str); // "0x657468657265756d000000000000000000000000000000000000000000000000"

var str2 = web3.fromAscii('ethereum', 32);
console.log(str2); // "0x657468657265756d000000000000000000000000000000000000000000000000"
```

***

##### web3.toDecimal

    web3.toDecimal(hexString);

**Returns** the decimal string representing the data `hexString` (when interpreted as a big-endian integer).
```javascript
var value = web3.toDecimal('0x15');
console.log(value === "21"); // true
```

***

##### web3.fromDecimal

    web3.fromDecimal(number);

**Returns** the HEX string representing the decimal integer `number`.
```javascript
var value = web3.fromDecimal('21');
console.log(value === "0x15"); // true
```

***

##### web3.fromWei

    web3.fromWei([String,BigNumber] number, [String] unit)

**Returns** either a number string, or a BigNumber object, depending on the given `number` parameter.

Converts a number of wei into the following ethereum units:

- kwei/ada
- mwei/babbage
- gwei/shannon
- szabo
- finney
- ether
- kether/grand/einstein
- mether
- gether
- tether

```javascript
var value = web3.fromWei('21000000000000', 'finney');
console.log(value); // "0.021"
```

***

##### web3.toWei

    web3.toWei([String,BigNumber] number, [String] unit)

**Returns** either a number string, or a BigNumber object, depending on the given `number` parameter.

Converts a number a ethereum unit into wei. Possible units are:

- kwei/ada
- mwei/babbage
- gwei/shannon
- szabo
- finney
- ether
- kether/grand/einstein
- mether
- gether
- tether


```javascript
var value = web3.toWei('1', 'ether');
console.log(value); // "1000000000000000000"
```

***

##### web3.toBigNumber

    web3.toBigNumber(numberOrHexString);

**Returns** a BigNumber object representing the given value

See the [note on BigNumber](#a-note-on-big-numbers-in-javascript).

```javascript
var value = web3.toBigNumber('200000000000000000000001');
console.log(value); // instanceOf BigNumber
console.log(value.toNumber()); // 2.0000000000000002e+23
console.log(value.toString(10)); // '200000000000000000000001'
```

***

##### web3.net.listening

    web3.net.listening

**Returns** `true` if and only if the client is actively listening for network connections, otherwise `false`.

```javascript
var listening = web3.net.listening;
console.log(listening); // true of false
```

***

##### web3.net.peerCount

    web3.net.peerCount

**Returns** the number of peers currently connected to the client.

```javascript
var peerCount = web3.net.peerCount;
console.log(peerCount); // 4
```

***

##### web3.eth
Contains the ethereum blockchain related methods.

```javascript
var eth = web3.eth;
```

***

##### web3.eth.coinbase

    web3.eth.coinbase

**Returns** the coinbase address of the client.

The coinbase is the address were the mining rewards go into.

```javascript
var coinbase = web3.eth.coinbase;
console.log(coinbase); // "0x407d73d8a49eeb85d32cf465507dd71d507100c1"
```

***

##### web3.eth.mining

    web3.eth.mining

**Returns** `true` if the client is mining, otherwise `false`.

```javascript
var mining = web3.eth.mining;
console.log(mining); // true or false
```

***

##### web3.eth.gasPrice

    web3.eth.gasPrice

**Returns ->** a BigNumber object of the current gas price in wei.

The gas price is determined by the x latest blocks median gas price.

See the [note on BigNumber](#a-note-on-big-numbers-in-javascript).

```javascript
var gasPrice = web3.eth.gasPrice;
console.log(gasPrice.toString(10)); // "10000000000000"
```

***

##### web3.eth.accounts

    web3.eth.accounts

**Returns** an array of the addresses owned by client.

```javascript
var accounts = web3.eth.accounts;
console.log(accounts); // ["0x407d73d8a49eeb85d32cf465507dd71d507100c1"] 
```

***

##### web3.eth.register

    web3.eth.register(addressHexString)

**Returns** ?

Registers the given address to be included in `web3.eth.accounts`. This allows non-private-key owned accounts to be associated as an owned account (e.g., contract wallets).

```javascript
web3.eth.register("0x407d73d8a49eeb85d32cf465507dd71d507100ca")
```

***

##### web3.eth.unRegister

     web3.eth.unRegister(addressHexString [, callback])

If you pass an optional callback the HTTP request is made asynchronous.

**Returns** ?

Unregisters a given address.

```javascript
web3.eth.unregister("0x407d73d8a49eeb85d32cf465507dd71d507100ca")
```

***

##### web3.eth.defaultBlock

    web3.eth.defaultBlock

**Returns** the default block number/age to use when querying a state.

This default block is used for the following methods (optionally you can overwrite the defaultBlock by passing it as the last parameter):

- [web3.eth.getBalance()](#web3ethgetbalance)
- [web3.eth.getData()](#web3ethgetdata)
- [web3.eth.getTransactionCount()](#web3ethgettransactioncount)
- [web3.eth.getStorage()](#web3ethgetstorage)
- [web3.eth.getStorageAt()](#web3ethgetstorageat)
- [web3.eth.call()](#web3ethcall)

When positive this is a block number, when 0 or negative it is a block age. `-1` therefore means the most recently mined block, `0` means the block being currently mined (i.e. to include pending transactions). Defaults to -1.

```javascript
var defaultBlock = web3.eth.defaultBlock;
console.log(defaultBlock); // -1

// set the default block
web3.eth.defaultBlock = 0;
```

***

##### web3.eth.blockNumber

    web3.eth.blockNumber

**Returns** the number of the most recent block.

```javascript
var number = web3.eth.blockNumber;
console.log(number); // 2744
```

***

##### web3.eth.getBalance

    web3.eth.getBalance(addressHexString [, defaultBlock] [, callback])

- If you pass an optional defaultBlock it will not use the default [web.eth.defaultBlock](#web3ethdefaultblock).
- If you pass an optional callback the HTTP request is made asynchronous.

**Returns** a BigNumber object of the current balance for the given address in wei.

See the [note on BigNumber](#a-note-on-big-numbers-in-javascript).

```javascript
var balance = web3.eth.getBalance("0x407d73d8a49eeb85d32cf465507dd71d507100c1");
console.log(balance); // instanceof BigNumber
console.log(balance.toString(10)); // '1000000000000'
console.log(balance.toNumber()); // 1000000000000
```

***

##### web3.eth.getStorage

    web3.eth.getStorage(addressHexString [, defaultBlock] [, callback])

- If you pass an optional defaultBlock it will not use the default [web.eth.defaultBlock](#web3ethdefaultblock).
- If you pass an optional callback the HTTP request is made asynchronous.

**Returns** the storage as a json object.

```javascript
var storage = web3.eth.getStorage("0x407d73d8a49eeb85d32cf465507dd71d507100c1");
console.log(storage); // { "0x" : "0x03" }
```

***

##### web3.eth.getStorageAt

    web3.eth.getStorageAt(addressHexString, position [, defaultBlock] [, callback])

- If you pass an optional defaultBlock it will not use the default [web.eth.defaultBlock](#web3ethdefaultblock).
- If you pass an optional callback the HTTP request is made asynchronous.

**Returns** the value in storage at position `position` of the address `addressHexString`.

```javascript
var state = web3.eth.getStorageAt("0x407d73d8a49eeb85d32cf465507dd71d507100c1", 0);
console.log(state); // "0x03"
```

***

##### web3.eth.getData

    web3.eth.getData(addressHexString [, defaultBlock] [, callback])

- If you pass an optional defaultBlock it will not use the default [web.eth.defaultBlock](#web3ethdefaultblock).
- If you pass an optional callback the HTTP request is made asynchronous.

**Returns** the data at given address `addressHexString`.

```javascript
var data = web3.eth.getData("0xd5677cf67b5aa051bb40496e68ad359eb97cfbf8");
console.log(data); // "0x600160008035811a818181146012578301005b601b6001356025565b8060005260206000f25b600060078202905091905056"
```

***

##### web3.eth.getBlock

     web3.eth.getBlock(hashHexStringOrBlockNumber, returnTransactionObjectsBoolean [, callback])

If you pass an optional callback the HTTP request is made asynchronous.

**Returns** a block object with number or hash `hashHexStringOrBlockNumber`:

  * `number` (integer): The number of this block.
  * `hash` (32-byte hash): The block hash (i.e. the SHA3 of the RLP-encoded dump of the block's header).
  * `parentHash` (32-byte hash): The parent block's hash (i.e. the SHA3 of the RLP-encoded dump of the parent block's header).
  * `nonce`: (32-byte hash)
  * `sha3Uncles` (32-byte hash): The SHA3 of the RLP-encoded dump of the uncles portion of the block.
  * `logsBloom` (32-byte hash): The bloom filter of this block.  
  * `stateRoot` (32-byte hash): The root of the state trie.
  * `transactionsRoot` (32-byte hash): The root of the block's transactions trie.
  * `miner` (20-byte address): The address of the account that was rewarded for mining this block (né the coinbase address).
  * `difficulty` (BigNumber): The PoW difficulty of this block.
  * `totalDifficulty` (BigNumber): The total difficulty of the entire chain up and including this block.
  * `size` (integer) the size in bytes of the block
  * `minGasPrice` (BigNumber): The minimum price, in Wei per GAS, that the miner accepted for any transactions in this block.
  * `gasLimit` (integer): The gas limit of this block.
  * `gasUsed` (integer): The amount of gas used in this block.
  * `timestamp` (integer): The timestamp of this block (an integer).
  * `extraData` (byte array): Any extra data this block contains.
  * `nonce` (32-byte hash): The block's PoW nonce.
  * `children` (array of 32-byte hashes): The hashes of any children this block has.
  * `transactions` (array) transaction objects or hashes, depending on the last parameter
  * `uncles` (array) hashes of uncles

```javascript
var info = web3.eth.block(3150);
console.log(info);
 /*{
  "number": 3150,
  "difficulty": 125000,
  "totalDifficulty": 124334345000,
  "transactionsRoot": "0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421"
  "hash": "0x452e84d0d0599d779a4e466d0b70d50ca316499a8489604d5b9df436ccfeee",
  "parentHash": "0xf9de6948d835ed5257229b5103f9421a7f70c3ccc65fd31c0db85324e05702f5",
  "nonce": "0xed0c6a53777b15880fb359dfd9368d002c959243a19c35ca1ae2e97f9bf78a53",
  "sha3Uncles": "0x1dcc4de8dec75d7aab85b567b6ccd41ad312451b948a7413f0a142fd40d49347",
  "logsBloom": "0xe84d0d0599d779a4e466d0b70d50ca316499a8489604d5b9df436ccfeee",
  "stateRoot": "0xd878fcee309af964e8e70c66ec25b1e7de9eca9c4f49e90efddeea8f77a37e43",
  "extraData": "0x0000000000000000000000000000000000000000000000000000000000000000",
  "minGasPrice": instanceof BigNumber,
  "gasLimit": 125000,
  "gasUsed": 34345,
  "miner": "0x407d73d8a49eeb85d32cf465507dd71d507100c1",
  "timestamp": 1416585210,
  "transactions": [{...}, {...}],
  "uncles": ["0xkj423h4kj23...", "0xlj4234k2..."]
} */
```

***

##### web3.eth.getBlockTransactionCount

    web3.eth.getBlockTransactionCount(hashStringOrBlockNumber [, callback])

If you pass an optional callback the HTTP request is made asynchronous.

**Returns** the number of transactions in a given block `hashStringOrBlockNumber`.

```javascript
var number = web3.eth.getBlockTransactionCount("0x407d73d8a49eeb85d32cf465507dd71d507100c1");
console.log(number); // 1
```

***

##### web3.eth.getUncle

    web3.eth.getUncle(blockHashStringOrNumber, uncleNumber [, callback])

If you pass an optional callback the HTTP request is made asynchronous.

Returns the uncle with number `uncleNumber` from the block with number or hash `blockHashStringOrNumber`.

Return value see [web3.eth.getBlock()](#web3ethgetblock)

```js
var blockNumber = 500;
var indexOfUncle = 0

var uncle = web3.eth.getUncle(blockNumber, indexOfUncle);
console.log(uncle); // see web3.eth.getBlock

```

***

#####web3.eth.getTransaction

    web3.eth.getTransaction(hashStringOrNumber, blockNumber [, callback])

If you pass an optional callback the HTTP request is made asynchronous.

**Returns** a transaction object by number or hash `hashStringOrNumber` from block with `blockNumber`:

  * `hash` (32-byte hash): The hash of the transaction.
  * `nonce` (32-byte hash): The transaction nonce.
  * `blockHash` (32-byte hash): the blocks hash, where this transaction appeared
  * `blockNumber` (integer):  the blocks number, where this transaction appeared
  * `transactionIndex` (integer):  the index a t which this transaction appeared in the block
  * `to` (20-byte address): The address to which the transaction was sent. This may be the null address (address 0) if it was a contract-creation transaction.
  * `from` (20-byte address): The cryptographically verified address from which the transaction was sent .
  * `gas` (integer): The amount of GAS supplied for this transaction to happen.
  * `gasPrice` (BigNumber): The price offered to the miner to purchase this amount of GAS, in Wei per GAS.
  * `value` (BigNumber): The amount of ETH to be transferred to the recipient with the transaction in wei.
  * `input` (byte array): The binary data that formed the input to the transaction, either the input data if it was a message call or the contract initialisation if it was a contract creation.


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
##### web3.eth.getTransactionCount

    web3.eth.getTransactionCount(addressHexString [, defaultBlock] [, callback])

- If you pass an optional defaultBlock it will not use the default [web.eth.defaultBlock](#web3ethdefaultblock).
- If you pass an optional callback the HTTP request is made asynchronous.

**Returns** the number of transactions send from the given address `addressHexString`.

```javascript
var number = web3.eth.getTransactionCount("0x407d73d8a49eeb85d32cf465507dd71d507100c1");
console.log(number); // 1
```

***

##### web3.eth.sendTransaction

    web3.eth.sendTransaction(transactionObject [, callback])

If you pass an optional callback the HTTP request is made asynchronous.

Creates a new message-call transaction.
  * `transactionObject`, an anonymous object specifying the parameters of the transaction.
    * `from` (hexString), the address for the sending account;
    * `to` (hexString), the destination address of the message, left undefined for a contract-creation transaction
    * `value (number|hexString|BigNumber)`, the value transferred for the transaction in Wei, also the endowment if it's a contract-creation transaction;
    * `endowment`, synonym for `value`;
    * `gas (integer)`, the amount of gas to purchase for the transaction (unused gas is refunded), defaults to the most gas your ether balance allows; and
    * `gasPrice (number|hexString|BigNumber)`, the price of gas for this transaction in wei, defaults to the mean network gasPrice.
    * `data`  (hexString), either a [byte string](https://github.com/ethereum/wiki/wiki/Solidity,-Docs-and-ABI) containing the associated data of the message, or in the case of a contract-creation transaction, the initialisation code;
    * `code`, a synonym for `data`;
  * `callback`, the callback function, called on completion of the transaction. If the transaction was a contract-creation transaction, it is passed with a single argument; the address of the new account.
```javascript

// compile solidity source code
var source = "" + 
"contract test {\n" +
"   function multiply(uint a) returns(uint d) {\n" +
"       return a * 7;\n" +
"   }\n" +
"}\n";

var compiled = web3.eth.solidity(source);

var address = web3.eth.sendTransaction({data: compiled});
console.log(address); // "0x7f9fade1c0d57a7af66ab4ead7c2eb7b11a91385"
```

***

##### web3.eth.contract

    web3.eth.contract(abiArray)

**Returns** a contract object, which can be used to initiate use contracts on an address.

This method creates a contract object for a solidity contract. It has the same methods available as solidity contract description itself.

You can read more about events [here](https://github.com/ethereum/wiki/wiki/Ethereum-Contract-ABI#example-javascript-usage).

usage example:
```javascript
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
        topic: "0x12345678901234567890123456789012", "0x0000000000000000000000000000000000000000000000000000000000000005",
        data: "0x0000000000000000000000000000000000000000000000000000000000000001",
        number: 2
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

myEvent.stopWatching();
```

***

##### web3.eth.call

    web3.eth.call(callObject [, defaultBlock] [, callback])

- If you pass an optional defaultBlock it will not use the default [web.eth.defaultBlock](#web3ethdefaultblock).
- If you pass an optional callback the HTTP request is made asynchronous.

**Returns** the data which matches the call.

Executes a new message-call immediately without creating a transaction on the block chain. `callObject` is an object specifying the parameters of the transaction.

```javascript
var options = {
    to: "0xc4abd0339eb8d57087278718986382264244252f", 
    data: "0xc6888fa10000000000000000000000000000000000000000000000000000000000000003"
};
var result = web3.eth.call(options);
console.log(result); // "0x0000000000000000000000000000000000000000000000000000000000000015"
```

***

##### web3.eth.filter

```js
// can be 'chain' or 'pending'
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
   * `filterString`:  `'chain'` or `'pending'` to watch for changes in the chain or pending transactions respectively
   * `options`
       * `fromBlock`: The number of the earliest block (-1 may be given to mean the most recent, currently mining, block).
       * `toBlock`: The number of the latest block (-1 may be given to mean the most recent, currently mining, block).
       * `limit`: The maximum number of messages to return.
       * `offset`: The number of messages to skip before the list is constructed. May be used with `max` to paginate messages into multiple calls.
       * `address`: An address or a list of addresses to restrict log entries by requiring them to be made from a particular account.
       * `topic`: A set of values which must each appear in the log entries. (string or array of strings)
   * `eventArguments` is an object with keys of one or more indexed arguments for the event(s) and values of either one (directly) or more (in an array) e.g. {'a': 1, 'b': [myFirstAddress, mySecondAddress]}.

**Returns** a filter object with the following methods:

#### Filter Methods:
  * `filter.watch(callback)`: Watches for state changes that fit the filter and calls the callback.
  * `filter.stopWatching()`: Stops the watch and uninstalls the filter in the node. Should always be called once it is done.
  * `filter.get()`: Returns all of the log entries that fit the filter.

#### Callback return values

If its a log filter it returns a list of log entries; each includes the following fields:

* `address`: The address of the account whose execution of the message resulted in the log entry being made.
* `topic`: The topic(s) of the log.
* `data`: The associated data of the log.
* `number`: The block number from which this log is.

If its an event filter it returns a filter object with the return values of event:

* `args`: The arguments coming from the event
* `topic`: The topic(s) of the log.
* `number`: The block number from at which this event happened.

```javascript
var filter = web3.eth.filter('pending');

filter.watch(function (log) {
  console.log(log); //  {"address":"0x0000000000000000000000000000000000000000","data":"0x0000000000000000000000000000000000000000000000000000000000000000","number":0}
});

var myResults = filter.get();

filter.stopWatching();

```

***

##### web3.eth.getCompilers

    web3.eth.getCompilers([callback])

If you pass an optional callback the HTTP request is made asynchronous.

**Returns** an array of available compilers.


```javascript
var number = web3.eth.getCompilers();
console.log(number); // ["lll", "solidity", "serpent"]
```

***

##### web3.eth.compile.solidity

    web3.eth.compile.solidity(sourceString [, callback])

If you pass an optional callback the HTTP request is made asynchronous.

**Returns** the compiled solidity code as HEX string.


Compiles the solidity source code `sourceString` and returns the output data.
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

##### web3.eth.compile.lll

    web3. eth.compile.lll(sourceString [, callback])

If you pass an optional callback the HTTP request is made asynchronous.

**Returns** the compiled lll code as HEX string.


Compiles the LLL source code `sourceString` and returns the output data.
```javascript
var source = "...";

var code = web3.eth.compile.lll(source);
console.log(code); // "0x603880600c6000396000f3006001600060e060020a600035048063c6888fa114601857005b6021600435602b565b8060005260206000f35b600081600702905091905056"
```

***

##### web3.eth.compile.serpent

    web3.eth.compile.serpent(sourceString [, callback])

If you pass an optional callback the HTTP request is made asynchronous.

**Returns** the compiled serpent code as HEX string.


Compiles the serpent source code `sourceString ` and returns the output data.

```js
var source = "...";

var code = web3.eth.compile.serpent(source);
console.log(code); // "0x603880600c6000396000f3006001600060e060020a600035048063c6888fa114601857005b6021600435602b565b8060005260206000f35b600081600702905091905056"
```

***

#####web3.eth.flush

```javascript
// TODO:
```

***

##### web3.db.put
This method should be called, when we want to store number in local leveldb database.
First param is db name, second is the key, and third is the value.
```javascript
var result = web3.db.put('test', 'key', "5");
console.log(result); // true

var value = web3.db.get('test', 'key');
console.log(value); // "0x05"
```

***

##### web3.db.putString
This method should be called, when we want to store string in local leveldb database.
First param is db name, second is the key, and third is the string value.
```javascript
var result = web3.db.putString('test', 'key', "5")
console.log(result); // true

var value = web3.db.get('test', 'key');
console.log(value); // "5"
```

***

##### web3.db.get
This method should be called, when we want to get number value from local leveldb database.
First param is db name and second is the key of value.
```javascript
var result = web3.db.put('test', 'key', "5");
console.log(result); // true

var value = web3.db.get('test', 'key');
console.log(value); // "0x05"
```

***

##### web3.db.getString
This method should be called, when we want to get string value from local leveldb database.
First param is db name and second is the key of string value.
```javascript
var result = web3.db.putString('test', 'key', "5")
console.log(result); // true

var value = web3.db.get('test', 'key');
console.log(value); // "5"
```

***

##### web3.shh
[Whisper  Overview](https://github.com/ethereum/wiki/wiki/Whisper-Overview)
```javascript
var shh = web3.shh;
```

***

##### web3.shh.post

   web3.shh.post(object [, callback])

If you pass an optional callback the HTTP request is made asynchronous.

This method should be called, when we want to post whisper message. `_message` object may have following fields:
  * `from`: identity of sender as hexString
  * `to`: identity of receiver as hexString
  * `payload`: message payload
  * `ttl`: time to live in seconds
  * `workToProve`: // or priority TODO
  * `topic`: string or array of strings, with message topics
```js
var identity = web3.shh.newIdentity();
var topic = 'example';
var payload = 'hello whisper world!';

var message = {
  from: identity,
  topic: [topic],
  payload: payload,
  ttl: 100,
  workToProve: 100 // or priority TODO
};

web3.shh.post(message);
```

***

##### web3.shh.newIdentity

    web3.shh.newIdentity([callback])

If you pass an optional callback the HTTP request is made asynchronous.

Should be called to create new identity.

**Returns** a new identity hex string.

```js
var identity = web3.shh.newIdentity();
console.log(identity); // "0xc931d93e97ab07fe42d923478ba2465f283f440fd6cabea4dd7a2c807108f651b7135d1d6ca9007d5b68aa497e4619ac10aa3b27726e1863c1fd9b570d99bbaf"
```

***

##### web3.shh.hasIdentity

    web3.shh.hasIdentity([callback])

If you pass an optional callback the HTTP request is made asynchronous.

Should be called, if we want to check if user has given identity. Accepts one param. Returns true if he has, otherwise false.

**Returns** a boolean whether or not the identity exists.


```javascript
var identity = web3.shh.newIdentity();
var result = web3.shh.hasIdentity(identity);
console.log(result); // true

var result2 = web3.shh.hasIdentity(identity + "0");
console.log(result2); // false
```

***

##### web3.shh.newGroup
```javascript
// TODO: not implemented yet
```

***

##### web3.shh.addToGroup
```javascript
// TODO: not implemented yet
```

***

##### web3.shh.filter

    wev3.shh.filter(options)

This method should be used, when you want to watch whisper messages.
Available filter options are:
* `topic`: string or array of strings. filter messages with this topic
* `to`: filter the identity of receiver of the message

```javascript
var topic = 'example';

var options = {
  topic: [topic],
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
  "topic":['myTopic'],
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
</script>
</body></html>
```

To test it, just put it in file and save. Load it in AlethZero and point the URL to file:///WHEREVER_YOU_SAVED_IT

Job done. Now go create.

more examples can be found [here](https://github.com/ethereum/ethereum.js/tree/master/example)