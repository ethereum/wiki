### Overview

[JSON](http://json.org/) is a lightweight data-interchange format. It can represent numbers, strings, ordered sequences of values, and collections of name/value pairs.

[JSON-RPC](http://www.jsonrpc.org/specification) is a stateless, light-weight remote procedure call (RPC) protocol. Primarily this specification defines several data structures and the rules around their processing. It is transport agnostic in that the concepts can be used within the same process, over sockets, over HTTP, or in many various message passing environments. It uses JSON ([RFC 4627](http://www.ietf.org/rfc/rfc4627.txt)) as data format.

### JavaScript API

To talk to an ethereum node from inside an JavaScript application use the [ethereum.js](https://github.com/ethereum/ethereum.js) library, which gives an convenient interface for the RPC methods.
See the [JavaScript API](https://github.com/ethereum/wiki/wiki/JavaScript-API) for more.

### JSON-RPC Endpoint

Default JSON-RPC endpoints:
```
C++: http://localhost:8080
Go: http://localhost:8545
```

##### Go

You can start the HTTP JSON-RPC with the `--rpc` flag
```bash
geth --rpc
```

change the default port (8545) and listing address (localhost) with:

```bash
geth --rpc --rpcaddr <ip> --rpcport <portnumber>
```

If accessing the RPC from a browser, CORS will need to be enabled with the appropriate domain set. Otherwise, JavaScript calls are limit by the same-origin policy and requests will fail:

```bash
geth --rpc --rpccorsdomain "http://localhost:3000"
```

The JSON RPC can also be started from the [geth console](https://github.com/ethereum/go-ethereum/wiki/JavaScript-Console) using the `admin.startRPC(addr, port)` command.


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

### Output HEX values

At present there are two key datatypes that are passed over JSON: unformatted byte arrays and quantities. Both are passed with a hex encoding, however with different requirements to formatting:

When encoding **QUANTITIES** ("integers", "numbers"): encode as hex, prefix with "0x", the most compact representation (slight exception: zero should be represented as "0x0"). Examples:
- 0x41 (65 in decimal)
- 0x400 (1024 in decimal)
- WRONG: 0x (should always have at least one digit - zero is "0x0")
- WRONG: 0x0400 (no leading zeroes allowed)
- WRONG: ff (must be prefixed 0x)

When encoding **UNFORMATTED DATA** ("byte arrays", account addresses, hashes, bytecode arrays): encode as hex, prefix with "0x", two hex digits per byte. Examples:
- 0x41 (size 1, "A")
- 0x004200 (size 3, "\0B\0")
- 0x (size 0, "")
- WRONG: 0xf0f0f (must be even number of digits)
- WRONG: 004200 (must be prefixed 0x)

Currently [cpp-ethereum](https://github.com/ethereum/cpp-ethereum) and [go-ethereum](https://github.com/ethereum/go-ethereum) provides JSON-RPC communication only over http.

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
- `String "earliest"` for the earliest/genesis block
- `String "pending"` - for the pending state/transactions

### JSON-RPC methods

* [web3_clientVersion](#web3_clientversion)
* [web3_sha3](#web3_sha3)
* [net_version](#net_version)
* [net_peerCount](#net_peercount)
* [net_listening](#net_listening)
* [eth_protocolVersion](#eth_protocolversion)
* [eth_coinbase](#eth_coinbase)
* [eth_mining](#eth_mining)
* [eth_gasPrice](#eth_gasprice)
* [eth_accounts](#eth_accounts)
* [eth_blockNumber](#eth_blocknumber)
* [eth_getBalance](#eth_getbalance)
* [eth_getStorageAt](#eth_getstorageat)
* [eth_getTransactionCount](#eth_gettransactioncount)
* [eth_getBlockTransactionCountByHash](#eth_getblocktransactioncountbyhash)
* [eth_getBlockTransactionCountByNumber](#eth_getblocktransactioncountbynumber)
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
* [eth_getTransactionByBlockNumberAndIndex](#eth_gettransactionbyblocknumberandindex)
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
* [shh_version](#shh_version)
* [shh_newIdentity](#shh_newidentity)
* [shh_hasIdentity](#shh_hasidentity)
* [shh_newGroup](#shh_newgroup)
* [shh_addToGroup](#shh_addtogroup)
* [shh_newFilter](#shh_newfilter)
* [shh_uninstallFilter](#shh_uninstallfilter)
* [shh_getFilterChanges](#shh_getfilterchanges)
* [shh_getMessages](#shh_getmessages)

### API

***

#### web3_clientVersion

Returns the current client version.

##### Parameters
none

##### Returns

`String` - The current client version

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"web3_clientVersion","params":[],"id":67}'

// Result
{
  "id":67,
  "jsonrpc":"2.0",
  "result": "Mist/v0.9.3/darwin/go1.4.1"
}
```

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

#### net_version

Returns the current network protocol version.

##### Parameters
none

##### Returns

`String` - The current network protocol version

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"net_version","params":[],"id":67}'

// Result
{
  "id":67,
  "jsonrpc": "2.0",
  "result": "59"
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

#### eth_protocolVersion

Returns the current ethereum protocol version.

##### Parameters
none

##### Returns

`String` - The current ethereum protocol version

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_protocolVersion","params":[],"id":67}'

// Result
{
  "id":67,
  "jsonrpc": "2.0",
  "result": "54"
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
2. `HEX String|String` - integer block number, or the string `"latest"`, `"earliest"` or `"pending"`, see the [default block parameter](#the-default-block-parameter)

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
3. `HEX String|String` - integer block number, or the string `"latest"`, `"earliest"` or `"pending"`, see the [default block parameter](#the-default-block-parameter)


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
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getStorageAt","params":["0x407d73d8a49eeb85d32cf465507dd71d507100c1", "0x0", "0x2"],"id":1}'

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
2. `HEX String|String` - integer block number, or the string `"latest"`, `"earliest"` or `"pending"`, see the [default block parameter](#the-default-block-parameter)

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

1. `HEX String` - integer of a block number, or the string "latest", "earliest" or "pending", see the [default block parameter](#the-default-block-parameter)

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

1. `HEX String` - integer of a block number, or the string "latest", "earliest" or "pending", see the [default block parameter](#the-default-block-parameter)

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
2. `HEX String|String` - integer block number, or the string `"latest"`, `"earliest"` or `"pending"`, see the [default block parameter](#the-default-block-parameter)

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
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getCode","params":["0xd5677cf67b5aa051bb40496e68ad359eb97cfbf8", "0x2"],"id":1}'

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
  - `to`: `HEX String`  - (optional when creating new contract) The address the transaction is directed to.
  - `gas`: `HEX String`  - (optional, default: To-Be-Determined) Integer of the gas provided for the transaction execution. It will return unused gas.
  - `gasPrice`: `HEX String`  - (optional, default: To-Be-Determined) Integer of the gasPrice used for each payed gas
  - `value`: `HEX String`  - (optional) Integer of the value send with this transaction
  - `data`: `HEX String`  - (optional) The compiled code of a contract

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
  - `gas`: `HEX String`  - (optional) Integer of the gas provided for the transaction execution. It will return unused gas.
  - `gasPrice`: `HEX String`  - (optional) Integer of the gasPrice used for each payed gas
  - `value`: `HEX String`  - (optional) Integer of the value send with this transaction
  - `data`: `HEX String`  - (optional) The compiled code of a contract
2. `HEX String|String` - integer block number, or the string `"latest"`, `"earliest"` or `"pending"`, see the [default block parameter](#the-default-block-parameter)

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

(?)


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

`Object` - A block object, or `null` when no transaction was found:

  - `number`: `HEX String` - integer of the block number.
  - `hash`: `HEX String` - 32-byte hash of the block.
  - `parentHash`: `HEX String` - 32-byte hash of the paretn block.
  - `nonce`: `HEX String` - 8-byte hash of the generated proof-of-work.
  - `sha3Uncles`: `HEX String` - SHA3 of all uncle (?).
  - `logsBloom`: `HEX String` - bloom (?).
  - `transactionsRoot`: `HEX String` - (?).
  - `stateRoot`: `HEX String` - (?).
  - `miner`: `HEX String` - the address of the miner, who minded this block.
  - `difficulty`: `HEX String` - integer of the difficulty for this block.
  - `totalDifficulty`: `HEX String` - integer of the total difficulty up until this block (?).
  - `extraData`: `HEX String` - byte array of extra data add to this block (?),
  - `size`: `HEX String` - integer the size of this block in bytes.
  - `gasLimit`: `HEX String` - integer of the maximum gas allowed in this block (?).
  - `minGasPrice`: `HEX String` - the minimal (?).
  - `gasUsed`: `HEX String` - the total used gas by all transactions in this block (?).
  - `timestamp`: `HEX String` - the unix timestamp when the block was mined (?).
  - `transactions`: `Array` - Array of transaction objects, or transaction hashes depending on the last parameter.
  - `uncles`: `Array` - Array of uncle hashes.


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
    "difficulty": "0x027f07", // 163591
    "totalDifficulty":  "0x027f07", // 163591
    "extraData": "0x0000000000000000000000000000000000000000000000000000000000000000",
    "size":  "0x027f07", // 163591
    "gasLimit": "0x9f759", // 653145
    "minGasPrice": "0x9f759", // 653145
    "gasUsed": "0x9f759", // 653145
    "timestamp": "0x54e34e8e" // 1424182926
    "transactions": [{...},{ ... }] 
    "uncles": ["0x1606e5...", "0xd5145a9..."]
  }
}
```

***

#### eth_getBlockByNumber

Returns information about a block by block number.

##### Parameters

1. `HEX String` - integer of a block number.
2. `Boolean` - If `true` it returns the full transaction objects, if `false` only the hashes of the transactions.

```js
params: [
   '0x1b4', // 436
   true
]
```

##### Returns

See [eth_getBlockByHash](#eth_getblockbyhash)

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getBlockByNumber","params":["0x1b4", true],"id":1}'
```

Result see [eth_getBlockByHash](#eth_getblockbyhash)

***

#### eth_getTransactionByHash

Returns the information about a transaction requested by transaction hash.


##### Parameters

1. `HEX String` - hash of a transaction

```js
params: [
   '0xe670ec64341771606e55d6b4ca35a1a6b75ee3d5145a99d05921026d1527331'
]
```

##### Returns

`Object` - A transaction object, or `null` when no transaction was found:

  - `hash`: `HEX String` - 32-byte hash of the transaction.
  - `nonce`: `HEX String` - integer of the transaction nonce (?).
  - `blockHash`: `HEX String` - 32-byte hash of the block where this transaction was in. `null` when the transaction is pending.
  - `blockNumber`: `HEX String` - integer of the block number where this transaction was in. `null` when the transaction is pending.
  - `transactionIndex`: `HEX String` - integer of the transactions index position in the block.
  - `from`: `HEX String` - 20-byte address of the sender.
  - `to`: `HEX String` - 20-byte address of the receiver. `null` when its a contract creation transaction.
  - `value`: `HEX String` - integer of the value transfered in wei.
  - `gasPrice`: `HEX String` - integer of the price payed per gas in wei.
  - `gas`: `HEX String` - integer of the gas used.
  - `input`: `HEX String` - the data send along with the transaction.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getTransactionByHash","params":["0xc6ef2fc5426d6ad6fd9e2a26abeab0aa2411b7ab17f30a99d3cb96aed1d1055b"],"id":1}'

// Result
{
"id":1,
"jsonrpc":"2.0",
"result": {
    "hash":"0xc6ef2fc5426d6ad6fd9e2a26abeab0aa2411b7ab17f30a99d3cb96aed1d1055b",
    "nonce":"0x",
    "blockHash": "0xbeab0aa2411b7ab17f30a99d3cb9c6ef2fc5426d6ad6fd9e2a26a6aed1d1055b",
    "blockNumber": "0x15df", // 5599
    "transactionIndex":  "0x1", // 1
    "from":"0x407d73d8a49eeb85d32cf465507dd71d507100c1",
    "to":"0x85h43d8a49eeb85d32cf465507dd71d507100c1",
    "value":"0x7f110" // 520464
    "gas": "0x7f110" // 520464
    "gasPrice":"0x09184e72a000",
    "input":"0x603880600c6000396000f300603880600c6000396000f3603880600c6000396000f360",
  }
}
```

***

#### eth_getTransactionByBlockHashAndIndex

Returns information about a transaction by block hash and transaction index position.


##### Parameters

1. `HEX String` - hash of a block.
2. `HEX String` - integer of the transaction index position.

```js
params: [
   '0xe670ec64341771606e55d6b4ca35a1a6b75ee3d5145a99d05921026d1527331',
   '0x0' // 0
]
```

##### Returns

See [eth_getBlockByHash](#eth_gettransactionbyhash)

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getTransactionByBlockHashAndIndex","params":[0xc6ef2fc5426d6ad6fd9e2a26abeab0aa2411b7ab17f30a99d3cb96aed1d1055b, "0x0"],"id":1}'
```

Result see [eth_getTransactionByHash](#eth_gettransactionbyhash)

***

#### eth_getTransactionByBlockNumberAndIndex

Returns information about a transaction by block number and transaction index position.


##### Parameters

1. `HEX String` - integer of a block number.
2. `HEX String` - integer of the transaction index position.

```js
params: [
   '0x29c', // 668
   '0x0' // 0
]
```

##### Returns

See [eth_getBlockByHash](#eth_gettransactionbyhash)

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getTransactionByBlockNumberAndIndex","params":["0x29c", "0x0"],"id":1}'
```

Result see [eth_getTransactionByHash](#eth_gettransactionbyhash)

***

#### eth_getUncleByBlockHashAndIndex

Returns information about a uncle of a block by hash and uncle index position.


##### Parameters


1. `HEX String` - hash a block.
2. `HEX String` - integer of the uncle index position.

```js
params: [
   '0xc6ef2fc5426d6ad6fd9e2a26abeab0aa2411b7ab17f30a99d3cb96aed1d1055b',
   '0x0' // 0
]
```

##### Returns

See [eth_getBlockByHash](#eth_getblockbyhash)

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getUncleByBlockHashAndIndex","params":["0xc6ef2fc5426d6ad6fd9e2a26abeab0aa2411b7ab17f30a99d3cb96aed1d1055b", "0x0"],"id":1}'
```

Result see [eth_getBlockByHash](#eth_getblockbyhash)

**Note**: An uncle doesn't contain individual transactions.

***

#### eth_getUncleByBlockNumberAndIndex

Returns information about a uncle of a block by number and uncle index position.


##### Parameters

1. `HEX String` - integer a block number.
2. `HEX String` - integer of the uncle index position.

```js
params: [
   '0x29c', // 668
   '0x0' // 0
]
```

##### Returns

See [eth_getBlockByHash](#eth_getblockbyhash)

**Note**: An uncle doesn't contain individual transactions.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getUncleByBlockNumberAndIndex","params":["0x29c", "0x0"],"id":1}'
```

Result see [eth_getBlockByHash](#eth_getblockbyhash)

***

#### eth_getCompilers

Returns a list of available compilers in the client.

##### Parameters
none

##### Returns

`Array` - Array of available compilers.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getCompilers","params":[],"id":1}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": ["solidity", "lll", "serpent"]
}
```

***

#### eth_compileSolidity

Returns compiled solidity code.

##### Parameters

1. `String` - The source code.

```js
params: [
   "contract test { function multiply(uint a) returns(uint d) {   return a * 7;   } }",
]
```

##### Returns

`HEX String` - The compiled source code.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_compileSolidity","params":["contract test { function multiply(uint a) returns(uint d) {   return a * 7;   } }"],"id":1}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0x603880600c6000396000f3006001600060e060020a600035048063c6888fa114601857005b6021600435602b565b8060005260206000f35b600081600702905091905056" // the compiled source code
}
```

***

#### eth_compileLLL

Returns compiled LLL code.

##### Parameters

1. `String` - The source code.

```js
params: [
   "(?)",
]
```

##### Returns

`HEX String` - The compiled source code.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_compileSolidity","params":["(?)"],"id":1}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0x603880600c6000396000f3006001600060e060020a600035048063c6888fa114601857005b6021600435602b565b8060005260206000f35b600081600702905091905056" // the compiled source code
}
```

***

#### eth_compileSerpent

Returns compiled serpent code.

##### Parameters

1. `String` - The source code.

```js
params: [
   "(?)",
]
```

##### Returns

`HEX String` - The compiled source code.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_compileSolidity","params":["(?)"],"id":1}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0x603880600c6000396000f3006001600060e060020a600035048063c6888fa114601857005b6021600435602b565b8060005260206000f35b600081600702905091905056" // the compiled source code
}
```

***

#### eth_newFilter

Creates a filter object, based on filter options, to notify when the state changes (logs).
To check if the state has changed, call [eth_getFilterChanges](#eth_getfilterchanges).

##### Parameters

1. `Object` - The filter options:
  - `fromBlock`: `HEX String|String` - (optional, default: `"latest"`) Integer block number, or `"latest"` for the last mined block or `"pending"`, `"earliest"` for not yet mined transactions.
  - `toBlock`: `HEX String|String` - (optional, default: `"latest"`) Integer block number, or `"latest"` for the last mined block or `"pending"`, `"earliest"` for not yet mined transactions.
  - `address`: `HEX String|Array` - (optional) Contract address or a list of addresses from which logs should originate.
  - `topics`: `Array` - (optional) Array of `HEX Strings` topics.

```js
params: [{
  "fromBlock": "0x1",
  "toBlock": "0x2",
  "address": "0x01231f12a01231f12a01ff231f12a0123101231f12a",
  "topics": ['0x1234fa1234']
}]
```

##### Returns

`HEX String` - The integer of a filter id.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_newFilter","params":[{"topics":["0x12341234"]}],"id":73}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0x1" // 1
}
```

***

#### eth_newBlockFilter

Creates a filter object, based on an option string, to notify when state changes (logs).
To check if the state has changed, call [eth_getFilterChanges](#eth_getfilterchanges).


##### Parameters

1. `String` - The string `"latest"` for notifications about new block and `"pending"` for notifications about pending transactions.

```js
params: ["pending"]
```

##### Returns

`HEX String` - The integer of a filter id.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_newBlockFilter","params":["pending"],"id":73}'

// Result
{
  "id":1,
  "jsonrpc":  "2.0",
  "result": "0x1" // 1
}
```

***

#### eth_uninstallFilter

Uninstalls a filter with given id. Should always be called when watch is no longer needed.
Additonally Filters timeout when they aren't requested with [eth_getFilterChanges](#eth_getfilterchanges) for a period of time.


##### Parameters

1. `HEX String` - The filter id.

```js
params: [
  "0xb" // 11
]
```

##### Returns

`Boolean` - `true` if the filter was successfully uninstalled, otherwise `false`.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_uninstallFilter","params":["0xb"],"id":73}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": true
}
```

***

#### eth_getFilterChanges

Polling method for a filter, which returns an array of logs which occurred since last poll.


##### Parameters

1. `HEX String` - Integer of the filter id.

```js
params: [
  "0x16" // 22
]
```

##### Returns

`Array` - Array of log objects, or an empty array (if nothing has changed since last poll).

For filters created with `eth_newBlockFilter` log objects are `null`.

For filters created with `eth_newFilter` logs are objects with following params:

  - `hash`: `HEX String` - 32-byte hash of the log.
  - `logIndex`: `HEX String` - integer of the log index position in the block.
  - `transactionIndex`: `HEX String` - integer of the transactions index position log was created from.
  - `transactionHash`: `HEX String` - hash of the transactions this log was created from.
  - `blockHash`: `HEX String` - 32-byte hash of the block where this log was in. `null` when the log is pending.
  - `blockNumber`: `HEX String` - integer of the block number where this log was in. `null` when the log is pending.
  - `address`: `HEX String` - address from which this log originated.
  - `data`: `HEX String` - the data from this log.
  - `topics`: `Array` - Array of `HEX Strings` topics.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getFilterChanges","params":["0x16"],"id":73}'

// Result
{
  "id":1,
  "jsonrpc":"2.0",
  "result": [{
    "hash": "0x5785ac562ff41e2dcfdf8216c5785ac562ff41e2dcfdf829c5a142f1fccd7d",
    "logIndex": "0x1", // 1
    "blockNumber":"0x1b4" // 436
    "blockHash": "0x8216c5785ac562ff41e2dcfdf5785ac562ff41e2dcfdf829c5a142f1fccd7d",
    "transactionHash":  "0xdf829c5a142f1fccd7d8216c5785ac562ff41e2dcfdf5785ac562ff41e2dcf",
    "transactionIndex": "0x0", // 0
    "address": "0x16c5785ac562ff41e2dcfdf829c5a142f1fccd7d",
    "data":"0x0000000000000000000000000000000000000000000000000000000000000000",
    "topics": ["0x59ebeb90bc63057b6515673c3ecf9438e5058bca0f92585014eced636878c9a5"]
    },{
      ...
    }]
}
```

***

#### eth_getFilterLogs

Returns an array of all logs matching filter with given id.


##### Parameters

1. `HEX String` - The filter id.

```js
params: [
  "0x16" // 22
]
```

##### Returns

See [eth_getFilterChanges](#eth_getfilterchanges)

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getFilterLogs","params":["0x16"],"id":74}'
```

Result see [eth_getFilterChanges](#eth_getfilterchanges)

***

#### eth_getLogs

Returns an array of all logs matching a given filter object.

##### Parameters

1. `Object` - the filter object, see [eth_newFilter parameters](#eth_newfilter).

```js
params: [{
  "topics": ["0x12341234"]
}]
```

##### Returns

See [eth_getFilterChanges](#eth_getfilterchanges)

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getLogs","params":[{"topics":["0x12341234"]}],"id":74}'
```

Result see [eth_getFilterChanges](#eth_getfilterchanges)

***

#### eth_getWork

Returns the hash of the current block, the seedHash, and the difficulty to be met.

##### Parameters
none

##### Returns

`Array` - Arrway with the following properties:
  1. `HEX String` - current block header hash without the nonce (the first part of the proof-of-work pair) (?).
  2. `HEX String` - the seed hash used for the DAG.
  3. `HEX String` - integer of the difficulty required to solve.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getWork","params":[],"id":73}'

// Result
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

***

#### eth_submitWork

Used for submitting a proof-of-work solution.


##### Parameters

1. `HEX String` - The nonce found (64 bits)
2. `HEX String` - The header (256 bits)
3. `HEX String` - The mix digest (256 bits)

```js
params: [
  "0x0000000000000001",
  "0x1234567890abcdef1234567890abcdef",
  "0xD1GE5700000000000000000000000000"
]
```

##### Returns

`Boolean` - returns `true` if the provided solution is valid, otherwise `false`.


##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0", "method":"eth_submitWork", "params":["0x0000000000000001", "0x1234567890abcdef1234567890abcdef", "0xD1GE5700000000000000000000000000"]],"id":73}'

// Result
{
  "id":1,
  "jsonrpc":"2.0",
  "result": true
}
```

***

#### db_putString

Stores a string in the local database.

##### Parameters

1. `String` - Database name.
2. `String` - Key name.
3. `String` - String to store.

```js
params: [
  "testDB",
  "myKey",
  "myString"
]
```

##### Returns

`Boolean` - returns `true` if the value was stored, otherwise `false`.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"db_putString","params":["testDB","myKey","myString"],"id":73}'

// Result
{
  "id":1,
  "jsonrpc":"2.0",
  "result": true
}
```

***

#### db_getString

Returns string from the local database.

##### Parameters

1. `String` - Database name.
2. `String` - Key name.

```js
params: [
  "testDB",
  "myKey",
]
```

##### Returns

`String` - The previously stored string.


##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"db_getString","params":["testDB","myKey"],"id":73}'

// Result
{
  "id":1,
  "jsonrpc":"2.0",
  "result": "myString"
}
```

***

#### db_putHex

Stores binary data in the local database.


##### Parameters

1. `String` - Database name.
2. `String` - Key name.
3. `HEX String` - HEX string to store.

```js
params: [
  "testDB",
  "myKey",
  "0x68656c6c6f20776f726c64"
]
```

##### Returns

`Boolean` - returns `true` if the value was stored, otherwise `false`.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"db_putHex","params":["testDB","myKey","0x68656c6c6f20776f726c64"],"id":73}'

// Result
{
  "id":1,
  "jsonrpc":"2.0",
  "result": true
}
```

***

#### db_getHex

Returns binary data from the local database.


##### Parameters

1. `String` - Database name.
2. `String` - Key name.

```js
params: [
  "testDB",
  "myKey",
]
```

##### Returns

`HEX String` - The previously stored HEX string.


##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"db_getHex","params":["testDB","myKey"],"id":73}'

// Result
{
  "id":1,
  "jsonrpc":"2.0",
  "result": "0x68656c6c6f20776f726c64"
}
```

***

#### shh_version

Returns the current whisper protocol version.

##### Parameters
none

##### Returns

`String` - The current whisper protocol version

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"shh_version","params":[],"id":67}'

// Result
{
  "id":67,
  "jsonrpc": "2.0",
  "result": "2"
}
```

***

#### shh_post

Sends a whisper message.

##### Parameters

1. `Object` - The whisper post object:
  - `from`: `HEX String` - (optional) The identity of the sender.
  - `to`: `HEX String` - (optional) The identity of the receiver. When present whisper will encrypt the message so that only the receiver can decrypt it.
  - `topics`: `Array` - Array of `HEX String` topics, so that receiver can identify messages.
  - `payload`: `HEX String` - The payload of the message.
  - `priority`: `HEX String` - The integer of the priority in a rang from ... (?).
  - `ttl`: `HEX String` - integer of the time to live in seconds.

```js
params: [{
  from: "0x0487bc1d6bf3aa84d8a2c24865f83cd7bcfd11ccd8cb18d9dead364e...",
  to: "0x742d636c69656e776869737065bb722d63686174...",
  topics: ["0x776869737065722d636861742d636c69656e74", "0x4d5a695276454c39425154466b61693532"],
  payload: "0x7b2274797065223a226d6...",
  priority: "0x64",
  ttl: "0x64",
}]
```

##### Returns

`Boolean` - returns `true` if the message was send, otherwise `false`.


##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"db_getString","params":[{"from":"0xc931d93e97ab07fe42d923478ba2465f2..","topics": ["0x68656c6c6f20776f726c64"],"payload":"0x68656c6c6f20776f726c64","ttl":0x64,"priority":0x64}],"id":73}'

// Result
{
  "id":1,
  "jsonrpc":"2.0",
  "result": true
}
```

***

#### shh_newIdentinty

Creates new whisper identity in the client.

##### Parameters
none

##### Returns

`HEX String` - the address of the new identiy.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"shh_newIdentinty","params":[],"id":73}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0xc931d93e97ab07fe42d923478ba2465f283f440fd6cabea4dd7a2c807108f651b7135d1d6ca9007d5b68aa497e4619ac10aa3b27726e1863c1fd9b570d99bbaf"
}
```

***

#### shh_hasIdentity

Checks if the client hold the private keys for a given identity.


##### Parameters

1. `HEX String` - The identity address to check.

```js
params: [
  "0xc931d93e97ab07fe42d9234..."
]
```

##### Returns

`Boolean` - returns `true` if the client holds the privatekey for that identity, otherwise `false`.


##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"shh_hasIdentity","params":["0xc931d93e97ab07fe42d923478ba2465f283..."],"id":73}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": true
}
```

***

#### shh_newGroup

(?)

##### Parameters
none

##### Returns

`HEX String` - the address of the new group.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"shh_newIdentinty","params":[],"id":73}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": "0xc65f283f440fd6cabea4dd7a2c807108f651b7135d1d6ca90931d93e97ab07fe42d923478ba2407d5b68aa497e4619ac10aa3b27726e1863c1fd9b570d99bbaf"
}
```

***

#### shh_addToGroup

(?)

##### Parameters

1. `HEX String` - The identity address to add to a group (?).

```js
params: [
  "0xc931d93e97ab07fe42d9234..."
]
```

##### Returns

`Boolean` - returns `true` if the identity was successfully added to the group, otherwise `false` (?).

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"shh_hasIdentity","params":["0xc931d93e97ab07fe42d923478ba2465f283..."],"id":73}'

// Result
{
  "id":1,
  "jsonrpc": "2.0",
  "result": true
}
```

***

#### shh_newFilter

Creates filter to notify, when client receives whisper message matching the filter options.


##### Parameters

1. `Object` - The filter options:
  - `to`: `HEX String` - (optional) Identity of the receiver. When present it will try to decrypt any incoming message if the client holds the private key to this identity.
  - `topics`: `Array` - Array of `HEX String` topics which the message has to have. 

```js
params: [{
   "topics": ['0x12341234bf4b564f'],
   "to": "0x2341234bf4b2341234bf4b564f..."
}]
```

##### Returns

`HEX String` - Integer of the newly created filter.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"shh_newFilter","params":[{"topics": ['0x12341234bf4b564f'],"to": "0x2341234bf4b2341234bf4b564f..."}],"id":73}'

// Result
{
  "id":1,
  "jsonrpc":"2.0",
  "result": "0x7" // 7
}
```

***

#### shh_uninstallFilter

Uninstalls a filter with given id. Should always be called when watch is no longer needed.
Additonally Filters timeout when they aren't requested with [shh_getFilterChanges](#shh_getfilterchanges) for a period of time.


##### Parameters

1. `HEX String` - The filter id.

```js
params: [
  "0x7" // 7
]
```

##### Returns

`Boolean` - `true` if the filter was successfully uninstalled, otherwise `false`.

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"shh_uninstallFilter","params":["0x7"],"id":73}'

// Result
{
  "id":1,
  "jsonrpc":"2.0",
  "result": true
}
```

***

#### shh_getFilterChanges

Polling method for whisper filters.


##### Parameters

`. `HEX String` - The filter id.

```js
params: [
  "0x7" // 7
]
```

##### Returns

`Array` - Array of messages received since last poll:

  - `hash`: `HEX String` - The hash of the message.
  - `from`: `HEX String` - The sender of the message, if a sender was specified.
  - `to`: `HEX String` - The receiver of the message, if a receiver was specified.
  - `expiry`: `HEX String` - Integer of the time in seconds when this message should expire (?).
  - `ttl`: `HEX String` -  Integer of the time the message should float in the system in seconds (?).
  - `sent`: `HEX String` -  Integer of the unix timestamp when the message was sent.
  - `topics`: `Array` - Array of `HEX String` topics the message contained.
  - `payload`: `HEX String` - The payload of the message.
  - `workProved`: `HEX String` - Integer of the work this message required before it was send (?).

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"shh_getFilterChanges","params":["0x7"],"id":73}'

// Result
{
  "id":1,
  "jsonrpc":"2.0",
  "result": [{
    "hash": "0x33eb2da77bf3527e28f8bf493650b1879b08c4f2a362beae4ba2f71bafcd91f9",
    "from": "0x3ec052fc33..",
    "to": "0x87gdf76g8d7fgdfg...",
    "expiry": "0x54caa50a", // 1422566666
    "sent": "0x54ca9ea2", // 1422565026
    "ttl": "0x64" // 100
    "topics": ["0x6578616d"],
    "payload": "0x7b2274797065223a226d657373616765222c2263686...",
    "workProved": "0x0"
    }]
}
```

***

#### shh_getMessages

Get all messages matching a filter, which are still existing in the node.

##### Parameters

1. `HEX String` - The filter id.

```js
params: [
  "0x7" // 7
]
```

##### Returns

See [shh_getFilterChanges](#shh_getfilterchanges)

##### Example
```js
// Request
curl -X POST --data '{"jsonrpc":"2.0","method":"shh_getMessages","params":["0x7"],"id":73}'
```

Result see [shh_getFilterChanges](#shh_getfilterchanges)