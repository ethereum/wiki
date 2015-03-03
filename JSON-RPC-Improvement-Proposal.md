In order to satisfy the data needed by the new JS API (https://github.com/ethereum/wiki/wiki/JavaScript-API), we propose a set of changes to the JSON-RPC API spec (https://github.com/ethereum/wiki/wiki/JSON-RPC) which is used by the JS API to get information from the Ethereum node and to perform actions.

Proposed changes:

All values in returned objects (transactions, blocks, shh posts) are transfered in HEX.
Simple values like, peerCount, defaultBlock stay as integer.

Change needed changing all out and inputs to HEX (for all):

- [x] eth_transact
- [ ] eth_blockByHash
- [ ] eth_blockByNumber
- [x] eth_transactionByHash
- [ ] eth_uncleByHash
- [ ] eth_uncleByNumber
- [ ] eth_changed
- [ ] eth_filterLogs
- [ ] eth_logs
- [ ] shh_changed
- [ ] shh_getMessages
- [ ] shh_post

### name changes

- [ ] net_peerCount
- [ ] net_listening


### Remove the following RPC functions

- [ ] eth_setCoinbase
- [ ] eth_setListening
- [ ] eth_setMining
- [ ] eth_setDefaultBlock (must be passed as the second parameter to the specific funcitons
- [ ] eth_defaultBlock (**We pass it now on per function basis**)

### add

- [ ] eth_version ? is network version ===  node/client version
- [ ] shh_version ? is network version ===  node/client version
- [ ] net_version ? is network version ===  node/client version

### add as last parameter "defaultBlock" to the following methods:

- [ ] eth_balanceAt
- [ ] eth_codeAt
- [ ] eth_stateAt
- [ ] eth_storageAt
- [ ] eth_countAt
- [ ] eth_call

### eth_transactionByHash
https://github.com/ethereum/wiki/wiki/JSON-RPC#eth_transactionbyhash

Rename to `eth_transactionByBlockHashAndIndex`

*Release: replaced by new function `eth_transactionByHash`:*

Accepts the transaction hash and returns the transaction.

### eth_blockByHash and eth_blockByNumber 

Add the following return values as needed as they are returned by 
web3.eth.getBlock https://github.com/ethereum/wiki/wiki/JavaScript-API#web3ethgetblock

add **additional 3rd parameter (Boolean)** include transactionObjects (default: FALSE)

* `minGasPrice` (minimum price in wei per gas the miner accepted for txs in this block)
* `gasUsed`
* `children` (array of block hashes of all children of the block)
* `totalDifficulty` (total difficulty of block as specified in Yellow Paper (YP))
* `logsBloom` (the logsBloom filter of this block as specified in YP)
* `transactions` (array of transaction objects or hashes for all transactions in this block)
* `unlces` array of block hashes
* `size` integer in bytes


### eth_transactionBy*

- [ ] `blockHash`
- [ ] `blockNumber` (rename from number)
- [ ] `transactionIndex`


### eth_logs, eth_filterLogs, eth_logs and eth_changed

Add the following return value to any returned logs. This allows developers to better debug logs, as well as provide additional data (like value send, gasPrice payed etc), which are connected to this log.

- [ ] `transactionHash` (hash of transaction, this event log belongs to)
- [ ] `blockHash`
- [ ] `transactionIndex`
- [ ] `logIndex`
- [ ] `hash`
- [ ] `blockNumber` (rename from number)


