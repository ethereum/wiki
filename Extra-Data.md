Implementations are encouraged to follow this protocol for populating the `extraData` field of mined blocks.

`extraData` should be an RLP list whose first element is a version identifier encoded as a canonical RLP positive integer. All other items in the list are determined by the version ID.

`[` version: `P`, ... `]`

### Version 0

`[` version: `P`, clientIdentity: `B` `]`

Two further arguments, a raw representation of a string to identify the client (this would usually be the same client identifier as returned by the JSON RPC's `web3_clientVersion`). Secondly, the node's DEVp2p network ID.
