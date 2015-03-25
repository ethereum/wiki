### Overview

[JSON](http://json.org/) is a lightweight data-interchange format. It can represent numbers, strings, ordered sequences of values, and collections of name/value pairs.

[JSON-RPC](http://www.jsonrpc.org/specification) is a stateless, light-weight remote procedure call (RPC) protocol. Primarily this specification defines several data structures and the rules around their processing. It is transport agnostic in that the concepts can be used within the same process, over sockets, over http, or in many various message passing environments. It uses JSON ([RFC 4627](http://www.ietf.org/rfc/rfc4627.txt)) as data format.

Currently [cpp-ethereum](https://github.com/ethereum/cpp-ethereum) and [go-ethereum](https://github.com/ethereum/go-ethereum) provides JSON-RPC communication only over http.

In order to free ethereum implementation developers from the hassle of writing their own JavaScript implementation for browser-to-node communication there's now [ethereum.js](https://github.com/ethereum/ethereum.js). Ethereum.js provides a full communication bridge between your node (backend) and browser (frontend) given that you **tell** it how _speak_. Communication is done through a `Provider`. Ethereum.js documentation is [here](https://github.com/ethereum/wiki/wiki/JavaScript-API).

### JSON-RPC Endpoint

Default json-rpc endpoints:
```
C++: http://localhost:8080
Go: http://localhost:8545
```

##### Go

You can start the HTTP JSON-RPC with the `--rpc` flag
```bash
ethereum --rpc
```

change the default port (8545) and listing address (localhost) with:

```bash
ethereum --rpc --rpcaddr <ip> --rpcport <portno>
```

The RPC can also be started from the CLI using the `admin.startRPC(addr, port)` command.


##### C++

You can start it by running `eth` application with `-j` option:
```bash
./eth -j
```

You can also specify JSON-RPC port (default is 8080):
```bash
./eth -j --json-rpc-port 8079
```

### JSON-RPC support

| | cpp-ethereum | go-ethereum |
|-------|:------------:|:-----------:|
| JSON-RPC 1.0 | &#x2713; | |
| JSON-RPC 2.0 | &#x2713; | &#x2713; |
| Batch requests | &#x2713; |  &#x2713; | 
| HTTP | &#x2713; | &#x2713; |


### JSON-RPC methods

The following RPC messages should be accepted by the RPC-backend:

* [web3_sha3](#web3_sha3)
* [web3_clientVersion](#web3_clientversion)
* [net_version](#net_version)
* [net_peerCount](#net_peercount)
* [net_listening](#net_listening)
* [eth_coinbase](#eth_coinbase)
* [eth_mining](#eth_mining)
* [eth_gasPrice](#eth_gasprice)
* [eth_accounts](#eth_accounts)
* [eth_blockNumber](#eth_blocknumber)
* [eth_getBalance](#eth_getbalance)
* [eth_getStorageAt](#eth_getstorageat)
* [eth_getTransactionCount](#eth_gettransactioncount)
* [eth_getBlockTransactionCountByHash](#eth_getblocktransactioncountbyhash)
* [eth_getBlockTransactionCountByNumber](#eth_getBlockTransactioncountbynumber)
* [eth_getUncleCountByBlockHash](#eth_getunclecountbyblockhash)
* [eth_getUncleCountByBlockNumber](#eth_getunclecountbyblocknumber)
* [eth_getCode](#eth_getcode)
* [eth_sendTransaction](#eth_sendtransaction)
* [eth_call](#eth_call)
* [eth_flush](#eth_flush)
* [eth_getBlockByHash](#eth_getblockbyhash)
* [eth_getBlockByNumber](#eth_getblockbynumber)
* [eth_getTransactionByHash](#eth_gettransactionbyhash)
* [eth_getTransactionByBlockHashAndIndex](#eth_gettransactionbyblockhashandindex)
* [eth_getTransactionByBlockNumberAndIndex](#eth_gettransactionbynumberandindex)
* [eth_getUncleByBlockHashAndIndex](#eth_getunclebyblockhashandindex)
* [eth_getUncleByBlockNumberAndIndex](#eth_getunclebyblocknumberandindex)
* [eth_getCompilers](#eth_getcompilers)
* [eth_compileLLL](#eth_compileLLL)
* [eth_compileSolidity](#eth_compileSolidity)
* [eth_compileSerpent](#eth_compileSerpent)
* [eth_newFilter](#eth_newfilter)
* [eth_newBlockFilter](#eth_newblockfilter)
* [eth_uninstallFilter](#eth_uninstallfilter)
* [eth_getFilterChanges](#eth_getfilterchanges)
* [eth_getFilterLogs](#eth_getfilterlogs)
* [eth_getLogs](#eth_getlogs)
* [eth_getWork](#eth_getwork)
* [eth_submitWork](#eth_submitwork)
* [db_putString](#db_putstring)
* [db_getString](#db_getstring)
* [db_putHex](#db_puthex)
* [db_getHex](#db_gethex) 
* [shh_post](#shh_post)
* [shh_newIdentity](#shh_newidentity)
* [shh_hasIdentity](#shh_hasidentity)
* [shh_newGroup](#shh_newgroup)
* [shh_addToGroup](#shh_addtogroup)
* [shh_newFilter](#shh_newfilter)
* [shh_uninstallFilter](#shh_uninstallfilter)
* [shh_getFilterChanges](#shh_getfilterchanges)
* [shh_getMessages](#shh_getmessages)


### The default block parameter

The following methods have a extra default block parameter:

- [eth_getBalance](#eth_getbalance)
- [eth_getCode](#eth_getcode)
- [eth_getTransactionCount](#eth_gettransactioncount)
- [eth_getStorageAt](#eth_getstorageat)
- [eth_call](#eth_call)

When requests are made that act on the state of ethereum, the last default block parameter determines the height of the block.

The following options are possible for the defaultBlock parameter:

- `HEX String` - an integer block number
- `String "latest"` - for the latest minded block
- `String "pending"` - for the pending state/transactions

### API

***

#### web3_sha3

Returns SHA3 of the given data.

##### Parameters

1. `String` - the data to convert into a SHA3 hash

```js
params: [
  '0x68656c6c6f20776f726c64'
]
```

##### Returns

`String` - The SHA3 result of the given string.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"web3_sha3","params":["0x68656c6c6f20776f726c64"],"id":64}'

// Result
{
  "id":64,
  "jsonrpc": "2.0",
  "result": "0x47173285a8d7341e5e972fc677286384f802f8ef42a5ec5f03bbfa254cb01fad"
}
```

***

#### web3_clientVersion

Returns the current client version.

##### Parameters
none

##### Returns

`String` - The current clients version

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"web3_clientVersion","params":[],"id":67}'

// Result
{
  "id":67,
  "jsonrpc":"2.0",
  "result": "AlethZero/0.0.1"
}
```

***

#### net_version

Returns the current network protocol version.

##### Parameters
none

##### Returns

`String` - The current network version

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"net_version","params":[],"id":67}'

// Result
{
  "id":67,
  "jsonrpc":"2.0",
  "result": "0.0.1"
}
```

***

#### net_listening

Returns `true` if client is actively listening for network connections.

##### Parameters
none

##### Returns

`Boolean` - `true` when listening, otherwise `false`.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"net_listening","params":[],"id":67}'

// Result
{
  "id":67,
  "jsonrpc":"2.0",
  "result":true
}
```

***

#### net_peerCount

Returns number of peers currenly connected to the client.

##### Parameters
none

##### Returns

`HEX String` - integer of the number of connected peers.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"net_peerCount","params":[],"id":74}'

// Result
{
  "id":74,
  "jsonrpc": "2.0",
  "result": "0x2" // 2
}
```

***

#### eth_coinbase

Returns the client coinbase address.


##### Parameters
none

##### Returns

`HEX String` - the current coinbase address.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_coinbase","params":[],"id":64}'

// Result
{
  "id":64,
  "jsonrpc": "2.0",
  "result": "0x407d73d8a49eeb85d32cf465507dd71d507100c1"
}
```

***

#### eth_mining

Returns `true` if client is actively mining new blocks.

##### Parameters
none

##### Returns

`Boolean` - returns `true` of the client is mining, otherwise `false`.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_mining","params":[],"id":71}'

// Result
{
  "id":71,
  "jsonrpc": "2.0",
  "result": true
}

```

***

#### eth_gasPrice

Returns the current price per gas in wei.

##### Parameters
none

##### Returns

`HEX String` - integer of the current gas price in wei.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_gasPrice","params":[],"id":73}'

// Result
{
  "id":73,
  "jsonrpc": "2.0",
  "result": "0x09184e72a000" // 10000000000000
}
```

***

#### eth_accounts

Returns a list of addresses owned by client.


##### Parameters
none

##### Returns

`Array of HEX Strings` - addresses owned by the client.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_accounts","params":[],"id":1}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": ["0x407d73d8a49eeb85d32cf465507dd71d507100c1"]
}
```

***

#### eth_blockNumber

Returns the number of most recent block.

##### Parameters
none

##### Returns

`HEX String` - integer of the current block number the client is on.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_blockNumber","params":[],"id":83}'

// Result
{
  "id":83,
  "jsonrpc": "2.0",
  "result": "0x4b7" // 1207
}
```

***

#### eth_getBalance

Returns the balance of the account of given address.

##### Parameters

1. `HEX String` - address to check for balance.
2. `HEX String|String` - integer block number, or the string `"latest"` or `"pending"`, see the [default block parameter](#the-default-block-parameter)

```js
params: [
   '0x407d73d8a49eeb85d32cf465507dd71d507100c1',
   'latest'
]
```

##### Returns

`HEX String` - integer of the current balance in wei.


##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getBalance","params":["0x407d73d8a49eeb85d32cf465507dd71d507100c1", "latest"],"id":1}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0x0234c8a3397aab58" // 158972490234375000
}
```

***

#### eth_getStorageAt

Returns the value from a storage position at a given address.


##### Parameters

1. `HEX String` - address of the storage.
2. `HEX String` - integer of the position in the storage.
3. `HEX String|String` - integer block number, or the string `"latest"` or `"pending"`, see the [default block parameter](#the-default-block-parameter)


```js
params: [
   '0x407d73d8a49eeb85d32cf465507dd71d507100c1',
   '0x0', // storage position at 0
   '0x2' // state at block number 2
]
```

##### Returns

`HEX String` - the value at this storage position.


##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getStorageAt","params":["0x407d73d8a49eeb85d32cf465507dd71d507100c1", "0x0", "latest"],"id":1}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0x03"
}
```

***

#### eth_getTransactionCount

Returns the number of transactions *send* from a address.


##### Parameters

1. `HEX String` - address.
2. `HEX String|String` - integer block number, or the string `"latest"` or `"pending"`, see the [default block parameter](#the-default-block-parameter)

```js
params: [
   '0x407d73d8a49eeb85d32cf465507dd71d507100c1',
   'latest' // state at the latest block
]
```

##### Returns

`HEX String` - integer of the number of transactions send from this address.


##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getTransactionCount","params":["0x407d73d8a49eeb85d32cf465507dd71d507100c1","latest"],"id":1}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0x1" // 1
}
```

***

#### eth_getBlockTransactionCountByHash

Returns the number of transactions in a block from a block matching the given block hash.


##### Parameters

1. `HEX String` - hash of a block

```js
params: [
   '0x407d73d8a49eeb85d32cf465507dd71d507100c1'
]
```

##### Returns

`HEX String` - integer of the number of transactions in this block.


##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getBlockTransactionCountByHash","params":["0x407d73d8a49eeb85d32cf465507dd71d507100c1"],"id":1}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0xb" // 11
}
```

***

#### eth_getBlockTransactionCountByNumber

Returns the number of transactions in a block from a block matching the given block number.


##### Parameters

1. `HEX String` - integer of a block number

```js
params: [
   '0xe8', // 232
]
```

##### Returns

`HEX String` - integer of the number of transactions in this block.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getBlockTransactionCountByNumber","params":["0xe8"],"id":1}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0xa" // 10
}
```

***

#### eth_getUncleCountByBlockHash

Returns the number of uncles in a block from a block matching the given block hash.


##### Parameters

1. `HEX String` - hash of a block

```js
params: [
   '0x407d73d8a49eeb85d32cf465507dd71d507100c1'
]
```

##### Returns

`HEX String` - integer of the number of uncles in this block.


##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getUncleCountByBlockHash","params":["0x407d73d8a49eeb85d32cf465507dd71d507100c1"],"id"Block:1}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0x1" // 1
}
```

***

#### eth_getUncleCountByBlockNumber

Returns the number of uncles in a block from a block matching the given block number.


##### Parameters

1. `HEX String` - integer of a block number

```js
params: [
   '0xe8', // 232
]
```

##### Returns

`HEX String` - integer of the number of uncles in this block.


##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getUncleCountByBlockNumber","params":["0xe8"],"id":1}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0x1" // 1
}
```

***

#### eth_getCode

Returns code at a given address.


##### Parameters

1. address as hex string
2. `HEX String|String` - integer block number, or the string `"latest"` or `"pending"`, see the [default block parameter](#the-default-block-parameter)

```js
params: [
   '0xd5677cf67b5aa051bb40496e68ad359eb97cfbf8',
   '0x2'  // 2
]
```

##### Returns

`HEX String` - the code from the given address.


##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getCode","params":["0xd5677cf67b5aa051bb40496e68ad359eb97cfbf8", "latest"],"id":1}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0x600160008035811a818181146012578301005b601b6001356025565b8060005260206000f25b600060078202905091905056"
}
```

***

#### eth_sendTransaction

Creates new message call transaction or a contract creation, if the data field contains code.

##### Parameters

1. `Object` - The transaction object
  - `from`: `HEX String` - The address the transaction is send from.
  - `to`: `HEX String`  - The address the transaction is directed to.
  - `gas`: `HEX String`  - Integer of the gas provided for the transaction execution. It will return unused gas.
  - `gasPrice`: `HEX String`  - Integer of the gasPrice used for each payed gas
  - `value`: `HEX String`  - Integer of the value send with this transaction
  - `data`: `HEX String`  - The compiled code of a contract

```js
params: [{
  "from": "0xb60e8dd61c5d32be8058bb8eb970870f07233155",
  "to": "0xd46e8dd67c5d32be8058bb8eb970870f072445675",
  "gas": "0x76c0", // 30400,
  "gasPrice": "0x9184e72a000", // 10000000000000
  "value": "0x9184e72a", // 2441406250
  "data": "0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675"
}]
```

##### Returns

`HEX String` - the address of the newly created contract, or the transaction hash.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_sendTransaction","params":[{see above}],"id":1}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0x7f9fade1c0d57a7af66ab4ead7c2eb7b11a91385"
}
```

***

#### eth_call

Executes a new message call immediately without creating a transaction on the block chain.


##### Parameters

1. `Object` - The transaction call object
  - `from`: `HEX String` - The address the transaction is send from.
  - `to`: `HEX String`  - The address the transaction is directed to.
  - `gas`: `HEX String`  - Integer of the gas provided for the transaction execution. It will return unused gas.
  - `gasPrice`: `HEX String`  - Integer of the gasPrice used for each payed gas
  - `value`: `HEX String`  - Integer of the value send with this transaction
  - `data`: `HEX String`  - The compiled code of a contract
2. `HEX String|String` - integer block number, or the string `"latest"` or `"pending"`, see the [default block parameter](#the-default-block-parameter)

See: [eth_sendTransaction Parameters](#eth_sendtransaction)

##### Returns

`HEX String` - the return value of executed contract.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_call","params":[{see above}],"id":1}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0x0"
}
```

***

#### eth_flush

[not described yet]


##### Parameters
none

##### Returns
none

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_flush","params":[],"id":64}'

// Result
{
"id":1,
"jsonrpc":"2.0",
"result":true
}
```

***

#### eth_getBlockByHash

Returns information about a block by hash.


##### Parameters

1. `HEX String` - Hash of a block.
2. `Boolean` - If `true` it returns the full transaction objects, if `false` only the hashes of the transactions.

```js
params: [
   '0xe670ec64341771606e55d6b4ca35a1a6b75ee3d5145a99d05921026d1527331',
   true
]
```

##### Returns

`Object` - A block object.


##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getBlockByHash","params":["0xe670ec64341771606e55d6b4ca35a1a6b75ee3d5145a99d05921026d1527331", true],"id":1}'

// Result
{
"id":1,
"jsonrpc":"2.0",
"result": {
  "number": "0x1b4", // 436
  "hash": "0xe670ec64341771606e55d6b4ca35a1a6b75ee3d5145a99d05921026d1527331",
  "parentHash": "0x9646252be9520f6e71339a8df9c55e4d7619deeb018d2a3f2d21fc165dde5eb5",
  "nonce": "0xe04d296d2460cfb8472af2c5fd05b5a214109c25688d3704aed5484f9a7792f2",
  "sha3Uncles": "0x1dcc4de8dec75d7aab85b567b6ccd41ad312451b948a7413f0a142fd40d49347",
  "logsBloom": "0xe670ec64341771606e55d6b4ca35a1a6b75ee3d5145a99d05921026d1527331",
  "transactionsRoot": "0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421",
  "stateRoot": "0xd5855eb08b3387c0af375e9cdb6acfc05eb8f519e419b874b6ff2ffda7ed1dff",
  "miner": "0x4e65fda2159562a496f9f3522f89122a3088497a",
  "difficulty": "0x027f07", // 163591 big int?
  "totalDifficulty":  "0x027f07", // 163591 big int
  "size":  "0x027f07", // 163591 big int? of bytes
  "extraData": "0x0000000000000000000000000000000000000000000000000000000000000000",
  "gasLimit": "0x9f759", // 653145 big int?
  "minGasPrice": "0x9f759", // 653145 big int
  "gasUsed": "0x9f759", // 653145 big int?
  "timestamp": "0x54e34e8e" // 1424182926
  "transactions": [{
      // see eth_transaction objects
   },{ ... }] // or array of hashes, if the last given parameter if is FALSE
   "uncles": ["0x1606e5...", "0xd5145a9..."]
  }
}
```

***

#### eth_getBlockByNumber
*returns block info for block with given number.*

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getBlockByNumber","params":["0x1b4", true],"id":1}'
```

##### Parameters

0. number of a block
1. include transaction objects (If FALSE it only includes the hashes in an array)

```js
params: [
   '0x1b4', // 436
   true
]
```

##### Response Example
See [eth_getBlockByHash](#eth_getblockbyhash)

***

#### eth_getTransactionByHash
*returns transaction with hash*

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getTransactionByHash","params":["0xc6ef2fc5426d6ad6fd9e2a26abeab0aa2411b7ab17f30a99d3cb96aed1d1055b"],"id":1}'
```

##### Parameters

0. hash of a transaction

```js
params: [
   '0xe670ec64341771606e55d6b4ca35a1a6b75ee3d5145a99d05921026d1527331'
]
```

##### Response Example
```js
{
"id":1,
"jsonrpc":"2.0",
"result": {
    "status": "mined", // or "pending"
    "hash":"0xc6ef2fc5426d6ad6fd9e2a26abeab0aa2411b7ab17f30a99d3cb96aed1d1055b",
    "nonce":"0x",
    "blockHash": "0x6fd9e2a26ab...", // or null if pending
    "blockNumber": "0x15df", // 5599 // or null if pending
    "transactionIndex":  "0x1", // 1
    "from":"0x407d73d8a49eeb85d32cf465507dd71d507100c1",
    "to":"0x85h43d8a49eeb85d32cf465507dd71d507100c1",
    "value":"0x7f110" // 520464 big int
    "gas": "0x7f110" // 520464
    "gasPrice":"0x09184e72a000",
    "input":"0x603880600c6000396000f30060...",
  }
}
```

***

#### eth_getTransactionByBlockHashAndIndex
*returns transaction with number[0] from the block with hash["0xc6ef2fc5426d6ad6fd9e2a26abeab0aa2411b7ab17f30a99d3cb96aed1d1055b"]*

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getTransactionByBlockHashAndIndex","params":[0xc6ef2fc5426d6ad6fd9e2a26abeab0aa2411b7ab17f30a99d3cb96aed1d1055b, "0x0"],"id":1}'
```

##### Parameters

0. hash of a block
1. index of the transaction

```js
params: [
   '0xe670ec64341771606e55d6b4ca35a1a6b75ee3d5145a99d05921026d1527331',
   '0x0' // 0
]
```

##### Response Example
See [eth_getTransactionByHash](#eth_gettransactionbyhash)

***

#### eth_getTransactionByBlockNumberAndIndex
*returns transaction from the block with number[668] at positon number[0]*

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getTransactionByBlockNumberAndIndex","params":["0x29c", "0x0"],"id":1}'
```

##### Parameters

0. number of the block
1. index number of the transaction

```js
params: [
   '0x29c', // 668
   '0x0' // 0
]
```

##### Response Example
See [eth_getTransactionByHash](#eth_gettransactionbyhash)

***

#### eth_getUncleByBlockHashAndIndex
*returns uncle from the block with hash  "0xc6ef2fc5426d6ad6fd9e2a..." at index positon 0*

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getUncleByBlockHashAndIndex","params":["0xc6ef2fc5426d6ad6fd9e2a26abeab0aa2411b7ab17f30a99d3cb96aed1d1055b", "0x0"],"id":1}'
```

##### Parameters

0. hash of the block
1. index number of the uncle
2. include transaction objects (If FALSE it only includes the hashes in an array)

```js
params: [
   '0xc6ef2fc5426d6ad6fd9e2a26abeab0aa2411b7ab17f30a99d3cb96aed1d1055b',
   '0x0', // 0
   true
]
```

##### Response Example
See [eth_getBlockByHash](#eth_getblockbyhash)

***

#### eth_getUncleByBlockNumberAndIndex
*returns uncle from the block with number 668 at index position 0*

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getUncleByBlockNumberAndIndex","params":["0x29c", "0x0"],"id":1}'
```

##### Parameters

0. number of the block
1. index number of the uncle
2. include transaction objects (If FALSE it only includes the hashes in an array)

```js
params: [
   '0x29c', // 668
   '0x0', // 0
   true
]
```

##### Response Example
See [eth_getBlockByHash](#eth_getblockbyhash)

***

#### eth_getCompilers
*returns list of available compilers for the client*

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getCompilers","params":[],"id":1}'
```

##### Parameters
none

##### Response Example
```json
{
"id":1,
"jsonrpc":"2.0",
"result": ["lll", "solidity", "serpent"]
}
```

***

#### eth_compileSolidity
*returns compiled solidity code*

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_compileSolidity","params":["contract test { function multiply(uint a) returns(uint d) {   return a * 7;   } }"],"id":1}'
```

##### Parameters

0. the source code as string

```js
params: [
   "contract test { function multiply(uint a) returns(uint d) {   return a * 7;   } }",
]
```

##### Response Example
```js
{
  "id":1,
  "jsonrpc":"2.0",
  "result":"0x603880600c6000396000f3006001600060e060020a600035048063c6888fa114601857005b6021600435602b565b8060005260206000f35b600081600702905091905056" // the compiled source code
}
```

***

#### eth_compileLLL
*returns compiled lll code*

##### Example
```js
// Request
// TODO
```
##### Response Example
```json
// TODO
```

***

#### eth_compileSerpent
*returns compiled serpent code*

##### Example
```js
// Request
// TODO
```
##### Response Example
```json
// TODO
```

***

#### eth_newFilter
*creates watch object to notify, when state changes in particular way, defined by given filter object. Returns new filter id.*

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_newFilter","params":[{"topics":["0x12341234"]}],"id":73}'
```

##### Parameters

0. the filter object

```js
params: [{
  "fromBlock": "0x1", (optional) // integer block number, "latest" for the last mined block or "pending" for transactions not yet mined.
  "toBlock": "0x2", (optional) // integer block number, "latest" or "pending".
  "address": "0x01231f12a..", (optional) // to filter from account
  "topics": ['0x1234fa1234...'] (optional) // array of topic strings
}]
```

##### Response Example
```js
"id":1,
"jsonrpc":"2.0",
"result": "0x1" // 1
```

***

#### eth_newBlockFilter
*creates watch object to notify, when state changes in particular way, defined by a filter string. Returns new watch id. To check if the state has changed, call [eth_getFilterChanges](#eth_getfilterchanges)*

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_newBlockFilter","params":["pending"],"id":73}'
```

##### Parameters

0. the string "pending" or "latest" for changes in the latest block

```js
params: ["pending"]
```

##### Response Example
```js
{
"id":1,
"jsonrpc":"2.0",
"result": "0x1" // 1
}
```

***

#### eth_uninstallFilter
*uninstalls a filter with given id. Should always be called when watch is no longer needed.*

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_uninstallFilter","params":["0x1"],"id":73}'
```

##### Parameters

0. the filter id

```js
params: ["0x1"] // 1
```

##### Response Example
```json
{
"id":1,
"jsonrpc":"2.0",
"result": true
}
```

***

#### eth_getFilterChanges
*polling method for a filter, which returns an array of logs which occurred since last poll*

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getFilterChanges","params":["0x16"],"id":73}'
```

##### Parameters

0. the filter id

```js
params: ["0x16"] // 22
```

##### Response Example
```js
{
"id":1,
"jsonrpc":"2.0",
"result": [{
  "status": "mined", // or "pending"
  "hash": "0x5785ac562ff41e2dcfdf8216c5785ac562ff41e2dcfdf829c5a142f1fccd7d",
  "address": "0x16c5785ac562ff41e2dcfdf829c5a142f1fccd7d",
  "data":"0x0000000000000000000000000000000000000000000000000000000000000000",
  "blockNumber":"0x1b4" // 436 // or null, if pending
  "blockHash": "0x62ff4df829c5..." // or null, if pending
  "transactionHash":  "0x1e2dcfdf821..."
  "transactionIndex": "0x0" // 0
  "logIndex": "0x1" // 1
  }]
}
```

***

#### eth_getFilterLogs
*returns an array of all logs matching filter with given id*

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getFilterLogs","params":["0x16"],"id":74}'
```

##### Parameters

0. the filter id

```js
params: ["0x16"] // 22
```

##### Response Example
See [eth_getFilterChanges](#eth_getfilterchanges)

***

#### eth_getLogs
*returns an array of all logs matching filter object*

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getLogs","params":[{"topics":["0x12341234"]}],"id":74}'
```

##### Parameters

0. the filter object

```js
params: [{"topics": ["0x12341234"] }]
```

##### Response Example
See [eth_getFilterChanges](#eth_getfilterchanges)

***

#### eth_getWork
*returns the hash of current block, the seedHash, and the difficulty to be met.*

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getWork","params":[],"id":73}'
```

##### Parameters
none

##### Response Example
```json
{
"id":1,
"jsonrpc":"2.0",
"result": [
    "0x1234567890abcdef1234567890abcdef",
    "0x5EED0000000000000000000000000000",
    "0xd1ff1c01710000000000000000000000"
]
}
```

The hash first of the result value is the header hash without the nonce (the first part of the proof-of-work pair)

The second is the seed hash used for the DAG.

The third is the difficulty required to solve.

***

#### eth_submitWork
*Used for submitting a solution to the proof-of-work. The return value is true if the submission is valid.*

##### Example
```js
// Request
curl -X POST --data '{
  "jsonrpc":"2.0",
  "method":"eth_submitWork",
  "params":["0x0000000000000001",
            "0x1234567890abcdef1234567890abcdef",
            "0xD1GE5700000000000000000000000000"]],"id":73}'
```

##### Parameters

0. The nonce found (64 bits)
1. The header (256 bits)
2. The mix digest (256 bits)

```js
params: ["0x1234567890abcdef1234567890abcdef"]
```

##### Response Example
```json
{
"id":1,
"jsonrpc":"2.0",
"result": true
}
```

***

#### db_putString
*stores a string in the local database.*

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"db_putString","params":["testDB","myKey","myString"],"id":73}'
```

##### Parameters

0. database name
1. key name
2. string to store

```js
params: [
  "testDB",
  "myKey",
  "myString"
]
```

##### Response Example
```json
{
"id":1,
"jsonrpc":"2.0",
"result": true
}
```

***

#### db_getString
*returns string from the local database.*

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"db_getString","params":["testDB","myKey"],"id":73}'
```

##### Parameters

0. database name
1. key name

```js
params: [
  "testDB",
  "myKey",
]
```

##### Response Example
```js
{
"id":1,
"jsonrpc":"2.0",
"result": "myString"
}
```

***

#### db_putHex
*stores binary data in the local database.*

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"db_putHex","params":["testDB","myKey","0x68656c6c6f20776f726c64"],"id":73}'
```

##### Parameters

0. database name
1. key name
2. hex string to store

```js
params: [
  "testDB",
  "myKey",
  "0x68656c6c6f20776f726c64"
]
```

##### Response Example
```json
{
"id":1,
"jsonrpc":"2.0",
"result": true
}
```

***

#### db_getHex
*returns binary data from the local database.*

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"db_getHex","params":["testDB","myKey"],"id":73}'
```

##### Parameters

0. database name
1. key name

```js
params: [
  "testDB",
  "myKey",
]
```

##### Response Example
```js
{
"id":1,
"jsonrpc":"2.0",
"result": "0x68656c6c6f20776f726c64"
}
```

***

#### shh_post
*sends whisper message*

##### Example
```js
// Request
"0x68656c6c6f20776f726c64"
curl -X POST --data '{"jsonrpc":"2.0","method":"db_getString","params":[{"from":"0xc931d93e97ab07fe42d923478ba2465f2..","topics": ["0x68656c6c6f20776f726c64"],"payload":"0x68656c6c6f20776f726c64","ttl":0x64,"priority":0x64}],"id":73}'
```
##### Parameters

0. the shh post object

```js
params: [{
  from: "0x0424406a...",
  topics: ["0x776869737065722d636861742d636c69656e74", "0x4d5a695276454c39425154466b61693532"],
  payload: "0x7b2274797065223a226d6...",
  priority: "0x64", // TODO or workToProve?
  ttl: "0x64",
}]
```

##### Response Example
```json
{
"id":1,
"jsonrpc":"2.0",
"result": true
}
```

***

#### shh_newIdentinty
*creates new whisper identity*

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"shh_newIdentinty","params":[],"id":73}'
```

##### Parameters
none

##### Response Example
```json
{
"id":1,
"jsonrpc":"2.0",
"result":"0xc931d93e97ab07fe42d923478ba2465f283f440fd6cabea4dd7a2c807108f651b7135d1d6ca9007d5b68aa497e4619ac10aa3b27726e1863c1fd9b570d99bbaf"
}
```

***

#### shh_hasIdentity
*returns true if client has given identity*

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"shh_hasIdentity","params":["0xc931d93e97ab07fe42d923478ba2465f283..."],"id":73}'
```

##### Parameters

0. the identity to check

```js
params: ["0xc931d93e97ab07fe42d9234..."]
```

##### Response Example
```json
{
"id":1,
"jsonrpc":"2.0",
"result": true
}
```

***

#### shh_newGroup

##### Example
```js
// Request
// TODO: not implemented yet
```
##### Response Example
```json
// TODO: not implemented yet
```

***

#### shh_addToGroup

##### Example
```js
// Request
// TODO: not implemented yet
```
##### Response Example
```json
// TODO: not implemented yet
```

***

#### shh_newFilter
*creates filter object to notify, when client receives whisper message matching particular format, defined by filter. Returns new filter id.*

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"shh_newFilter","params":[{"topics": ["0x68656c6c6f20776f726c64"], "to": "0x34kh345k34kjh5k34"}],"id":73}'
```

##### Parameters

0. the filter object

```js
params: [{
   "topics": ['0x12341234...'],
   "to": "0x34kh345k34kjh5k34"
}]
```

##### Response Example
```js
{
"id":1,
"jsonrpc":"2.0",
"result": "0x7" // 7
}
```

***

#### shh_uninstallFilter
*uninstalls watch with given id. Should always be called when watch is no longer needed.*

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"shh_uninstallFilter","params":["0x7"],"id":73}'
```

##### Parameters

0. the filter id

```js
params: ["0x7"] // 7
```

##### Response Example
```json
{
"id":1,
"jsonrpc":"2.0",
"result": true
}
```

***

#### shh_getFilterChanges
*polling method, which returns array of messages which are received since last poll*

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"shh_getFilterChanges","params":["0x7"],"id":73}'
```

##### Parameters

0. the filter id

```js
params: ["0x7"] // 7
```

##### Response Example
```js
{
"id":1,
"jsonrpc":"2.0",
"result": [{
  "hash":"0x33eb2da77bf3527e28f8bf493650b1879b08c4f2a362beae4ba2f71bafcd91f9",
  "from":"0x3ec052fc33..",
  "to":"0x87gdf76g8d7fgdfg...",
  "expiry": "0x54caa50a", // 1422566666
  "sent": "0x54ca9ea2", // 1422565026
  "ttl": "0x64" // 100
  "topics":["0x6578616d"],
  "payload":"0x7b2274797065223a226d657373616765222c2263686..",
  "workProved":0 // TODO or priority?
  }]
}
```

***

#### shh_getMessages
*returns array of whisper messages received by filter with given id.*

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"shh_getMessages","params":["0x7"],"id":73}'
```

##### Parameters

0. the filter id

```js
params: ["0x7"] // 7
```

##### Response Example
See [shh_getFilterChanges](#shh_getfilterchanges)