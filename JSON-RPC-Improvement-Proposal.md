In order to satisfy the data needed by the new JS API (https://github.com/ethereum/wiki/wiki/JavaScript-API), we propose a set of changes to the JSON-RPC API spec (https://github.com/ethereum/wiki/wiki/JSON-RPC) which is used by the JS API to get information from the Ethereum node and to perform actions.

Proposed changes:

### eth_blockByHash and eth_blockByNumber 

Add the following return values as needed as they are returned by 
web3.eth.getBlock https://github.com/ethereum/wiki/wiki/JavaScript-API#web3ethgetblock

* Add `minGasPrice` (minimum price in wei per gas the miner accepted for txs in this block)
* Add `gasUsed`
* Add `children` (array of block hashes of all children of the block)
* Add `totalDifficulty` (total difficulty of block as specified in Yellow Paper (YP))
* add `logsBloom` (the logsBloom filter of this block as specified in YP)
* Add `transactions` (array of tx objects for all txs in this block)


