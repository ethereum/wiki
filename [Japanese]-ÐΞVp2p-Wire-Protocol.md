Ethereum / Whisper 等を実行するノード間の p2p コミュニケーションは、既存の ÐΞV テクノロジーや、[RLP](https://github.com/ethereum/wiki/wiki/RLP) のような標準規格を利用した wire-protocol によって管理されるよう設計されています。当ドキュメントはこのプロトコルを包括的に特定することを目的とします。

### Low-Level

ÐΞVp2p ノードは、RLPx という暗号化され認証をうけた transport protocol を使用してメッセージを送信することで、通信します。
peer は、どのTCPポート上でも自由に通信できますが、デフォルトでは、30303 番ポートを利用します。
TCP は、connection-oriented の媒体を提供するのに対し、ÐΞVp2p ノードは、パケット単位でコミュニケートします。
RLPx はパケットを送受信するための設備を提供します。RLPx に関して詳しくは [protocol specification](https://github.com/ethereum/devp2p/tree/master/rlpx.md) をご覧ください。 

ÐΞVp2p ノードは、RLPx discovery protocol DHT を介して peer を発見します。また、peer コネクションは、クライアント特有の RPC API へ、端点である peer を供給することによっても、初期化することが可能です。

### Payload Contents

コネクションに対し、RLP 符号化のされた、数々の異なる payload「乗客」の type があります。
この ''type'' はいつも RLP の最初のエントリによって決定され、integer として解釈されます。

ÐΞVp2p は、基礎となる wire protocol 上に構築された任意のサブプロトコル ( _capabilities_ として知られたもの) をサポートする目的で設計されます。各サブプロトコルは、必要に応じ、message-ID 空間 の大きさ として与えられます（そのようなプロトコルはすべて、いくつ message-ID を必要とするのかを静的に特定しなければなりません。）
コネクションをし、`Hello` message を受信する間、双方の peer は、どのサブプロトコルを共有するのかについて同等の情報を保持し、
message-ID 空間 の合成の上で、合意形成を得ることができます。

message-ID は、0x10からはじまるもの (0x00-0x10 は ÐΞVp2p messages 用に予約されています) のコンパクト符号化とし、
アルファベット順に並んだ各共有サブプロトコル（同じ名前かつ同じバージョンのもの）に付与されます。
共有されないサブプロトコルは無視されます。もし、複数のバージョンが同じサブプロトコルとして共有されれば、数値の大きい方が勝ち、他は無視されます。


### P2P

**Hello**
`0x00` [`p2pVersion`: `P`, `clientId`: `B`, [[`cap1`: `B_3`, `capVersion1`: `P`], [`cap2`: `B_3`, `capVersion2`: `P`], `...`], `listenPort`: `P`, `nodeId`: `B_64`] コネクション上で最初に送られるパケットで、双方から一度だけ送信されます。Hello が受信されるまで、ほかのメッセージは一切送信されないでしょう。
* `p2pVersion` Specifies the implemented version of the P2P protocol. Now must be 1.
* `clientId` は、クライアントソフトウェアの個体番号で、人が読みやすいように string としています。 (e.g. "Ethereum(++)/1.0.0").
* `cap` は peer の capability (装備) の名前を特定するもので、長さ3 の ASCII string です。現在サポートされているものとしては、`eth`, `shh` があります。
* `capVersion` は peer の capability (装備) のバージョンを特定するもので、正の integer です。現在サポートされているバージョンは、`eth` の 34 および、 `shh` の 1 です。
* `listenPort` は、クライアントが listen (待機) するポートを特定します。（現在のコネクションのインターフェース上にのったものの中から選びます）もし、0 であれば、クライアントは listen (待機) していません。
* `nodeId` はノードの個体認証で、512-bit のハッシュ値を特定し、ノードを識別します。

**Disconnect**
`0x01` [`reason`: `P`] peer に対し、disconnection が執行されることを知らせます。; 受信されれば、直ちに peer は disconnect するのがよいでしょう。送信のとき、行儀のよいホストは、つながっている複数の peer に対して、disconnect するための相手の機会 (read: wait 2 seconds) を与えてから、自身を disconnect するものです。
* `reason` は、オプショナルの integer で、disconnect の理由を次の中から一つ選びます:
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

あるひとつの ÐΞVp2p node の個体認証は、あるひとつの secp256k1 public key です。　

Node は自由に、与えられた複数の ID に対する評価レートを保存し、それに応じたパフォーマンスを与えることができます。
Node は、man-in-the-middle 攻撃のポテンシャルを図るために、node ID をトラッキングしているかもしれません。
クライアントは自由に、新しい node を書き記したり、node の評判を決定する手段として、node ID を使ったりすることが可能です。

### Session Management

コネクション時、すべてのクライアント（つまり、コネクションの双方）は、`Hello` メッセージを送信しなければなりません。
`Hello` メッセージ受信するときは、ネットワークとの親和性とバージョンを検証し、セッションがアクティブとなり、任意の P2P メッセージが送信されます。

いつ何時でも、Disconnect メッセージは送信されます。
