---
name: JSON RPC Error Codes Improvement Proposal
category: 
---

To help developers add proper error handling in the dapp side, we need to implement custom error codes for the returned JSON RPC errors.

## JSON RPC Standard errors

| Code    | Possible Return message | Description |
| --------|-------------------------|-------------|
|-32700 | Parse error       | Invalid JSON was received by the server. An error occurred on the server while parsing the JSON text. |
|-32600 | Invalid Request   | The JSON sent is not a valid Request object. |
|-32601 | Method not found  | The method does not exist / is not available. |
|-32602 | Invalid params    | Invalid method parameter(s). |
|-32603 | Internal error    | Internal JSON-RPC error. |
|-32000 to -32099             | `Server error`. Reserved for implementation-defined server-errors. |

## Custom error codes:

| Code    | Possible Return message | Description |
| --------|-------------------------|-------------|
|1 | Unauthorized       | Should be used when some action is not authorized, e.g. sending from a locked account.
|2 | Action not allowed | Should be used when some action is not allowed, e.g. preventing an action, while another depending action is processing on, like sending again when a confirmation popup is shown to the user (?).
|3 | Execution error    | Will contain a subset of custom errors in the data field. See below. |

### Custom error fields

Custom error `3` can contain custom error(s) to further explain what went wrong.
They will be contained in the `data` field of the RPC error message as follows:

```js
{
    code: 3,
    message: 'Execution error',
    data: [{
        code: 102,
        message: 'Innsufficient gas',
        reason: 'GAS'
    },
    {
        code: 103,
        message: 'Gas limit exceeded',
        reason: 'GAS'
    }]
}
```

| Code    | Reson values | Possible Return message | Description |
| --------|--------------|-------------------------|-------------|
|100 | ? | X doesn't exist    | Should be used when something which should be there is not found. (Doesn't apply for not found transactions or blocks. They return a success with value `null`)
|101 | `ETHER` ? | Requires X         | Should be used for actions which require somethin else, e.g. gas or a value.
|102 | `GAS`, `ETHER` | X to low           | Should be used when a to low value of something was given, e.g. an to low gas amount.
|103 | `GAS` | X limit exceeded   | Should be used when a limit is exceeded, e.g. for the gas limit in a block.
|104 | `CODE` ? | Rejected           | Should be used when an action was rejected, e.g. because of its content (to long contract code, containing wrong characters ?, should differ from `-32602` - Invalid params).


## Possible future error codes?

| Code    | Possible Return message | Description |
| --------|-------------------------|-------------|
|105 | Timeout            | Should be used when an action timedout.
|106 | Conflict           | Should be used when an action conflicts with another (ongoing?) action.