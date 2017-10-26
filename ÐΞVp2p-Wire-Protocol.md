---
name: DEV P2P Wire Protocol
category: 
---

Peer-to-peer communications between nodes running Ethereum/Whisper/&c. clients are designed to be governed by a simple wire-protocol making use of existing ÐΞV technologies and standards such as [RLP](https://github.com/ethereum/wiki/wiki/RLP) wherever practical.

This document is intended to specify this protocol comprehensively.

### Low-Level

ÐΞVp2p nodes communicate by sending messages using RLPx, an encrypted and authenticated transport protocol. Peers are free to advertise and accept connections on any TCP ports they wish, however, a default port on which the connection may be listened and made will be 30303.
Though TCP provides a connection-oriented medium, ÐΞVp2p nodes communicate in terms of packets.
RLPx provides facilities to send and receive packets. For more information about RLPx, refer to the [protocol specification](https://github.com/ethereum/devp2p/tree/master/rlpx.md). 

ÐΞVp2p nodes find peers through the RLPx discovery protocol DHT. Peer connections can also be initiated by supplying
the endpoint of a peer to a client-specific RPC API.

### Payload Contents

There are a number of different types of payload that may be encoded within the RLP. This ''type'' is always determined by the first entry of the RLP, interpreted as an integer.

ÐΞVp2p is designed to support arbitrary sub-protocols (aka _capabilities_) over the basic wire protocol. Each sub-protocol is given as much of the message-ID space as it needs (all such protocols must statically specify how many message IDs they require). On connection and reception of the `Hello` message, both peers have equivalent information about what subprotocols they share (including versions) and are able to form consensus over the composition of message ID space.

Message IDs are assumed to be compact from ID 0x10 onwards (0x00-0x10 is reserved for ÐΞVp2p messages) and given to each shared (equal-version, equal name) sub-protocol in alphabetic order. Sub-protocols that are not shared are ignored. If multiple versions are shared of the same (equal name) sub-protocol, the numerically highest wins, others are ignored.

### P2P

**Hello**
`0x00` [`p2pVersion`: `P`, `clientId`: `B`, [[`cap1`: `B_3`, `capVersion1`: `P`], [`cap2`: `B_3`, `capVersion2`: `P`], `...`], `listenPort`: `P`, `nodeId`: `B_64`] First packet sent over the connection, and sent once by both sides. No other messages may be sent until a Hello is received.
* `p2pVersion` Specifies the implemented version of the P2P protocol. Now must be 1.
* `clientId` Specifies the client software identity, as a human-readable string (e.g. "Ethereum(++)/1.0.0").
* `cap` Specifies a peer capability name as a length-3 ASCII string. Current supported capabilities are `eth`, `shh`.
* `capVersion` Specifies a peer capability version as a positive integer. Current supported versions are 34 for `eth`, and 1 for `shh`.
* `listenPort` specifies the port that the client is listening on (on the interface that the present connection traverses). If 0 it indicates the client is not listening.
* `nodeId` is the Unique Identity of the node and specifies a 512-bit hash that identifies this node.

**Disconnect**
`0x01` [`reason`: `P`] Inform the peer that a disconnection is imminent; if received, a peer should disconnect immediately. When sending, well-behaved hosts give their peers a fighting chance (read: wait 2 seconds) to disconnect to before disconnecting themselves.
* `reason` is an optional integer specifying one of a number of reasons for disconnect:
  * `0x00` Disconnect requested;
  * `0x01` TCP sub-system error;
  * `0x02` Breach of protocol, e.g. a malformed message, bad RLP, incorrect magic number &c.;
  * `0x03` Useless peer;
  * `0x04` Too many peers;
  * `0x05` Already connected;
  * `0x06` Incompatible P2P protocol version;
  * `0x07` Null node identity received - this is automatically invalid;
  * `0x08` Client quitting;
  * `0x09` Unexpected identity (i.e. a different identity to a previous connection/what a trusted peer told us).
  * `0x0a` Identity is the same as this node (i.e. connected to itself);
  * `0x0b` Timeout on receiving a message (i.e. nothing received since sending last ping);
  * `0x10` Some other reason specific to a subprotocol.

**Ping**
`0x02` [] Requests an immediate reply of `Pong` from the peer.

**Pong**
`0x03` [] Reply to peer's `Ping` packet.

**NotImplemented (was GetPeers)**
`0x04`

**NotImplemented (was Peers)**
`0x05`

### Node identity and reputation

The identity of a ÐΞVp2p node is a secp256k1 public key.

Nodes are free to store ratings for given IDs (how useful the node has been in the past) and give preference accordingly. Nodes may also track node IDs (and their provenance) in order to help determine potential man-in-the-middle attacks.
Clients are free to mark down new nodes and use the node ID as a means of determining a node's reputation.

### Session Management

Upon connecting, all clients (i.e. both sides of the connection) must send a `Hello` message. Upon receiving the `Hello` message and verifying compatibility of the network and versions, a session is active and any other P2P messages may be sent.

At any time, a Disconnect message may be sent.