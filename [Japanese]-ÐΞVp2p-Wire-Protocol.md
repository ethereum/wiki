Ethereum / Whisper 等を実行するノード間の p2p コミュニケーションは、既存の ÐΞV 技術や、[RLP](https://github.com/ethereum/wiki/wiki/RLP) のような標準規格を利用した wire-protocol によって管理されるよう設計されています。当ドキュメントはこのプロトコルを包括的に特定することを目的とします。

### Low-Level

ÐΞVp2p ノードは、RLPx という暗号化され認証をうけた transport protocol を使用してメッセージを送信することで、通信します。
peer は、どのTCPポート上でも自由に通信できますが、デフォルトでは、30303 番ポートを利用します。
TCP は、connection-oriented の媒体を提供するのに対し、ÐΞVp2p ノードは、パケット単位でコミュニケートします。
RLPx はパケットを送受信するための設備を提供します。RLPx に関して詳しくは [protocol specification](https://github.com/ethereum/devp2p/tree/master/rlpx.md) をご覧ください。 

ÐΞVp2p ノードは、RLPx discovery protocol DHT を介して peer を発見します。また、peer コネクションは、クライアント特有の RPC API へ、端点である peer を供給することによっても、初期化することが可能です。

### Payload Contents

以上のコネクションに対し、RLP 符号化のされた、数々の異なる payload「乗客」の type があります。
この ''type'' はいつも RLP の最初のエントリによって決定され、integer として解釈されます。

ÐΞVp2p は、基礎となる wire protocol 上に構築された任意のサブプロトコル ( _capabilities_ として知られたもの) をサポートする目的で設計されます。各サブプロトコルは、必要に応じ、message-ID 空間 の大きさ として与えられます（そのようなプロトコルはすべて、いくつ message-ID を必要とするのかを静的に特定しなければなりません。）
コネクションをし、`Hello` message を受信する間、双方の peer は、どのサブプロトコルを共有するのかについて同等の情報を保持し、
message-ID 空間 の合成の上で、合意形成を得ることができます。

message-ID は、0x10からはじまるもの (0x00-0x10 は ÐΞVp2p messages 用に予約されています) のコンパクト符号化とし、
アルファベット順に並んだ各共有サブプロトコル（同じ名前かつ同じバージョンのもの）に付与されます。
共有されないサブプロトコルは無視されます。もし、複数のバージョンが同じサブプロトコルとして共有されれば、数値の大きい方が勝ち、他は無視されます。


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