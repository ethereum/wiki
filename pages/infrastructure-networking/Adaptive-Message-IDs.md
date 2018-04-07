---
name: Message IDs
category: 
---

### Goal

Dynamic numeric identities for the sub protocol message types rather than the current fixed id system. This way we don't have to reserve parts of the message ID space up front and have a central entity to police this space to prevent clashes.

### Overview

All sub-protocol message IDs begin at 0x10 and count only those messages in the shared protocols, in alphabetical order. Sub-protocol versioning is provided in the base protocol to allow guarantees that there is consensus over the number and order of messages for each sub-protocol between peers.

### Needed Changes

Wire protocol Hello package changed to (*note protocol version has changed to 1*):

**Hello**
[`0x00`: `P`, `p2pVersion`: `P`, `clientId`: `B`, [[`cap1`: `B_3`, `capVersion1`: `P`], [`cap2`: `B_3`, `capVersion2`: `P`], ...], `listenPort`: `P`, `nodeId`: `B_64`] First packet sent over the connection, and sent once by both sides. No other messages may be sent until a Hello is received.
* `p2pVersion` Specifies the implemented version of the P2P protocol. Now must be 1.
* `clientId` Specifies the client software identity, as a human-readable string (e.g. "Ethereum(++)/1.0.0").
* `cap` Specifies a peer capability name as a length-3 ASCII string. Current supported capabilities are `eth`, `shh`.
* `capVersion` Specifies a peer capability version as a positive integer. Current supported versions are 35 for `eth`, and 2 for `shh`.
* `listenPort` specifies the port that the client is listening on (on the interface that the present connection traverses). If 0 it indicates the client is not listening.
* `nodeId` is the Unique Identity of the node and specifies a 512-bit hash that identifies this node.

All `eth` sub-protocol message ids are lowered by `0x10` and have a `+` prepended to them to denote that the given ID is offset by some dynamic amount.

### Conversation overview

```
[23:11:29] gavofyork: for each shared sub protocol, in alphabetic order, you deploy the subprotocol's messages
[23:11:40] gavofyork: (after the basic p2p messages)
[23:12:42] gavofyork: so for two whisper-only peers, defined message types are Hello = 0x00, Disconnect, Ping, Pong, GetPeers, Peers = 0x05, WhisperMessage1 = 0x06, ...
[23:13:50] gavofyork: but for two peers that have eth and shh, you'd get:  Hello = 0x00, Disconnect, Ping, Pong, GetPeers, Peers = 0x05, Status = 0x06, GetTransactions = 0x07, Transactions = 0x08, GetBlockHashes = 0x09, BlockHashes = 0x0a, GetBlocks = 0x0b, Blocks = 0x0c, NewBlock = 0x0d, WhisperMessage1 = 0x0e, ...
[23:14:28] Jeffrey Wilcke: so what you mean is that you negotiate which message type gets what number
[23:14:34] gavofyork: exactamundo
[23:14:45] Jeffrey Wilcke: right interesting
[23:15:12] gavofyork: no arguing over what protocol gets what message-id space
[23:15:25] gavofyork: avoids any central registry or anything
[23:15:33] Jeffrey Wilcke: so receiving and sending might be completely different "numbers" but have the same meaning
[23:15:40] gavofyork: yup
[23:15:56] gavofyork: will make wire protocol analysis slightly harder
[23:16:02] gavofyork: though that's not necessarily a bad thing
[23:16:33] Jeffrey Wilcke: Ok so you tell a node that GetBlockHahses = 0x09
[23:17:37] Jeffrey Wilcke: GetBlockHashes internally is say 0x03. We give it an array with [0x03, 0x09] meaning "map GetBlockHashes to 0x09" or "when you receive 0x09 I mean GetBlockHashes".
[23:18:12] gavofyork: don't even need to do that
[23:18:21] gavofyork: so we'll need a map internally
[23:18:28] gavofyork: but there's no need to ever serialise it
[23:18:46] Jeffrey Wilcke: No, well you have to let the other know how you understand messages
[23:18:54] Jeffrey Wilcke: meaning that get hashes = 9
[23:19:01] gavofyork: "for each shared sub protocol, in alphabetic order, you deploy the subprotocol's messages"
[23:19:22] gavofyork: both form natural consensus on the alphabetic order of the shared subprotocols
[23:19:28] gavofyork: just from both Hello packets
[23:20:03] gavofyork: the one thing we should bring in is subprotocol versioning
[23:20:33] Jeffrey Wilcke: but how do we negotiate over the numbering?
[23:20:43] gavofyork: that is the negotiation :)
[23:21:04] gavofyork: you know the exact message type set & order in each subprotocol
[23:21:14] gavofyork: and you know the order they should be counted in
[23:21:26] gavofyork: and you know where to count from
[23:21:28] Jeffrey Wilcke: of course, but how do negotiate over the subprotocol
[23:21:33] gavofyork: oh
[23:21:36] gavofyork: that's in the Hello packet
[23:21:45] gavofyork: that contains the supported subprotocols
[23:21:52] gavofyork: e.g. A <=> B:
[23:21:53] Jeffrey Wilcke: aaah i follow
[23:21:56] gavofyork: ok
[23:22:11] Jeffrey Wilcke: right ok. i thought you meant alphabetical in the _messages_
[23:22:19] Jeffrey Wilcke: ok makes sense
```