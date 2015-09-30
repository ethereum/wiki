---
name: Reversion Notification Proposal
category: 
---

## **JSONRPC** changes:

`eth_getFilterChanges`, `eth_getFilterLogs` both return additional fields: "type" and "polarity"

- `type`: `STRING` - `pending` when the log is pending. `mined` if log is already mined.
- `polarity`:`BOOL` - either `true` (the transaction pertaining to this log was mined or arrived as pending compared to the previous notification/notified block) or `false` (the transaction pertaining to this log was reverted or was deleted compared to the previous notification/notified block).

```diff
// Result
{
  "id":1,
  "jsonrpc":"2.0",
  "result": [{
    "logIndex": "0x1", // 1
    "blockNumber":"0x1b4" // 436
    "blockHash": "0x8216c5785ac562ff41e2dcfdf5785ac562ff41e2dcfdf829c5a142f1fccd7d",
    "transactionHash":  "0xdf829c5a142f1fccd7d8216c5785ac562ff41e2dcfdf5785ac562ff41e2dcf",
    "transactionIndex": "0x0", // 0
    "address": "0x16c5785ac562ff41e2dcfdf829c5a142f1fccd7d",
    "data":"0x0000000000000000000000000000000000000000000000000000000000000000",
+   "type":"mined",
+   "polarity": true,
    "topics": ["0x59ebeb90bc63057b6515673c3ecf9438e5058bca0f92585014eced636878c9a5"]
    },{
      ...
    }]
}
```

- logs order

logs should be returned in order, from earliest to latest.

In case of fork, the order should be following:

```
//
// ......1 - 2 - 3a - 4a
// ...............\- 3b - 4b - 5b
//
// getLogs({fromBlock: 4a, toBlock: 5b}) should return
// 4a, 3a, 3b, 4b, 5b
//
```



## **JSONRPC** new methods:

- `eth_newFilterEx` (new version of `eth_newFilter`)
- `eth_getLogsEx` (new version of `eth_getLogs`)

They expect 'fromBlock', 'toBlock' to be block hash instead of block number.

```diff
params: [{
-  "fromBlock": "0x1",
+  "fromBlock": "0x599438826541d3e8c29c167fa15b491d91b65e422f8b8a3da69eaa9a43c832e1",
-  "toBlock": "0x2",
+  "toBlock": "0x599438826541d3e8c29c167fa15b491d91b65e422f8b8a3da69eaa9a43c832e1",
  "address": "0x8888f1f195afa192cfee860698584c030f4c9db1",
  "topics": ["0x000000000000000000000000a94f5374fce5edbc8e2a8697c15331677e6ebf0b"]
}]
```

- `eth_getFilterChangesEx` (new version of `eth_getFilterChanges`)
- `eth_getFilterLogsEx` (new version of `eth_getFilterLogs`)

`eth_getFilterChangesEx` && `eth_getFilterLogsEx` have the same input params as `eth_getFilterChanges` && `eth_getFilterLogs`, but different format of returned objects.

```
// Result
{
  "id":1,
  "jsonrpc":"2.0",
  "result": [{
    "blockNumber":"0x1b4" // 436
    "blockHash": "0x8216c5785ac562ff41e2dcfdf5785ac562ff41e2dcfdf829c5a142f1fccd7d",
    "type": "pending",
    "polarity": true,
    "logs": [{
        "logIndex": "0x1", // 1
        "transactionHash":  "0xdf829c5a142f1fccd7d8216c5785ac562ff41e2dcfdf5785ac562ff41e2dcf",
        "transactionIndex": "0x0", // 0
        "address": "0x16c5785ac562ff41e2dcfdf829c5a142f1fccd7d",
        "data":"0x0000000000000000000000000000000000000000000000000000000000000000",
        "topics": ["0x59ebeb90bc63057b6515673c3ecf9438e5058bca0f92585014eced636878c9a5"]
        },{
        ...
    }],
    },{
      ...
    }]
}
```


## Comments:

**Fabian**: I like it but i would change three things:

1. `polarity: true` -> `invalidated: true` or `invalid: true`
2. get rid of the old filter and add `eth_newFilterEx` as `eth_newLogFilter`, to make it fit the other filter type names (`eth_newBlockFilter`, `etH_newPendingTransactionFilter`)
3. Don't return logs grouped by blocks, as i don't see an advantage besides that it is harder to parse. (Logs don't really care in which block they came, though we need to add the `blockX` properties for reference)