---
name: Whisper Wire Protocol
category: 
---

Peer-to-peer communications between nodes running Whisper clients run using the underlying [ÐΞVp2p Wire Protocol](https://github.com/ethereum/wiki/wiki/%C3%90%CE%9EVp2p-Wire-Protocol).

This is a preliminary wire protocol for the Whisper subsystem. It will change.

### Whisper Sub-protocol

**Status**
[`+0x00`: `P`, `protocolVersion`: `P`] Inform a peer of the **whisper** status. This message should be send _after_ the initial handshake and _prior_ to any **whisper** related messages.
* `protocolVersion` is one of:
    * `0x02` for PoC-7.

**Messages**
[`+0x01`: `P`, [`expiry1`: `P`, `ttl1`: `P`, [`topic1x1`: `B_4`, `topic1x2`: `B_4`, ...], `data1`: `B`, `nonce1`: `P`], [`expiry2`: `P`, ...], ...] Specify one or more messages. Nodes should not resend the same message to a peer in the same session, nor send a message back to a peer from which it received. This packet may be empty. The packet must be sent at least once per second, and only after receiving a **Messages** message from the peer.

### Session Management

For the Whisper sub-protocol, upon an active session, a `Status` message must be sent. Following the reception of the peer's `Status` message, the Whisper session is active. The peer with the greatest Node Id should send a **Messages** message to begin the message rally. From that point, peers take it in turns to send (possibly empty) Messages packets.

