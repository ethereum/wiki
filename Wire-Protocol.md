Peer-to-peer communications between nodes running Ethereum clients are designed to be governed by a simple wire-protocol making use of existing Ethereum technologies and standards such as [RLP](https://github.com/ethereum/wiki/wiki/%5BEnglish%5D-RLP) wherever practical.

This document intents to specify this protocol comprehensively.


### Low-Level

Ethereum nodes may connect to each other over TCP only. Peers are free to advertise and accept connections on any port(s) they wish, however, a default port on which the connection may be listened and made will be 30303.

Though TCP provides a connection-oriented medium, Ethereum nodes communicate in terms of packets. These packets are formed as a 4-byte synchronisation token (0x22400891), a 4-byte "payload size", to be interpreted as a big-endian integer and finally an N-byte RLP-serialised data structure, where N is the aforementioned "payload size". To be clear, the payload size specifies the number of bytes in the packet ''following'' the first 8.

### Basic Chain Syncing
- Two peers connect & say Hello and send their Status message. Status includes the TD & hash of their best block.
- The client with the worst TD asks peer for full chain of just block hashes.
- Chain of hashes is stored in space shared by all peer connections, and used as a "work pool".
- While there are hashes in the chain of hashes that we don't have in our chain:
  - Ask for N blocks from our peer using the hashes. Mark them as on their way so we don't get them from another peer.

### Payload Contents

There are a number of different types of payload that may be encoded within the RLP. This ''type'' is always determined by the first entry of the RLP, interpreted as an integer.

The protocol is split up in two parts, the **P2P** protocol and **ethereum** messaging protocol.

### P2P

**Hello**
* `[0x00, P2P_VERSION, CLIEND_ID, CAPS, LISTEN_PORT, NODE_ID]`
* First packet sent over the connection, and sent once by both sides. No other messages may be sent until a Hello is received.
* `P2P_VERSION` Specifies the implemented version of the P2P protocol.
* `CLIENT_ID` Specifies the client software identity, as a human-readable string (e.g. "Ethereum(++)/1.0.0").
* `CAPS` Specifies the peers capabilities. This is an arbitrary length array, each entry denoting the capability with 3 `8 bit` characters. Current supported capabilities are `eth`, `bzz`, `shh`.
* `LISTEN_PORT` specifies the port that the client is listening on (on the interface that the present connection traverses). If 0 it indicates the client is not listening.
* `NODE_ID` is the Unique Identity of the node and specifies a 512-bit hash that identifies this node.


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
* `0x07` Incompatible network protocols;
* `0x08` Client quitting.

**Ping**
* `[0x02]`
* Requests an immediate reply of `Pong` from the peer.

**Pong**
* `[0x03]`
* Reply to peer's `Ping` packet.

**GetPeers**
* `[0x04]`
* Request the peer to enumerate some known peers for us to connect to. This should include the peer itself.

**Peers**
* `[0x05, [IP1, Port1, Id1], [IP2, Port2, Id2], ... ]`
* Specifies a number of known peers. `IP` is a 4-byte array 'ABCD' that should be interpreted as the IP address A.B.C.D. `Port` is a 2-byte array that should be interpreted as a 16-bit big-endian integer. `Id` is the 512-bit hash that acts as the unique identifier of the node.

### Ethereum

**Status**
* `[0x10, [PROTOCOL_VERSION, NETWORK_ID, TD, BEST_HASH, GENESIS_HASH]`
* Inform a peer of it's current **ethereum** state. This message should be send _after_ the initial handshake and _prior_ to any **ethereum** related messages.
* `PROTOCOL_VERSION` is one of:
    * `0x00` for PoC-1;
    * `0x01` for PoC-2;
    * `0x07` for PoC-3;
    * `0x09` for PoC-4.
    * `0x17` for PoC-5.
    * `0x1c` for PoC-6.
* `NETWORK_ID` should be 0.
* `TD`: Total Difficulty of the best chain. Integer, as found in block header.
* `BEST_HASH`: The hash of the best (i.e. highest TD) known block.
* `GENESIS_HASH`: The hash of the Genesis block

**Transactions**
* `[0x12, [nonce, receiving_address, value, ... ], ... ]`
* Specify (a) transaction(s) that the peer should make sure is included on its transaction queue. The items in the list (following the first item `0x12`) are transactions in the format described in the main Ethereum specification.

**Blocks**
* `[0x13, [block_header, transaction_list, uncle_list], ... ]`
* Specify (a) block(s) that the peer should know about. The items in the list (following the first item, `0x13`) are blocks in the format described in the main Ethereum specification.

**GetTransactions**
* `[0x16]`
* Request the peer to send all transactions currently in the queue. See Transactions.

**GetBlockHashes**
* [`0x17`, [ `hash` : `B_32`, `maxBlocks`: `P` ]] 
* Requests a `BlockHashes` message of at most `maxBlocks` entries, of block hashes from the blockchain, starting at the parent of block `hash`. Does not _require_ the peer to give `maxBlocks` hashes - they could give somewhat fewer.

**BlockHashes**
* [`0x18`, [ `hash_0`: `B_32`, `hash_1`: `B_32`, .... ]]
* Gives a series of hashes of blocks (each the child of the next). This implies that the blocks are ordered from youngest to oldest.

**GetBlocks**
* [`0x19`,[ `hash_0`: `B_32`, `hash_1`: `B_32`, .... ]]
* Requests a `Blocks` message detailing a number of blocks to be sent, each referred to by a hash. Note: Don't expect that the peer necessarily give you all these blocks in a single message - you might have to re-request them.

### Example Packets

`0x22400891000000088400000043414243`

A Hello packet specifying the client id is "ABC".

Peer 1: `0x22400891000000028102`

Peer 2: `0x22400891000000028103`

A Ping and the returned Pong.


### Session Management

Upon connecting, all clients (i.e. both sides of the connection) must send a Hello message. Upon receiving the Hello message and verifying compatibility of the network and versions, a session is active and any other messages may be sent.

At any time, a Disconnect message may be sent.