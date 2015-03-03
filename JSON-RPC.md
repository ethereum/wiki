### Overview

[JSON](http://json.org/) is a lightweight data-interchange format. It can represent numbers, strings, ordered sequences of values, and collections of name/value pairs.

[JSON-RPC](http://www.jsonrpc.org/specification) is a stateless, light-weight remote procedure call (RPC) protocol. Primarily this specification defines several data structures and the rules around their processing. It is transport agnostic in that the concepts can be used within the same process, over sockets, over http, or in many various message passing environments. It uses JSON ([RFC 4627](http://www.ietf.org/rfc/rfc4627.txt)) as data format.

Currently [cpp-ethereum](https://github.com/ethereum/cpp-ethereum) and [go-ethereum](https://github.com/ethereum/go-ethereum) provides json-rpc communication only over http.

In order to free ethereum implementation developers from the hassle of writing their own JavaScript implementation for browser-to-node communication there's now [ethereum.js](https://github.com/ethereum/ethereum.js). Ethereum.js provides a full communication bridge between your node (backend) and browser (frontend) given that you **tell** it how _speak_. Communication is done through a `Provider`. Ethereum.js documentation is [here](https://github.com/ethereum/wiki/wiki/JavaScript-API).

### JSON-RPC Endpoint

Default json-rpc endpoint for cpp-ethereum is:
```
http://localhost:8080/
```

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
| Batch requests | &#x2713; | | 
| HTTP | &#x2713; | &#x2713; |


### JSON-RPC methods

The following RPC messages should be accepted by the RPC-backend:

* [web3_sha3](#web3_sha3)
* [eth_coinbase](#eth_coinbase)
* [eth_setCoinbase](#eth_setcoinbase)
* [eth_listening](#eth_listening)
* [eth_setListening](#eth_setlistening)
* [eth_mining](#eth_mining)
* [eth_setMining](#eth_setmining)
* [eth_gasPrice](#eth_gasprice)
* [eth_accounts](#eth_accounts)
* [eth_peerCount](#eth_peercount)
* [eth_defaultBlock](#eth_defaultblock)
* [eth_setDefaultBlock](#eth_setdefaultblock)
* [eth_number](#eth_number)
* [eth_balanceAt](#eth_balanceat)
* [eth_stateAt](#eth_stateat)
* [eth_storageAt](#eth_storageat)
* [eth_countAt](#eth_countat)
* [eth_transactionCountByHash](#eth_transactioncountbyhash)
* [eth_transactionCountByNumber](#eth_transactioncountbynumber)
* [eth_uncleCountByHash](#eth_unclecountbyhash)
* [eth_uncleCountByNumber](#eth_unclecountbynumber)
* [eth_codeAt](#eth_codeat)
* [eth_transact](#eth_transact)
* [eth_call](#eth_call)
* [eth_flush](#eth_flush)
* [eth_blockByHash](#eth_blockbyhash)
* [eth_blockByNumber](#eth_blockbynumber)
* [eth_transactionByHash](#eth_transactionbyhash)
* [eth_transactionByBlockHashAndIndex](#eth_transactionbyblockhashandindex)
* [eth_transactionByNumber](#eth_transactionbynumber)
* [eth_uncleByHash](#eth_unclebyhash)
* [eth_uncleByNumber](#eth_unclebynumber)
* [eth_compilers](#eth_compilers)
* [eth_lll](#eth_lll)
* [eth_solidity](#eth_solidity)
* [eth_serpent](#eth_serpent)
* [eth_newFilter](#eth_newfilter)
* [eth_newFilterString](#eth_newfilterstring)
* [eth_uninstallFilter](#eth_uninstallfilter)
* [eth_changed](#eth_changed)
* [eth_filterLogs](#eth_filterlogs)
* [eth_logs](#eth_logs)
* [eth_getWork](#eth_getWork)
* [eth_submitWork](#eth_submitwork)
* [db_put](#db_put)
* [db_get](#db_get)
* [db_putString](#db_putstring)
* [db_getString](#db_getstring)
* [shh_post](#shh_post)
* [shh_newIdentity](#shh_newidentity)
* [shh_haveIdentity](#shh_haveidentity)
* [shh_newGroup](#shh_newgroup)
* [shh_addToGroup](#shh_addtogroup)
* [shh_newFilter](#shh_newfilter)
* [shh_uninstallFilter](#shh_uninstallfilter)
* [shh_changed](#shh_changed)
* [shh_getMessages](#shh_getmessages)


#### `web3_sha3`
*returns SHA3 of the given data*

* request:
```bash
curl -X POST --data '{"jsonrpc":"2.0","method":"web3_sha3","params":["0x68656c6c6f20776f726c64"],"id":64}' http://localhost:8080
```
### Response
```json
{
"id":64,
"jsonrpc":"2.0",
"result":"0x47173285a8d7341e5e972fc677286384f802f8ef42a5ec5f03bbfa254cb01fad"
}
```

***

#### `eth_coinbase`
*returns client coinbase address*

* request:
```bash
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_coinbase","params":[],"id":64}' http://localhost:8080
```
### Parameters 
none

### Response
```json
{
"id":64,
"jsonrpc":"2.0",
"result":"0x407d73d8a49eeb85d32cf465507dd71d507100c1"
}
```

***

#### `eth_setCoinbase`
DEPRECATED/REMOVED
```

***

#### `eth_listening`
*returns true if client is actively listening for network connections*

* request:
```bash
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_listening","params":[],"id":67}' http://localhost:8080
```

### Parameters 
none

### Response
```json
{
"id":67,
"jsonrpc":"2.0",
"result":true
}
```

***

#### `eth_setListening`
DEPRECATED/REMOVED
```

***

#### `eth_mining`
*returns true if client is actively mining new blocks*
* request:
```bash
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_mining","params":[],"id":71}' http://localhost:8080
```

### Parameters 
none

### Response
```json
{
"id":71,
"jsonrpc":"2.0",
"result":true
}

```

***

#### `eth_setMining`
DEPRECATED/REMOVED
```

***

#### `eth_gasPrice`
*returns special 256-bit number equal to hard-coded testnet price of gas*
* request:
```bash
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_gasPrice","params":[],"id":73}' http://localhost:8080
```

### Parameters 
none

### Response
```json
{
"id":73,
"jsonrpc":"2.0",
"result":"0x09184e72a000"
}
```

***

#### `eth_accounts`
*returns list of the addresses owned by client*
* request:
```bash
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_accounts","params":null,"id":1}' http://localhost:8080
```

### Parameters 
none

### Response
```json
{
"id":1,
"jsonrpc":"2.0",
"result":["0x407d73d8a49eeb85d32cf465507dd71d507100c1"]
}
```

***

#### `eth_peerCount`
*returns number of peers currenly connected to the client*
* request:
```bash
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_peerCount","params":[],"id":74}' http://localhost:8080
```

### Parameters 
none

### Response
```json
{
"id":74,
"jsonrpc":"2.0",
"result": 2
}
```

***

#### `eth_defaultBlock`
*returns default number/age to use when querying state. When positive this is a block number, when 0 or negative it is a block age. -1 therefore means the most recently mined block, 0 means the block being currently mined (i.e. to include pending transactions). Defaults to -1.*
* request:
```bash
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_defaultBlock","params":[],"id":77}' http://localhost:8080
```

### Parameters 
none

### Response
```js
{
"id":77,
"jsonrpc":"2.0",
"result": -1
}
```

#### `eth_setDefaultBlock`
DEPRTECATED/REMOVED
```

***

#### `eth_number`
*returns the number of most recent block*
* request:
```bash
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_number","params":[],"id":83}' http://localhost:8080
```

### Parameters 
none

### Response
```js
{
"id":83,
"jsonrpc":"2.0",
"result": 1207
}
```

***

#### `eth_balanceAt`
*returns the balance of the account of given address*
* request:
```bash
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_balanceAt","params":["0x407d73d8a49eeb85d32cf465507dd71d507100c1", "0x-1"],"id":1}' http://localhost:8080
```

### Parameters 

- address as hex string
- defaultBlock as signed integer

```js
params: [
   '0x407d73d8a49eeb85d32cf465507dd71d507100c1',
   '0x-1' // -1
]
```

### Response
```json
{
"id":1,
"jsonrpc":"2.0",
"result":"0x0234c8a3397aab580000"
}
```

***

#### `eth_stateAt`
*returns the value in storage at position[0] given by string of the account given by address["0x407d73d8a49eeb85d32cf465507dd71d507100c1"]*
* request:
```bash
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_stateAt","params":["0x407d73d8a49eeb85d32cf465507dd71d507100c1", "0x0", "0x-1"],"id":1}' http://localhost:8080
```

### Parameters 

- address as hex string
- the position in the storage
- defaultBlock as signed integer

```js
params: [
   '0x407d73d8a49eeb85d32cf465507dd71d507100c1',
   '0x0', // 0
   '0x-1' // -1
]
```

### Response
```json
{
"id":1,
"jsonrpc":"2.0",
"result":"0x03"
}
```

***

#### `eth_storageAt`
*returns storage dumped to json*
* request:
```bash
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_storageAt","params":["0x407d73d8a49eeb85d32cf465507dd71d507100c1", "0x-1"],"id":1}' http://localhost:8080
```
### Parameters 

- address as hex string
- defaultBlock as signed integer

```js
params: [
   '0x407d73d8a49eeb85d32cf465507dd71d507100c1',
   '0x-1' // -1
]
```

### Response
```json
{
"id":1,
"jsonrpc":"2.0",
"result":{"0x":"0x03"}
}
```

***

#### `eth_countAt`
*returns the number of transactions send from account of given address*
* request:
```bash
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_countAt","params":["0x407d73d8a49eeb85d32cf465507dd71d507100c1","0x-1"],"id":1}' http://localhost:8080
```

### Parameters 

- address as hex string
- defaultBlock as signed integer

```js
params: [
   '0x407d73d8a49eeb85d32cf465507dd71d507100c1',
   '0x-1' // -1
]
```

### Response
```js
{
"id":1,
"jsonrpc":"2.0",
"result":1
}
```

***

#### `eth_transactionCountByHash`
*returns the number of transactions in block of given block hash*
* request:
```bash
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_transactionCountByHash","params":["0x407d73d8a49eeb85d32cf465507dd71d507100c1"],"id":1}' http://localhost:8080
```

### Parameters 

- hash of a block

```js
params: [
   '0x407d73d8a49eeb85d32cf465507dd71d507100c1'
]
```

### Response
```js
{
"id":1,
"jsonrpc":"2.0",
"result":1
}
```

***

#### `eth_transactionCountByNumber`
*returns the number of transactions in block of given number*
* request:
```bash
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_transactionCountByNumber","params":["0xe8"],"id":1}' http://localhost:8080
```

### Parameters 

- number of a block

```js
params: [
   '0xe8', // 232
]
```

### Response
```js
{
"id":1,
"jsonrpc":"2.0",
"result":1
}
```

***

#### `eth_uncleCountByHash`
*returns the number of uncles for block of given hash*
* request:
```bash
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_uncleCountByHash","params":["0x407d73d8a49eeb85d32cf465507dd71d507100c1"],"id":1}' http://localhost:8080
```

### Parameters 

- hash of a block

```js
params: [
   '0x407d73d8a49eeb85d32cf465507dd71d507100c1'
]
```

### Response
```js
{
"id":1,
"jsonrpc":"2.0",
"result":1
}
```

***

#### `eth_uncleCountByNumber`
*returns the number of uncles for block of given number*
* request:
```bash
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_uncleCountByNumber","params":["0xe8"],"id":1}' http://localhost:8080
```

### Parameters

- number of a block

```js
params: [
   '0xe8', // 232
]
```

### Response
```js
{
"id":1,
"jsonrpc":"2.0",
"result":1
}
```

***

#### `eth_codeAt`
*returns data at a given address*
* request:
```bash
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_countAt","params":["0xd5677cf67b5aa051bb40496e68ad359eb97cfbf8"],"id":1}' http://localhost:8080
```

### Parameters

- address as hex string

```js
params: [
   '0xd5677cf67b5aa051bb40496e68ad359eb97cfbf8',
]
```

### Response
```json
{
"id":1,
"jsonrpc":"2.0",
"result":"0x600160008035811a818181146012578301005b601b6001356025565b8060005260206000f25b600060078202905091905056"
}
```

***

#### `eth_transact`
*creates new message call transaction.*
* request:
```bash
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_transact","params":[see below],"id":1}' http://localhost:8080
```
### Parameters

- The transaction object

```js
{
  "from": "0xb60e8dd61c5d32be8058bb8eb970870f07233155",
  "to": "0xd46e8dd67c5d32be8058bb8eb970870f072445675",
  "gas": "0x76c0",// 30400,
  "gasPrice": "0x9184e72a000", // hex of a big int
  "value": "0x9184e72a000", // hex of a big int
  "data": "0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675"
}
```

### Response
```json
{
"id":1,
"jsonrpc":"2.0",
"result":"0x7f9fade1c0d57a7af66ab4ead7c2eb7b11a91385"
}
```

***

#### `eth_call`
*executes new message call immediately without creating a transaction on the block chain.*
* request:
```bash
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_call","params":[see below],"id":1}' http://localhost:8080
```

### Parameters

- The call object

```js
{
  "to": "0xd46e8dd67c5d32be8058bb8eb970870f072445675",
  "data": "0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675"
}
```

### Response
```json
{
"id":1,
"jsonrpc":"2.0",
"result":"0x0000000000000000000000000000000000000000000000000000000000000015"
}
```

***

#### `eth_flush`
* request:
```bash
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_flush","params":[],"id":64}' http://localhost:8080
```

### Parameters
none

### Response
```json
{
"id":1,
"jsonrpc":"2.0",
"result":true
}
```

***

#### `eth_blockByHash`
*returns block info for block with given hash.*
* request:
```bash
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_blockByHash","params":["0xe670ec64341771606e55d6b4ca35a1a6b75ee3d5145a99d05921026d1527331", ""],"id":1}' http://localhost:8080
```

### Parameters

- hash of a block
- include transaction objects (If FALSE it only includes the hashes in an array)

```js
params: [
   '0xe670ec64341771606e55d6b4ca35a1a6b75ee3d5145a99d05921026d1527331',
   true
]
```

### Response
```js
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
   "unlces": ["0x1606e5...", "0xd5145a9..."]
  }
}
```

***

#### `eth_blockByNumber`
*returns block info for block with given number.*
* request:
```bash
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_blockByNumber","params":["0x1b4"],"id":1}' http://localhost:8080
```

### Parameters

- number of a block
- include transaction objects (If FALSE it only includes the hashes in an array)

```js
params: [
   '0x1b4', // 436
   true
]
```

### Response
See [blockByHash](#eth_blockbyhash)

***

#### `eth_transactionByHash`
*returns transaction with hash*
* request:
```bash
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_transactionByHash","params":["0xc6ef2fc5426d6ad6fd9e2a26abeab0aa2411b7ab17f30a99d3cb96aed1d1055b"],"id":1}' http://localhost:8080
```

### Parameters

- hash of a transaction

```js
params: [
   '0xe670ec64341771606e55d6b4ca35a1a6b75ee3d5145a99d05921026d1527331'
]
```

### Response
```js
{
"id":1,
"jsonrpc":"2.0",
"result": {
    "hash":"0xc6ef2fc5426d6ad6fd9e2a26abeab0aa2411b7ab17f30a99d3cb96aed1d1055b",
    "nonce":"0x",
    "blockHash": "0x6fd9e2a26abeab0aa2411c6ef2fc5426d6adb7ab17f30a99d3cb96aed1d1055b",
    "blockNumber": "0x15df", // 5599
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

#### `eth_transactionByBlockHashAndIndex`
*returns transaction with number[0] from the block with hash["0xc6ef2fc5426d6ad6fd9e2a26abeab0aa2411b7ab17f30a99d3cb96aed1d1055b"]*
* request:
```bash
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_transactionByHash","params":[0xc6ef2fc5426d6ad6fd9e2a26abeab0aa2411b7ab17f30a99d3cb96aed1d1055b, "0x0"],"id":1}' http://localhost:8080
```

### Parameters

- hash of a block
- index of the transaction

```js
params: [
   '0xe670ec64341771606e55d6b4ca35a1a6b75ee3d5145a99d05921026d1527331',
   '0x0' // 0
]
```

### Response
See [eth_transactionByHash](#eth_transactionbyhash)

***

#### `eth_transactionByNumber`
*returns transaction from the block with number[668] at positon number[0]*
* request:
```bash
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_transactionByNumber","params":["0x29c", "0x0"],"id":1}' http://localhost:8080
```

### Parameters

- number of the block
- index number of the transaction

```js
params: [
   '0x29c', // 668
   '0x0' // 0
]
```

### Response
See [eth_transactionByHash](#eth_transactionbyhash)

***

#### `eth_uncleByHash`
*returns uncle from the block with hash  "0xc6ef2fc5426d6ad6fd9e2a..." at index positon 0*
* request:
```bash
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_uncleByHash","params":["0xc6ef2fc5426d6ad6fd9e2a26abeab0aa2411b7ab17f30a99d3cb96aed1d1055b", "0x0"],"id":1}' http://localhost:8080
```

### Parameters

- hash of the block
- index number of the uncle

```js
params: [
   '0xc6ef2fc5426d6ad6fd9e2a26abeab0aa2411b7ab17f30a99d3cb96aed1d1055b',
   '0x0' // 0
]
```

### Response
See [blockByHash](#eth_blockbyhash)

***

#### `eth_uncleByNumber`
*returns uncle from the block with number 668 at index position 0*
* request:
```bash
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_uncleByNumber","params":["0x29c", "0x0"],"id":1}' http://localhost:8080
```

### Parameters

- number of the block
- index number of the uncle

```js
params: [
   '0x29c', // 668
   '0x0' // 0
]
```

### Response
See [blockByHash](#eth_blockbyhash)

***

#### `eth_compilers`
*returns list of available compilers for the client*
* request:
```bash
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_compilers","params":[],"id":1}' http://localhost:8080
```

### Parameters
none

### Response
```json
{
"id":1,
"jsonrpc":"2.0",
"result": ["lll", "solidity", "serpent"]
}
```

***

#### `eth_solidity`
*returns compiled solidity code*
* request:
```bash
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_solidity","params":["contract test { function multiply(uint a) returns(uint d) {   return a * 7;   } }"],"id":1}' http://localhost:8080
```

### Parameters

- the source code as string

```js
params: [
   '0x636f6e74726163742074657374207b2066756...', // "contract test { function multiply(uint a) returns(uint d) {   return a * 7;   } }"
]
```

### Response
```js
{
  "id":1,
  "jsonrpc":"2.0",
  "result":"0x603880600c6000396000f3006001600060e060020a600035048063c6888fa114601857005b6021600435602b565b8060005260206000f35b600081600702905091905056" // the compiled source code
}
```

***

#### `eth_lll`
*returns compiled lll code*
* request:
```bash
// TODO
```
### Response
```json
// TODO
```

***

#### `eth_serpent`
*returns compiled serpent code*
* request:
```bash
// TODO
```
### Response
```json
// TODO
```

***

#### `eth_newFilter`
*creates watch object to notify, when state changes in particular way, defined by filter. Returns new filter id.*
* request:
```bash
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_newFilter","params":[{"topic":"0x12341234"}],"id":73}' http://localhost:8080
```
### Response
```json
```

***

#### `eth_newFilterString`
*creates watch object to notify, when state changes in particular way, defined by filter. Returns new filter id.*
* request:
```bash
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_newFilterString","params":["pending"],"id":73}' http://localhost:8080
```
### Response
```json
{
"id":1,
"jsonrpc":"2.0",
"result": 1
}
```

***

#### `eth_uninstallFilter`
*uninstalls watch with given id. Should always be called when watch is no longer needed.*
* request:
```bash
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_uninstallFilter","params":[0],"id":73}' http://localhost:8080
```
### Response
```json
{
"id":1,
"jsonrpc":"2.0",
"result": true
}
```

***

#### `eth_changed`
*polling method, which returns array of logs which occurred since last poll*
* request:
```bash
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_changed","params":[0],"id":73}' http://localhost:8080
```
### Response
```js
{
"id":1,
"jsonrpc":"2.0",
"result": [{
  "address":"0x0000000000000000000000000000000000000000",
  "data":"0x0000000000000000000000000000000000000000000000000000000000000000",
  "number":"0x1b4" // 436
  }]
}
```

***

#### `eth_filterLogs`
*returns all logs matching filter with given id*
* request:
```bash
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_filterLogs","params":[0],"id":74}' http://localhost:8080
```
### Response
```js
{
"id":1,
"jsonrpc":"2.0",
"result": [{
  "address":"0x0000000000000000000000000000000000000000",
  "data":"0x0000000000000000000000000000000000000000000000000000000000000000",
  "number":"0x1b4" // 436
  }]
}
```

***

#### `eth_logs`
*returns all logs matching filter object*
* request:
```bash
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_logs","params":[{"topic":"0x12341234"}],"id":74}' http://localhost:8080
```
### Response
```js
{
"id":1,
"jsonrpc":"2.0",
"result": [{
  "address":"0x0000000000000000000000000000000000000000",
  "data":"0x0000000000000000000000000000000000000000000000000000000000000000",
  "number":"0x1b4" // 436
  }]
}
```

***

#### `eth_getWork`
*returns the hash of current block and difficulty to be met.*
* request:
```bash
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getWork","params":[],"id":73}' http://localhost:8080
```
### Response
```json
{
"id":1,
"jsonrpc":"2.0",
"result": ["0x1234567890abcdef1234567890abcdef", "0xd1ffic017i0000000000000000000000"]
}
```

The hash first of the two result values is the header hash without the nonce (the first part of the proof-of-work pair), the second of the two result values is the difficulty required to solve.

#### `eth_submitWork`
*Used for submitting a solution to the proof-of-work. The return value is true if the submission is valid.*
* request:
```bash
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_submitWork","params":["0x1234567890abcdef1234567890abcdef"],"id":73}' http://localhost:8080
```
### Response
```json
{
"id":1,
"jsonrpc":"2.0",
"result": true
}
```

***

#### `db_put`
*stores number in local database. First param is database name["test"], second is key["key"], third is value["5"]*
* request:
```bash
curl -X POST --data '{"jsonrpc":"2.0","method":"db_put","params":["test","key","5"],"id":73}' http://localhost:8080
```
### Response
```json
{
"id":1,
"jsonrpc":"2.0",
"result": true
}
```

***

#### `db_get`
*returns number from local database. First param is database name["test"], second is key["key"]*
* request:
```bash
curl -X POST --data '{"jsonrpc":"2.0","method":"db_get","params":["test","key"],"id":73}' http://localhost:8080
```
### Response
```json
{
"id":1,
"jsonrpc":"2.0",
"result": "0x05"
}
```

***

#### `db_putString`
*stores string in local database. First param is database name["test"], second is key["key"], third is value["5"]*
* request:
```bash
curl -X POST --data '{"jsonrpc":"2.0","method":"db_putString","params":["test","key","5"],"id":73}' http://localhost:8080
```
### Response
```json
{
"id":1,
"jsonrpc":"2.0",
"result": true
}
```

***

#### `db_getString`
*returns string from local database. First param is database name["test"], second is key["key"].*
* request:
```bash
curl -X POST --data '{"jsonrpc":"2.0","method":"db_getString","params":["test","key"],"id":73}' http://localhost:8080
```
### Response
```json
{
"id":1,
"jsonrpc":"2.0",
"result": "5"
}
```
#### `shh_post`
*sends whisper message*
* request:
```bash
"0x68656c6c6f20776f726c64"
curl -X POST --data '{"jsonrpc":"2.0","method":"db_getString","params":[{"from":"0xc931d93e97ab07fe42d923478ba2465f283f440fd6cabea4dd7a2c807108f651b7135d1d6ca9007d5b68aa497e4619ac10aa3b27726e1863c1fd9b570d99bbaf","topic":"0x68656c6c6f20776f726c64","payload":"0x68656c6c6f20776f726c64","ttl":100,"priority":100}],"id":73}' http://localhost:8080
```
* Parameters
```js
from: 
{
"0x0424406af6d2f23b503be4714e22ad34732d847a30f7c317d11d579a82e24f2e841e712298aad4837d96c84887232495def5cc4b70ed28e36513b52c951110d430",
topic: ["0x776869737065722d636861742d636c69656e74", "0x4d5a695276454c39425154466b61693532"],
payload: "0x7b2274797065223a226d6",
priority: "0x64", // TODO or workToProve?
ttl: "0x64",
}
```
### Response
```json
{
"id":1,
"jsonrpc":"2.0",
"result": true
}
```

***

#### `shh_newIdeninty`
*creates new whisper identity*
* request:
```bash
curl -X POST --data '{"jsonrpc":"2.0","method":"ssh_newIdentity","params":[],"id":73}' http://localhost:8080
```
### Response
```json
{
"id":1,
"jsonrpc":"2.0",
"result":"0xc931d93e97ab07fe42d923478ba2465f283f440fd6cabea4dd7a2c807108f651b7135d1d6ca9007d5b68aa497e4619ac10aa3b27726e1863c1fd9b570d99bbaf"
}
```

***

#### `shh_haveIdentity`
*returns true if client has given identity*
* request:
```bash
curl -X POST --data '{"jsonrpc":"2.0","method":"ssh_haveIdentity","params":["0xc931d93e97ab07fe42d923478ba2465f283f440fd6cabea4dd7a2c807108f651b7135d1d6ca9007d5b68aa497e4619ac10aa3b27726e1863c1fd9b570d99bbaf"],"id":73}' http://localhost:8080
```
### Response
```json
{
"id":1,
"jsonrpc":"2.0",
"result": true
}
```

***

#### `shh_newGroup`
* request:
```bash
// TODO: not implemented yet
```
### Response
```json
// TODO: not implemented yet
```

***

#### `shh_addToGroup`
* request:
```bash
// TODO: not implemented yet
```
### Response
```json
// TODO: not implemented yet
```

***

#### `shh_newFilter`
*creates watch object to notify, when client receives whisper message matching particular format, defined by filter. Returns new filter id.*
* request:
```bash
curl -X POST --data '{"jsonrpc":"2.0","method":"shh_newFilter","params":[{"topic":"0x68656c6c6f20776f726c64"}],"id":73}' http://localhost:8080
```
### Response
```json
{
"id":1,
"jsonrpc":"2.0",
"result": 7
}
```

***

#### `shh_uninstallFilter`
*uninstalls watch with given id. Should always be called when watch is no longer needed.*
* request:
```bash
curl -X POST --data '{"jsonrpc":"2.0","method":"shh_uninstallFilter","params":[7],"id":73}' http://localhost:8080
```
### Response
```json
{
"id":1,
"jsonrpc":"2.0",
"result": 7
}
```

***

#### `shh_changed`
*polling method, which returns array of messages which are received since last poll*
* request:
```bash
curl -X POST --data '{"jsonrpc":"2.0","method":"shh_uninstallFilter","params":[7],"id":73}' http://localhost:8080
```
### Response
```json
{
"id":1,
"jsonrpc":"2.0",
"result": [{
  "expiry":1422565026,
  "from":"0x3ec052fc3376f8a218b24b652803a5892038c39419a9a44923a61b113b7785d16ed1572df628859af3504670e4df31dcd8b3ee9a2110fd710c948690f0557394",
  "hash":"0x33eb2da77bf3527e28f8bf493650b1879b08c4f2a362beae4ba2f71bafcd91f9",
  "payload":"0x7b2274797065223a226d657373616765222c2263686174223a2265537668624d763939784c345442537741222c2274696d657374616d70223a7b222464617465223a313432323536343932363533377d2c22746f706963223a22222c2266726f6d223a7b226964656e74697479223a2230783365633035326663333337366638613231386232346236353238303361353839323033386333393431396139613434393233613631623131336237373835643136656431353732646636323838353961663335303436373065346466333164636438623365653961323131306664373130633934383639306630353537333934222c226e616d65223a22416365746f7465227d2c226d657373616765223a2273617361222c226964223a227a465232614e68594a666f4d4c62707333227d",
  "sent":1422564926,
  "to":"0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
  "topics":["0x6578616d"],
  "ttl":100,
  "workProved":0 // TODO or priority?
  }]
}
```

***

#### `shh_getMessages`
*returns array of whisper messages received by filter with given id.*
* request:
```bash
curl -X POST --data '{"jsonrpc":"2.0","method":"shh_getMessages","params":[7],"id":73}' http://localhost:8080
```
### Response
```json
{
"id":1,
"jsonrpc":"2.0",
"result":[{
  "expiry":1424106174,
  "from":"0xd551cf48d0b19ecad714109f287c5f1fa79a9d3779d8f1f83984968444c5236250aa1b30191bbc01d07e128857a28aee42cdf6b53298925f127959150802dbb0",
  "hash":"0x35fcd832607182dc2b78f1780186dab569dfd7ed31b7d94f958e9deccf6f0249",
  "payload":"0x7b2274797065223a226d657373616765222c2273656e64696e67223a747275652c2274696d657374616d70223a313432343130363037342c22746f706963223a22222c2266726f6d223a7b226964656e74697479223a2230786435353163663438643062313965636164373134313039663238376335663166613739613964333737396438663166383339383439363834343463353233363235306161316233303139316262633031643037653132383835376132386165653432636466366235333239383932356631323739353931353038303264626230222c226e616d65223a224469736f74677567227d2c226d657373616765223a223132222c2263686174223a224157746265357636387846727473724638222c226964223a22547a3470415353665257724d545257684b227d",
  "sent":1424106074,
  "to":"0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
  "topic":["0xa548145f","0x70a19471"],
  "ttl":100,
  "workProved":0 // TODO or priority?
}]
}
```