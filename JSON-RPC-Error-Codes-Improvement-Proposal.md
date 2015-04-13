To help developers add proper error handling in the dapp side, we need to implement custom error codes.

Possible errors:

JSON RPC Standard errors:

| Code    | Possible Return message | Description |
| --------|-------------------------|-------------|
|-32700 | Parse error       | Invalid JSON was received by the server. An error occurred on the server while parsing the JSON text. |
|-32600 | Invalid Request   | The JSON sent is not a valid Request object. |
|-32601 | Method not found  | The method does not exist / is not available. |
|-32602 | Invalid params    | Invalid method parameter(s). |
|-32603 | Internal error    | Internal JSON-RPC error. |
|-32000 to -32099             | `Server error`. Reserved for implementation-defined server-errors. |

Custom error codes:

| Code    | Possible Return message | Description |
| --------|-------------------------|-------------|
|1 | Unauthorized       | Should be used when some action is not authorized, e.g. sending from a locked account.
|2 | Action not allowed | Should be used when some action is not allowed, e.g. preventing an action, while another depending action is processing on, like sending again when a confirmation popup is shown to the user (?).
|3 | X doesn't exist    | Should be used when something which should be there is not found. (Doesn't apply for not found transactions or blocks. They return a success with value `null`)
|4 | Requires X         | Should be used for actions which require somethin else, e.g. gas or a value.
|5 | X to low           | Should be used when a to low value of something was given, e.g. an to low gas amount.
|6 | X limit exceeded   | Should be used when a limit is exceeded, e.g. for the gas limit in a block.
|7 | Rejected           | Should be used when an action was rejected, e.g. because of its content (to long contract code, containing wrong characters ?, should differ from `-32602` - Invalid params).
|8 | Timeout            | Should be used when an action timedout.
|9 | Conflict           | Should be used when an action conflicts with another (ongoing?) action.