## **JSONRPC** changes:

`eth_newFilter` accept from/to as block hashes.

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

## **JSONRPC** new methods:

- `eth_getFilterChangesEx`
- `eth_getFilterLogsEx`

`eth_getFilterChangesEx` && `eth_getFilterLogsEx` have the same input params as `eth_getFilterChanges` && `eth_getFilterLogs`, but different format of returned objects.

##### Returns

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