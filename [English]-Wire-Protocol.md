Peer-to-peer communications between nodes running Ethereum clients are designed to be governed by a simple wire-protocol making use of existing Ethereum technologies and standards such as RLP wherever practical.

This document intents to specify this protocol comprehensively.


### Low-Level

Ethereum nodes may connect to each other over TCP only. Peers are free to advertise and accept connections on any port(s) they wish, however, a default port on which the connection may be listened and made will be 30303.

Though TCP provides a connection-oriented medium, Ethereum nodes communicate in terms of packets. These packets are formed as a 4-byte synchronisation token (0x22400891), a 4-byte "payload size", to be interpreted as a big-endian integer and finally an N-byte RLP-serialised data structure, where N is the aforementioned "payload size". To be clear, the payload size specifies the number of bytes in the packet ''following'' the first 8.


### Payload Contents

There are a number of different types of payload that may be encoded within the RLP. This ''type'' is always determined by the first entry of the RLP, interpreted as an integer:

**Hello**
* `[0x00, PROTOCOL_VERSION, NETWORK_ID, CLIENT_ID, CAPABILITIES, LISTEN_PORT, NODE_ID]`
* First packet sent over the connection, and sent once by both sides. No other messages may be sent until a Hello is received.
* `CLIENT_ID` Specifies the client software identity, as a human-readable string (e.g. "Ethereum(++)/1.0.0").
* `PROTOCOL_VERSION` is one of:
* `0x00` for PoC-1;
* `0x01` for PoC-2;
* `0x07` for PoC-3.
* `NETWORK_ID` should be 0.
* `LISTEN_PORT` specifies the port that the client is listening on (on the interface that the present connection traverses). If 0 it indicates the client is not listening.
* `CAPABILITIES` specifies the capabilities of the client as a set of flags; presently three bits are used: `0x01` for peers discovery, `0x02` for transaction relaying, `0x04` for block-chain querying.
* `NODE_ID` is optional and specifies a 512-bit hash, (potentially to be used as public key) that identifies this node.

**Disconnect**
* `[0x01, REASON]`
* Inform the peer that a disconnection is imminent; if received, a peer should disconnect immediately. When sending, well-behaved hosts give their peers a fighting chance (read: wait 2 seconds) to disconnect to before disconnecting themselves.
* `REASON` is an optional integer specifying one of a number of reasons for disconnect:
* `0x00` Disconnect requested;
* `0x01` TCP sub-system error;
* `0x02` Bad protocol;
* `0x03` Useless peer;
* `0x04` Too many peers;
* `0x05` Already connected;
* `0x06` Wrong genesis block;
* `0x07` Incompatible network protocols.

**Ping**
* `[0x02]`
* Requests an immediate reply of `Pong` from the peer.

**Pong**
* `[0x03]`
* Reply to peer's '''Ping''' packet.

**GetPeers**
* `[0x10]`
* Request the peer to enumerate some known peers for us to connect to. This should include the peer itself.

**Peers**
* `[0x11, [IP1, Port1, Id1], [IP2, Port2, Id2], ... ]`
* Specifies a number of known peers. `IP` is a 4-byte array 'ABCD' that should be interpreted as the IP address A.B.C.D. `Port` is a 2-byte array that should be interpreted as a 16-bit big-endian integer. `Id` is the 512-bit hash that acts as the unique identifier of the node.

**Transactions**
* `[0x12, [nonce, receiving_address, value, ... ], ... ]`
* Specify (a) transaction(s) that the peer should make sure is included on its transaction queue. The items in the list (following the first item `0x12`) are transactions in the format described in the main Ethereum specification.

**Blocks**
* `[0x13, [block_header, transaction_list, uncle_list], ... ]`
* Specify (a) block(s) that the peer should know about. The items in the list (following the first item, `0x13`) are blocks in the format described in the main Ethereum specification.

**GetChain**
* `[0x14, Parent1, Parent2, ..., ParentN, Count]`
* Request the peer to send `Count` (to be interpreted as an integer) blocks in the current canonical block chain that are children of `Parent1` (to be interpreted as a SHA3 block hash). If `Parent1` is not present in the block chain, it should instead act as if the request were for `Parent2` &c. through to `ParentN`. If the designated parent is the present block chain head, an empty reply should be sent. If none of the parents are in the current canonical block chain, then `NotInChain` should be sent along with `ParentN` (i.e. the last Parent in the parents list). If no parents are passed, then a reply need not be made.

**NotInChain**
* `[0x15, Hash]`
* Tell the peer that the given hash was not found in its block chain.

**GetTransactions**
* `[0x16]`
* Request the peer to send all transactions currently in the queue. See Transactions.


### Example Packets

`0x22400891000000088400000043414243`

A Hello packet specifying the client id is "ABC".

Peer 1: `0x22400891000000028102`

Peer 2: `0x22400891000000028103`

A Ping and the returned Pong.


### Session Management

Upon connecting, all clients (i.e. both sides of the connection) must send a Hello message. Upon receiving the Hello message and verifying compatibility of the network and versions, a session is active and any other messages may be sent.

At any time, a Disconnect message may be sent.
