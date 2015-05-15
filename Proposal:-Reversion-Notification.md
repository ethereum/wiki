JSONRPC changes:

getFilterChanges, newFilter both accept from/to as block hashes.

Outputs to getFilterChanges, getFilterLogs and getLogs all include: type: "enacted", "reverted".