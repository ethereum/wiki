In order to satisfy the data needed by the new JS API (https://github.com/ethereum/wiki/wiki/JavaScript-API), we propose a set of changes to the JSON-RPC API spec (https://github.com/ethereum/wiki/wiki/JSON-RPC) which is used by the JS API to get information from the Ethereum node and to perform actions.

Proposed changes:

All values in returned objects (transactions, blocks, shh posts) are transfered in HEX.
Simple values like, peerCount, defaultBlock stay as integer.

Change needed changing all out and inputs to HEX:

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

https://github.com/ethereum/wiki/wiki/JSON-RPC#eth_transactionbyhash
### eth_transactionByHash

Return transaction from passing in the transaction hash as input, not the block hash and number.

### eth_blockByHash and eth_blockByNumber 

Add the following return values as needed as they are returned by 
web3.eth.getBlock https://github.com/ethereum/wiki/wiki/JavaScript-API#web3ethgetblock

* `minGasPrice` (minimum price in wei per gas the miner accepted for txs in this block)
* `gasUsed`
* `children` (array of block hashes of all children of the block)
* `totalDifficulty` (total difficulty of block as specified in Yellow Paper (YP))
* `logsBloom` (the logsBloom filter of this block as specified in YP)
* `transactions` (array of transaction objects for all transactions in this block)
* `uncles` (array of block hashes for all uncles of this block)

### eth_logs, eth_filterLogs, eth_logs and eth_changed

Add the following return value to any returned logs. This allows developers to better debug logs, as well as provide additional data (like value send, gasPrice payed etc), which are connected to this log.

* `transaction` (object of the transaction this log belongs to)


