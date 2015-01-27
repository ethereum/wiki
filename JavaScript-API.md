# Introduction

To make your ÐApp work with on Ethereum, you'll need to know about the Ethereum Javascript bindings, or, if you like, magic Javascript objects. These bindings may be used with AlethZero, Mist and external browser. These bindings can be found at [ethereum.js](https://github.com/ethereum/ethereum.js) repository.

There is, at the global scope, one objects; the `web3` object, containing data handling functions (commonly used for all other APIs). This object also contains other subprotocol objects including the `eth` object - `web3.eth` (for specifically Ethereum interaction) and the `shh` object - `web3.shh` (for Whisper interaction). Over time we'll introduce other objects for each of the other web3 protocols.

# API

* [web3](#web3)
  * [sha3](#web3sha3) *(_s1)*
  * [toAscii](#web3toascii) *(_s)*
  * [fromAscii](#web3fromascii) *(_s, [_padding])*
  * [toDecimal](#web3todecimal) *(_s)*
  * [fromDecimal](#web3fromdecimal) *(_s)*
  * [toFixed](#web3tofixed) *(_s)*
  * [fromFixed](#web3fromfixed) *(_s)*
  * [offset](#web3offset) *(_s, _o)*
  * [eth](#web3eth)
    * [coinbase](#web3ethcoinbase)
    * [listening](#web3ethlistening)
    * [mining](#web3ethmining)
    * [gasPrice](#web3ethgasprice)
    * [accounts](#web3ethaccounts)
    * [peerCount](#web3ethpeercount)
    * [defaultBlock](#web3ethdefaultblock)
    * [number](#web3ethnumber)
    * [balanceAt](#web3ethbalanceat) *(_address)*
    * [stateAt](#web3ethstateat) *(_address, _storage)*
    * [storageAt](#web3ethstorageat) *(_address)*
    * [countAt](#web3ethcountat) *(_address)*
    * [codeAt](#web3ethcodeat) *(_address)*
    * [transact](#web3ethtransact) *(_object)*
    * [block](#web3ethblock) *(_hash/_number)*
    * [transaction](#web3ethtransaction) *(_object, _number)*
    * [uncle](#web3ethuncle) *(_hash/number)*
    * [compilers](#web3ethcompilers) *()*
    * [lll](#web3ethlll) *(_string)*
    * [solidity](#web3ethsolidity) *(_object)*
    * [serpent](#web3ethserpent) *(_object)*
    * [logs](#web3ethlogs) *(_object/_string)*
    * [watch](#web3ethwatch) *(_object/_string)*
      * [arrived](#) *(_callback)*
      * [changed](#) *(_callback)*
      * [logs](#) *(_callback)*
      * [uninstall](#) *(_callback)*
    * [contract](#web3ethcontract) *(_address, _abi)*
  * [db](#web3db)
    * [put](#web3dbput) *(_name, _key, _value)*
    * [putString](#web3dbputstring) *(__name, _key, _value)*
    * [get](#web3dbget) *(_name, _key)*
    * [getString](#web3dbgetstring) *(_name, _key)*
  * [shh](#shh)
    * [post](#web3shhpost) *(_object)*
    * [newIdentity](#web3shhnewIdentity) *()*
    * [haveIdentity](#web3shhhaveIdentity) *(_string)*
    * [newGroup](#web3shhnewGroup) *(_id, _who)*
    * [addToGroup](#addToGroup) *(_group, _who)*
    * [watch](#shh_watch) *(_object/_string)*
      * [arrived](#) *(_callback)*
      * [changed](#) *(_callback)*
      * [messages](#) *(_callback)*
      * [uninstall](#) *(_callback)*

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

##### web3.sha3
Returns the SHA3 of the given data.
```javascript
var str = web3.fromAscii("Some ASCII string to be hashed");
console.log(str); // "0x536f6d6520415343494920737472696e6720746f20626520686173686564"

var hash = web3.sha3(str);
console.log(hash); // "0xb21dbc7a5eb6042d91f8f584af266f1a512ac89520f43562c6c1e37eab6eb0c4"
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
console.log(str); // "0x657468657265756d000000000000000000000000000000000000000000000000"

var str2 = web3.fromAscii('ethereum', 32);
console.log(str2); // "0x657468657265756d000000000000000000000000000000000000000000000000"
```

##### web3.toDecimal
Returns the decimal string representing the data `_s` (when interpreted as a big-endian integer).
```javascript
var value = web3.toDecimal('0x15');
console.log(value === "21"); // true
```

##### web3.fromDecimal
Returns the hex data string representing (in big-endian format) the decimal integer `_s`.
```javascript
var value = web3.fromDecimal('21');
console.log(value === "0x15"); // true
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

##### web3.eth
```javascript
var eth = web3.eth;
```

#####web3.eth.coinbase
Returns the coinbase address of the client.
```javascript
var coinbase = web3.eth.coinbase;
console.log(coinbase); // "0x407d73d8a49eeb85d32cf465507dd71d507100c1"
```

#####web3.eth.listening
Returns true if and only if the client is actively listening for network connections.
```javascript
var listening = web3.eth.listening;
console.log(listening); // true of false
```

#####web3.eth.mining
Returns true if and only if the client is actively mining new blocks.
```javascript
var mining = web3.eth.mining;
console.log(mining); // true or false
```

#####web3.eth.gasPrice
Returns the special 256-bit number equal to the hard-coded testnet price of gas.
```javascript
var gasPrice = web3.eth.gasPrice;
console.log(gasPrice); // "0x09184e72a000"
```

#####web3.eth.accounts
Returns the special key-pair list object corresponding to the address of each of the accounts owned by the client that this ÐApp has access to.
```javascript
var accounts = web3.eth.accounts;
console.log(accounts); // ["0x407d73d8a49eeb85d32cf465507dd71d507100c1"] 
```

#####web3.eth.peerCount
Returns the number of peers currently connected to the client.
```javascript
var peerCount = web3.eth.peerCount;
console.log(peerCount); // 4
```

#####web3.eth.defaultBlock
The default block number/age to use when querying state. When positive this is a block number, when 0 or negative it is a block age. -1 therefore means the most recently mined block, 0 means the block being currently mined (i.e. to include pending transactions). Defaults to -1.
```javascript
var defaultBlock = web3.eth.defaultBlock;
console.log(defaultBlock); // -1
```

#####web3.eth.number
Returns the number of the most recent block.
```javascript
var number = web3.eth.number;
console.log(number); // 2744
```

#####web3.eth.balanceAt
Returns the balance of the account of address given by the address `_a`
```javascript
var balance = web3.eth.balanceAt("0x407d73d8a49eeb85d32cf465507dd71d507100c1");
console.log(balance); // "0x32884442997a37a000"
```

#####web3.eth.stateAt
Returns the value in storage at position given by the string `_s` of the account of address given by the address `_a`.
```javascript
var state = web3.eth.stateAt("0x407d73d8a49eeb85d32cf465507dd71d507100c1", 0);
console.log(state); // "0x03"
```

#####web3.eth.storageAt
Dumps storage as json object.
```javascript
var storage = web3.eth.storageAt("0x407d73d8a49eeb85d32cf465507dd71d507100c1");
console.log(storage); // { "0x" : "0x03" }
```

#####web3.eth.countAt
Returns the number of transactions send from the account of address given by `_a`.
```javascript
var number = web3.eth.countAt("0x407d73d8a49eeb85d32cf465507dd71d507100c1");
console.log(number); // 1
```

#####web3.eth.codeAt
Returns true if the account of address given by `_a` is a contract-account.
```javascript
var code = web3.eth.codeAt("0xd5677cf67b5aa051bb40496e68ad359eb97cfbf8");
console.log(code); // "0x600160008035811a818181146012578301005b601b6001356025565b8060005260206000f25b600060078202905091905056"
```

#####web3.eth.block
Returns the block with number `_number`. Return value is an object with the following keys:
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
#####web3.eth.transaction
Returns the transaction number `_i` from block with number `_number`. Return value is an object with the following keys:
  * `hash`: The hash of the transaction. A 32-byte hash.
  * `input`: The binary data that formed the input to the transaction, either the input data if it was a message call or the contract initialisation if it was a contract creation. A byte array.
  * `to`: The address to which the transaction was sent. This may be the null address (address 0) if it was a contract-creation transaction (a 20-byte address).
  * `from`: The cryptographically verified address from which the transaction was sent (a 20-byte address).
  * `gas`: The amount of GAS supplied for this transaction to happen (an integer).
  * `gasPrice`: The price offered to the miner to purchase this amount of GAS, in Wei/GAS (a big int).
  * `nonce`: The transaction nonce (an integer).
  * `value`: The amount of ETH to be transferred to the recipient with the transaction (a big int).
```javascript
// TODO (_number, _i), (_hash, _i)
```

#####web3.eth.uncle
Returns the uncle number `_i` from block with number `_number`. Return value is an object with the following keys:
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
// TODO (_number, _i), (_hash, _i)
```

#####web3.eth.transact
Creates a new message-call transaction.
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
```javascript
// TODO
```

#####web3.eth.call
Executes a new message-call immediately without creating a transaction on the block chain.
  * `_params`, an anonymous object specifying the parameters of the transaction, similar to that above.
  * `_fn`, the callback function, called on completion of the message call. A single argument is passed equal to the output data of the message call.
```javascript
// TODO
```

#####web3.eth.logs
Past messages may be filtered and their attributes inspected, and future messages (and the changes they implicitly bring) may be notified of. Returns the list of log entries in Ethereum matching the given `_filter`. The filter is an object including fields:
  * `earliest`: The number of the earliest block (-1 may be given to mean the most recent, currently mining, block).
  * `latest`: The number of the latest block (-1 may be given to mean the most recent, currently mining, block).
  * `max`: The maximum number of messages to return.
  * `skip`: The number of messages to skip before the list is constructed. May be used with `max` to paginate messages into multiple calls.
  * `address`: An address or a list of addresses to restrict log entries by requiring them to be made from a particular account.
  * `topic`: A set of values which must each appear in the log entries.
  * Returns a list of log entries; each includes the following fields:
    * `address`: The address of the account whose execution of the message resulted in the log entry being made.
    * `topics`: The topic(s) of the log.
    * `data`: The associated data of the log.
    * `number`: The block number from which this log is.

#####web3.eth.watch
`web3.eth.watch(_filter)`: Creates a watch object to notify when the state changes in a particular way, given by `_filter`. Filter may be a log filter object, as defined above. It may also be either `'chain'` or `'pending'` to watch for changes in the chain or pending transactions respectively. Returns a watch object with the following methods:
  * `changed(_f)`: Installs a handler, `_f`, which is called when the state changes due to logs that fit `_filter`. These (new) log entries are passed as a parameter in a format equivalent to the return value of `web3.eth.logs`.
  * `logs()`: Returns all of the log entries that fit `_filter`.
  * `uninstall()`: Uninstalls the watch. Should always be called once it is done with.
```javascript
// TODO (_filter)
```

#####web3.eth.contract
This method should be called when we want to call / transact some solidity method from javascript. It returns an object which has the same methods available as solidity contract description.

usage example:
```javascript
var abi = [{
     name: 'myMethod',
     inputs: [{ name: 'a', type: 'string' }],
     outputs: [{name: 'd', type: 'string' }]
}];  // contract abi

var myContract = web3.eth.contract('0x0123123121', abi); // creation of contract object

myContract.myMethod('this is test string param for call'); // myMethod call (implicit, default)
myContract.call().myMethod('this is test string param for call'); // myMethod call (explicit)
myContract.transact().myMethod('this is test string param for transact'); // myMethod transact
```

#####web3.eth.compilers
Returns an array of available compilers
```javascript
var number = web3.eth.compilers();
console.log(number); // ["lll", "solidity", "serpent"]
```

#####web3.eth.solidity
Compiles the solidity source code `_s` and returns the output data.
```javascript
// TODO (_code)
var number = web3.eth.solidity("");
```

#####web3.eth.lll
Compiles the LLL source code `_s` and returns the output data.
```javascript
// TODO (_code)
```

#####web3.eth.serpent
Compiles the serpent source code `_s` and returns the output data.
```javascript
// TODO (_code)
```

# Example

A simple HTML snippet that will display the user's primary account balance of Ether:
```html
<html><body>
<div>You have <span id="ether">?</span> Weis</div>
<script>
web3.eth.watch({address: web3.eth.coinbase}).changed(function() {
    var balance = web3.eth.balanceAt(web3.eth.coinbase);
    document.getElementById("ether").innerText = web3.toDecimal(balance);
</script>
</body></html>
```

To test it, just put it in file and save. Load it in AlethZero and point the URL to file:///WHEREVER_YOU_SAVED_IT

Job done. Now go create.

more examples can be found [here](https://github.com/ethereum/ethereum.js/tree/master/example)

### Recent Changes
- Moved "Misc" into web3.* object.
- Removed all secret keys from the JS API.
- Altered naming to web3.
- Added web3.db and web3.ssh objects
- Added usage examples to JavaScript-Api wiki page.

### Upcoming Changes
- Proscribe particular bigint objects for numerical manipulation.
- Integrate Paperscript-style preprocessing to allow for operator overloading.
- Add more examples to ethereum.js