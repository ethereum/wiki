# Introduction

To make your ÐApp work with on Ethereum, you'll need to know about the Ethereum Javascript bindings, or, if you like, magic Javascript objects. These bindings may be used with AlethZero, Mist and external browser. These bindings can be found at [ethereum.js](https://github.com/ethereum/ethereum.js) repository.

There is, at the global scope, one objects; the `web3` object, containing data handling functions (commonly used for all other APIs). This object also contains other subprotocol objects including the `eth` object - `web3.eth` (for specifically Ethereum interaction) and the `shh` object - `web3.shh` (for Whisper interaction). Over time we'll introduce other objects for each of the other web3 protocols.

# API

* [web3](#web3)
  * [version](#) (not available yet)
     * [network](#) -> string e.g. '0.0.1' (the node version)
     * [api](#) -> string e.g. '0.0.1' (the ethereum.js version)
     * [client](#) -> string e.g. 'AlethZero/1.0.0' (the client ID)
  * [port](#) -> number e.g. 8080 (not available yet)
  * [sha3](#web3sha3) *(s1)*
  * [toAscii](#web3toascii) *(hexString)*, returns textString
  * [fromAscii](#web3fromascii) *(textString, [padding])*, returns hexString
  * [toDecimal](#web3todecimal) *(hexString)*, returns number
  * [fromDecimal](#web3fromdecimal) *(number)*, returns hexString
  * [fromWei](#web3fromwei) *(number, unit)*, returns number|BigNumber (depending on the input)
  * [toWei](#web3toWei) *(number, unit)*, returns number|BigNumber (depending on the input)
  * [isAddress](#web3isAddress) *(hexString)*, returns boolean
  * [setProvider](#web3setprovider) *(provider)*
  * [reset](#web3reset)
  * [eth](#web3eth)
    * [coinbase](#web3ethcoinbase) -> hexString
    * [listening](#web3ethlistening) -> boolean
    * [mining](#web3ethmining) -> boolean
    * [gasPrice](#web3ethgasprice) -> BigNumber
    * [accounts](#web3ethaccounts) -> array of hexStrings
    * [register(hexString)](#web3ethregister)
    * [unRegister(hexString)](#web3ethunregister)
    * [peerCount](#web3ethpeercount) -> Integer
    * [defaultBlock](#web3ethdefaultblock) -> Integer
    * [blockNumber](#web3ethblocknumber) -> Integer
    * [getBalance(address)](#web3ethgetbalance) -> BigNumber
    * [getState(address, storage)](#web3ethgetstate) -> hexString
    * [getStorage(address)](#web3ethgetstorage) -> hexString
    * [getTransactionCount(address)](#web3ethgettransactioncount) -> Integer
    * [getBlockTransactionCount(hash/number)](#web3ethgetblocktransactioncount) -> Integer
    * [getCode(address)](#web3ethgetcode) -> hexString
    * [sendTransaction(object)](#web3ethsendtransaction)
    * [call(object)](#web3ethcall) -> hexString
    * [getBlock(hash/number)](#web3ethgetblock) -> headerObject
    * [getTransaction(object, number)](#web3ethgettransaction) -> transactionObject
    * [getUncle(hash/number)](#web3ethgetuncle) -> headerObject
    * [getBlockUncleCount(hash/number)](#web3ethgetblockunclecount) -> Integer
    * [getCompilers()](#web3ethgetcompilers) -> array of strings
    * [compile.lll(string)](#web3ethcompilelll) -> hexString
    * [compile.solidity(string)](#web3ethcompilesolidity) -> hexString
    * [compile.serpent(string)](#web3ethcompileserpent) -> hexString
    * [logs](#web3ethlogs) *(_object/_string)*
    * [filter(array (, options) )](#web3ethfilter)
        - [watch(callback)](#web3ethfilter)
        - [stopWatching(callback)](#web3ethfilter)
        - [get()](#web3ethfilter)
    * [contract](#web3ethcontract) *(abiArray)*
    * [flush](#web3ethflush)
  * [db](#web3db)
    * [put](#web3dbput) *(_name, _key, _value)*
    * [putString](#web3dbputstring) *(__name, _key, _value)*
    * [get](#web3dbget) *(_name, _key)*
    * [getString](#web3dbgetstring) *(_name, _key)*
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

# Parameters

The canonical form of almost all parameters in this API is data represented as hex, prefixed with an `0x`. There's automatic conversion from decimal strings to the hex representation (interpreted as a big-endian as is standard for Ethereum). So, the following two forms are identically interpreted:

* `"0x414243"`
* `"4276803"`

In each case, they are interpreted as the number 4276803. To convert to or from other datatypes, such as strings and integers, there are a number of conversion functions, detailed later.

# Usage

##### web3
The `web3` object can be used for general data handling.
```javascript
var web3 = require('web3')
```

***

##### web3.sha3

    web3.sha3(string)

Returns the SHA3 of the given data.
```javascript
var str = web3.sha3("Some ASCII string to be hashed");
console.log(str); // "0x536f6d6520415343494920737472696e6720746f20626520686173686564"

var hash = web3.sha3(str);
console.log(hash); // "0xb21dbc7a5eb6042d91f8f584af266f1a512ac89520f43562c6c1e37eab6eb0c4"
```

***

##### web3.toAscii

    web3.toAscii(hexString);

Returns an ASCII string made from the data `hexString`.
```javascript
var str = web3.toAscii("0x657468657265756d000000000000000000000000000000000000000000000000");
console.log(str); // ethereum
```

***

##### web3.fromAscii

    web3.fromAscii(string, padding);

Returns data of the ASCII string `string`, auto-padded to `padding` bytes (default to 32) and left-aligned.
```javascript
var str = web3.fromAscii('ethereum');
console.log(str); // "0x657468657265756d000000000000000000000000000000000000000000000000"

var str2 = web3.fromAscii('ethereum', 32);
console.log(str2); // "0x657468657265756d000000000000000000000000000000000000000000000000"
```

***

##### web3.toDecimal

    web3.toDecimal(hexString);

Returns the decimal string representing the data `hexString` (when interpreted as a big-endian integer).
```javascript
var value = web3.toDecimal('0x15');
console.log(value === "21"); // true
```

***

##### web3.fromDecimal

    web3.fromDecimal(number);

Returns the hex data string representing (in big-endian format) the decimal integer `number`.
```javascript
var value = web3.fromDecimal('21');
console.log(value === "0x15"); // true
```

***

##### web3.fromWei

    web3.fromWei([String,BigNumber] number, [String] unit)

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

**Note** If you pass a BigNumber object, it will return one as well.

```javascript
var value = web3.fromWei('21000000000000', 'finney');
console.log(value === "0.021"); // true
```

***

##### web3.toWei

    web3.toWei([String,BigNumber] number, [String] unit)

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

**Note** If you pass a BigNumber object, it will return one as well.

```javascript
var value = web3.toWei('1', 'ether');
console.log(value === "1000000000000000000"); // true
```

***

##### web3.setProvider

    web3.setProvider(providor)

Should be called to set provider.
```javascript
web3.setProvider(new web3.providers.HttpSyncProvider());
// or
web3.setProvider(new web3.providers.QtSyncProvider());
```

***

##### web3.reset

    web3.reset()

Should be called to reset state of web3. Resets everything except manager. Uninstalls all filters. Stops polling.
```javascript
web3.reset();
```

***

##### web3.eth
Should be called to get web3.eth object.
```javascript
var eth = web3.eth;
```

***

##### web3.eth.coinbase

    web3.eth.coinbase

Returns the coinbase address of the client.
```javascript
var coinbase = web3.eth.coinbase;
console.log(coinbase); // "0x407d73d8a49eeb85d32cf465507dd71d507100c1"
```

***

##### web3.eth.listening

    web3.eth.listening

Returns `true` if and only if the client is actively listening for network connections.
```javascript
var listening = web3.eth.listening;
console.log(listening); // true of false
```

***

##### web3.eth.mining

    web3.eth.mining

Returns `true` if and only if the client is actively mining new blocks.
```javascript
var mining = web3.eth.mining;
console.log(mining); // true or false
```

***

##### web3.eth.gasPrice

    web3.eth.gasPrice

**Returns ->** BigNumber() in wei

Returns the current gasPrice in wei.
```javascript
var gasPrice = web3.eth.gasPrice;
console.log(gasPrice.toString(10)); // "10000000000000"
```

***

##### web3.eth.accounts

    web3.eth.accounts

Returns an array of the addresses owned by client
```javascript
var accounts = web3.eth.accounts;
console.log(accounts); // ["0x407d73d8a49eeb85d32cf465507dd71d507100c1"] 
```

***

##### web3.eth.register

    web3.eth.register(addressHexString)

Registers the given address to be included in `web3.eth.accounts`. This allows non-private-key owned accounts to be associated as an owned account (e.g., contract wallets).
```javascript
web3.eth.register("0x407d73d8a49eeb85d32cf465507dd71d507100ca")
```

***

##### web3.eth.unRegister

     web3.eth.unRegister(addressHexString)

Unregisters the given address
```javascript
web3.eth.unregister("0x407d73d8a49eeb85d32cf465507dd71d507100ca")
```

***

##### web3.eth.peerCount

    web3.eth.peerCount

Returns the number of peers currently connected to the client.
```javascript
var peerCount = web3.eth.peerCount;
console.log(peerCount); // 4
```

***

##### web3.eth.defaultBlock

    web3.eth.defaultBlock

The default block number/age to use when querying state. When positive this is a block number, when 0 or negative it is a block age. `-1` therefore means the most recently mined block, `0` means the block being currently mined (i.e. to include pending transactions). Defaults to -1.
```javascript
var defaultBlock = web3.eth.defaultBlock;
console.log(defaultBlock); // -1

// set a block
web3.eth.defaultBlock = 0;
```

***

##### web3.eth.blockNumber

    web3.eth.blockNumber

Returns the number of the most recent block.
```javascript
var number = web3.eth.blockNumber;
console.log(number); // 2744
```

***

##### web3.eth.getBalance

    web3.eth.getBalance(addressHexString)

**Returns ->** BigNumber() in wei

Returns the balance of the account of address given by the address `addressHexString`
```javascript
var balance = web3.eth.getBalance("0x407d73d8a49eeb85d32cf465507dd71d507100c1");
console.log(balance); // instanceof BigNumber
console.log(balance.toString(10)); // '1000000000000'
console.log(balance.toNumber()); // 1000000000000
```

***

##### web3.eth.getState

    web3.eth.getState(addressHexString, position)

Returns the value in storage at position `position` of the account by the given address `addressHexString`.
```javascript
var state = web3.eth.getState("0x407d73d8a49eeb85d32cf465507dd71d507100c1", 0);
console.log(state); // "0x03"
```

***

##### web3.eth.getStorage

    web3.eth.getStorage(addressHexString)

Dumps storage as json object.
```javascript
var storage = web3.eth.getStorage("0x407d73d8a49eeb85d32cf465507dd71d507100c1");
console.log(storage); // { "0x" : "0x03" }
```

***

##### web3.eth.getTransactionCount

    web3.eth.getTransactionCount(addressHexString)

Returns the number of transactions send from the account of address given by `addressHexString`.
```javascript
var number = web3.eth.getTransactionCount("0x407d73d8a49eeb85d32cf465507dd71d507100c1");
console.log(number); // 1
```

***

##### web3.eth.getBlockTransactionCount

    web3.eth.getBlockTransactionCount(hashStringOrBlockNumber)

Returns the number of transactions in a given block `hashStringOrBlockNumber`.
```javascript
var number = web3.eth.getBlockTransactionCount("0x407d73d8a49eeb85d32cf465507dd71d507100c1");
console.log(number); // 1
```

***

##### web3.eth.getCode

    web3.eth.getCode(addressHexString)

Returns code at given address `addressHexString`.
```javascript
var code = web3.eth.getCode("0xd5677cf67b5aa051bb40496e68ad359eb97cfbf8");
console.log(code); // "0x600160008035811a818181146012578301005b601b6001356025565b8060005260206000f25b600060078202905091905056"
```

***

##### web3.eth.getBlock

     web3.eth.getBlock([string, number]hasHexStringOrBlockNumber)

Returns the block with number `hasHexStringOrBlockNumber`. Return value is an object with the following keys:

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
```javascript
var info = web3.eth.block(3150);
console.log(info);
 /*{
  "difficulty": "0x02a88f",
  "extraData": "0x0000000000000000000000000000000000000000000000000000000000000000",
  "gasLimit": 125000,
  "hash": "80452e84d0d0599d779a4e466d0b70d50ca316499a8489604d5b9df436ccfeee",
  "minGasPrice": "0x09184e72a000",
  "miner": "0x407d73d8a49eeb85d32cf465507dd71d507100c1",
  "nonce": "0xed0c6a53777b15880fb359dfd9368d002c959243a19c35ca1ae2e97f9bf78a53",
  "number": 3150,
  "parentHash": "0xf9de6948d835ed5257229b5103f9421a7f70c3ccc65fd31c0db85324e05702f5",
  "sha3Uncles": "0x1dcc4de8dec75d7aab85b567b6ccd41ad312451b948a7413f0a142fd40d49347",
  "stateRoot": "0xd878fcee309af964e8e70c66ec25b1e7de9eca9c4f49e90efddeea8f77a37e43",
  "timestamp": 1416585210,
  "transactionsRoot": "0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421"
} */
```

***

#####web3.eth.getTransaction

    web3.eth.getTransaction(hashStringOrNumber, blockNumber)

Returns the transaction by number or hash `hashStringOrNumber` from block with number `blockNumber`. Return value is an object with the following keys:
  * `hash`: The hash of the transaction. A 32-byte hash.
  * `input`: The binary data that formed the input to the transaction, either the input data if it was a message call or the contract initialisation if it was a contract creation. A byte array.
  * `to`: The address to which the transaction was sent. This may be the null address (address 0) if it was a contract-creation transaction (a 20-byte address).
  * `from`: The cryptographically verified address from which the transaction was sent (a 20-byte address).
  * `gas`: The amount of GAS supplied for this transaction to happen (an integer).
  * `gasPrice`: The price offered to the miner to purchase this amount of GAS, in Wei/GAS (a big int).
  * `nonce`: The transaction nonce (an integer).
  * `value`: The amount of ETH to be transferred to the recipient with the transaction in wei (a big int).
```javascript
var blockNumber = 668;
var indexOfTransaction = 0

var transaction = web3.eth.getTransaction(blockNumber, indexOfTransaction);
console.log(transaction);
/*
{
"from":"0x407d73d8a49eeb85d32cf465507dd71d507100c1",
"gas":520464,
"gasPrice":"0x09184e72a000",
"hash":"0xc6ef2fc5426d6ad6fd9e2a26abeab0aa2411b7ab17f30a99d3cb96aed1d1055b",
"input":"0x603880600c6000396000f3006001600060e060020a600035048063c6888fa114601857005b6021600435602b565b8060005260206000f35b600081600702905091905056",
"nonce":"0x",
"to":"0x0000000000000000000000000000000000000000",
"value":"0x"
}
*/

```

***

##### web3.eth.getUncle

    web3.eth.getUncle(blockHashStringOrNumber, uncleNumber)

Returns the uncle with number `uncleNumber` from the block with number or hash `blockHashStringOrNumber`.

Return value is an object with the following keys:
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
```javascript
var blockNumber = 500;
var indexOfUncle = 0

var uncle = web3.eth.getUncle(blockNumber, indexOfUncle);
console.log(uncle);
/*
{
"difficulty":"0x",
"extraData":"0x0000000000000000000000000000000000000000000000000000000000000000",
"gasLimit":0,
"hash":"0000000000000000000000000000000000000000000000000000000000000000",
"miner":"0x0000000000000000000000000000000000000000",
"nonce":"0x0000000000000000000000000000000000000000000000000000000000000000",
"number":0,
"parentHash":"0x0000000000000000000000000000000000000000000000000000000000000000",
"sha3Uncles":"0x0000000000000000000000000000000000000000000000000000000000000000",
"stateRoot":"0x0000000000000000000000000000000000000000000000000000000000000000",
"timestamp":-1,
"transactionsRoot":"0x0000000000000000000000000000000000000000000000000000000000000000"
}
*/
```

***

##### web3.eth.sendTransaction

    web3.eth.sendTransaction(transactionObject, callback)

Creates a new message-call transaction.
  * `transactionObject`, an anonymous object specifying the parameters of the transaction.
    * `from`, the address for the sending account;
    * `value (number|hexString|BigNumber)`, the value transferred for the transaction in Wei, also the endowment if it's a contract-creation transaction;
    * `endowment`, synonym for `value`;
    * `to`, the destination address of the message, left undefined for a contract-creation transaction;
    * `data`, either a [byte string](https://github.com/ethereum/wiki/wiki/Solidity,-Docs-and-ABI) containing the associated data of the message, or in the case of a contract-creation transaction, the initialisation code;
    * `code`, a synonym for `data`;
    * `gas (number|hexString|BigNumber)`, the amount of gas to purchase for the transaction (unused gas is refunded), defaults to the most gas your ether balance allows; and
    * `gasPrice (number|hexString|BigNumber)`, the price of gas for this transaction in wei, defaults to the mean network gasPrice.
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

##### web3.eth.call

    web3.eth.call(callObject)

Executes a new message-call immediately without creating a transaction on the block chain.
  * `_params`, an anonymous object specifying the parameters of the transaction, similar to that above.
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
       * `earliest`: The number of the earliest block (-1 may be given to mean the most recent, currently mining, block).
       * `latest`: The number of the latest block (-1 may be given to mean the most recent, currently mining, block).
       * `max`: The maximum number of messages to return.
       * `skip`: The number of messages to skip before the list is constructed. May be used with `max` to paginate messages into multiple calls.
       * `address`: An address or a list of addresses to restrict log entries by requiring them to be made from a particular account.
       * `topic`: A set of values which must each appear in the log entries.
   * `eventArguments` is an object with keys of one or more indexed arguments for the event(s) and values of either one (directly) or more (in an array) e.g. {'a': 1, 'b': [myFirstAddress, mySecondAddress]}.
      
If its a log filter it returns a list of log entries; each includes the following fields:

* `address`: The address of the account whose execution of the message resulted in the log entry being made.
* `topic`: The topic(s) of the log.
* `data`: The associated data of the log.
* `number`: The block number from which this log is.

If its an event filter it returns a filter object with the return values of event.

#### Filter Methods:
  * `filter.watch(callback)`: Watches for state changes that fit the filter and calls the callback.
  * `filter.stopWatching()`: Stops the watch and uninstalls the filter in the node. Should always be called once it is done.
  * `filter.get()`: Returns all of the log entries that fit the filter.


```javascript
var filter = web3.eth.filter('pending');

filter.watch(function (log) {
  console.log(log); //  {"address":"0x0000000000000000000000000000000000000000","data":"0x0000000000000000000000000000000000000000000000000000000000000000","number":0}
});
```

***

##### web3.eth.contract

    web3.eth.contract(abiArray)

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

// initiate with the contract address
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

#####web3.eth.flush

```javascript
// TODO:
```

***

##### web3.eth.getCompilers

    web3.eth.getCompilers()

Returns an array of available compilers
```javascript
var number = web3.eth.getCompilers();
console.log(number); // ["lll", "solidity", "serpent"]
```

***

##### web3.eth.compile.solidity

    web3.eth.compile.solidity(sourceString)

Compiles the solidity source code `sourceString` and returns the output data.
```javascript
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

    web3. eth.compile.lll(sourceString)

Compiles the LLL source code `sourceString` and returns the output data.
```javascript
var source = "...";

var code = web3.eth.compile.lll(source);
console.log(code); // "0x603880600c6000396000f3006001600060e060020a600035048063c6888fa114601857005b6021600435602b565b8060005260206000f35b600081600702905091905056"
```

***

##### web3.eth.compile.serpent

    web3.eth.compile.serpent(sourceString)

Compiles the serpent source code `sourceString ` and returns the output data.
```javascript
var source = "...";

var code = web3.eth.compile.serpent(source);
console.log(code); // "0x603880600c6000396000f3006001600060e060020a600035048063c6888fa114601857005b6021600435602b565b8060005260206000f35b600081600702905091905056"
```

***

#####web3.db.put
This method should be called, when we want to store number in local leveldb database.
First param is db name, second is the key, and third is the value.
```javascript
var result = web3.db.put('test', 'key', "5");
console.log(result); // true

var value = web3.db.get('test', 'key');
console.log(value); // "0x05"
```

***

#####web3.db.putString
This method should be called, when we want to store string in local leveldb database.
First param is db name, second is the key, and third is the string value.
```javascript
var result = web3.db.putString('test', 'key', "5")
console.log(result); // true

var value = web3.db.get('test', 'key');
console.log(value); // "5"
```

***

#####web3.db.get
This method should be called, when we want to get number value from local leveldb database.
First param is db name and second is the key of value.
```javascript
var result = web3.db.put('test', 'key', "5");
console.log(result); // true

var value = web3.db.get('test', 'key');
console.log(value); // "0x05"
```

***

#####web3.db.getString
This method should be called, when we want to get string value from local leveldb database.
First param is db name and second is the key of string value.
```javascript
var result = web3.db.putString('test', 'key', "5")
console.log(result); // true

var value = web3.db.get('test', 'key');
console.log(value); // "5"
```

***

#####web3.shh
[Whisper  Overview](https://github.com/ethereum/wiki/wiki/Whisper-Overview)
```javascript
var shh = web3.shh;
```

***

#####web3.shh.post
This method should be called, when we want to post whisper message. `_message` object may have following fields:
  * `from`: identity of sender
  * `to`: identity of receiver
  * `payload`: message payload
  * `ttl`: time to live
  * `workToProve`: TODO
  * `topic`: string or array of strings, with message topics
```javascript
var identity = web3.shh.newIdentity();
var topic = 'example';
var payload = 'hello whisper world!';

var message = {
  from: identity,
  topic: [web3.fromAscii(topic)],
  payload: web3.fromAscii(payload),
  ttl: 100,
  priority: 100
};

web3.shh.post(message);
```

***

#####web3.shh.newIdentity
Should be called to create new identity.
```javascript
var identity = web3.shh.newIdentity();
console.log(identity); // "0xc931d93e97ab07fe42d923478ba2465f283f440fd6cabea4dd7a2c807108f651b7135d1d6ca9007d5b68aa497e4619ac10aa3b27726e1863c1fd9b570d99bbaf"
```

***

#####web3.shh.hasIdentity
Should be called, if we want to check if user has given identity. Accepts one param. Returns true if he has, otherwise false.
```javascript
var identity = web3.shh.newIdentity();
var result = web3.shh.hasIdentity(identity);
console.log(result); // true

var result2 = web3.shh.haveIdentity(identity + "0");
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
This method should be used, when you want to watch whisper messages.
Available filter options are:
* `topic`: string or array of strings. filter messages with this topic
* `to`: filter the identity of receiver of the message

```javascript
var topic = 'example';

var options = {
  topic: [web3.fromAscii(topic)]
};

var filter = web3.shh.filter(options);

filter.watch(function(res) {
  console.log(res);
  /* {
  "expiry":1422565026,
  "from":"0x3ec052fc3376f8a218b24b652803a5892038c39419a9a44923a61b113b7785d16ed1572df628859af3504670e4df31dcd8b3ee9a2110fd710c948690f0557394",
  "hash":"0x33eb2da77bf3527e28f8bf493650b1879b08c4f2a362beae4ba2f71bafcd91f9",
  "payload":"0x7b2274797065223a226d657373616765222c2263686174223a2265537668624d763939784c345442537741222c2274696d657374616d70223a7b222464617465223a313432323536343932363533377d2c22746f706963223a22222c2266726f6d223a7b226964656e74697479223a2230783365633035326663333337366638613231386232346236353238303361353839323033386333393431396139613434393233613631623131336237373835643136656431353732646636323838353961663335303436373065346466333164636438623365653961323131306664373130633934383639306630353537333934222c226e616d65223a22416365746f7465227d2c226d657373616765223a2273617361222c226964223a227a465232614e68594a666f4d4c62707333227d",
  "sent":1422564926,
  "to":"0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
  "topic":["0x6578616d"],
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
    var balance = web3.eth.balanceAt(web3.eth.coinbase);
    document.getElementById("ether").innerText = web3.toDecimal(balance);
  });
</script>
</body></html>
```

To test it, just put it in file and save. Load it in AlethZero and point the URL to file:///WHEREVER_YOU_SAVED_IT

Job done. Now go create.

more examples can be found [here](https://github.com/ethereum/ethereum.js/tree/master/example)