**NOTE:** *This is a work-in-progress*

Ethereum が成功ををおさめるために、そして、ethereum が達成されるという究極の目標のためには、Ethereum は多くのセキュアな分散型データシステムを採用する必要がある。一般化されたチューリング完全性、拡張可能な状態をもつブロックチェーンはこの構成要素の一つですが、その潜在能力を十分に発揮し、分散アプリケーション DApps を構築するには、データシステム一式を付け加える必要があります。各分散データシステムは特定の需要を解決します。一般にはどのデータシステムが究極的に必要とされるのかを予測するのは困難です、というのは非中央集約化のパラダイムはすぐに比較可能でなく、今までの中央集約型設計のシステムとは似てもにつかわないものなのです。

データシステムには３種類あって、とりわけ現行の大規模不特定多数のユーザアプリケーション（MMAやMMORPGsのようなweb上モバイルアプリ）で必要とされます。トランザクションのデータベース(Ethereum) に加え、出版およびダウンロードするためのシステムが、ひとつの一般化された低級「掲示板」に加えて、必要となることでしょう。

しかし、将来も含めてたくさんのそのようなタイプの分散コミュニケーションシステムを想像にえがくことができます。たとえば、identity-based RTC に対するものです。これらの各構成要素は数々の shared prerequisities を要します。peer-discovery/recoiding, network will-formedness, transport-level buffering, scheduling/framing, authentication/security などです。これらのsaltに値するコンピュータサイエンティストは誰でも、手早く「抽象的な実戦の機会」をつかむことができます！  

### What it is

libp2p (aka ÐΞVp2p) は 軽量抽象レイヤ を提供することを目的とし、それにより低級アルゴリズムやある透明な枠組におけるプロトコルやサービスが、結果としてのプロトコルのユースケースを決定することなく、提供されます。

その特別な目的は、言語に依存しないAPIおよび仕様を提供することです。以下の要件を満たします。
- *Universal:* Pairwise addressing (ala Telehash), broadcast (ala Bitcoin), groupwise (some DHT designs & filesharing) are all reasonable. There may be others. It should provide only the network structure, not dictate usage over it.
- *Ubiquitous:* The same peer-set/-network should be able to be used for all protocols.
- *Secure:* Encryption between physical peers is a given. Peer introduction can provide a good level of defence against systematic MitM attacks.
- *Efficient:* Framing and prioritisation to guarantee QoS over each protocol. Multiplexing allows access to limited resources to be easily controlled between p2p protocols. Kademlia-style network well-formedness guarantees a low maximum hope distance to any peer in the network and its group.
- *Simple:* A minimal, developer-driven API gives source-level future-proofing.

Ultimately, additional secondary features will also be explored:
- *DPI security:* Framing and bandwidth control can be used to control traffic shape.
- *Transport-layer Agnostic:* TCP/IP v4 and v6 are provided with initial protocol specifications. Others should be able to be added on at later dates without altering the API. Mesh networking devices could even ultimately implement this natively.

### Basic Design

- Peers can each be identified by a node-ID.
- Provides ability *only* to communicate with peers.
- Everything happens in typed, ordered datagrams.
- Any number of datagram types can be registered - these are automatically negotiated at the handshake.
- Peers can be requested via a libp2p physical endpoint.
- Peers can be rated per-protocol using a local metric.
- Peer set is dynamic and "steered" by libp2p internally, going from ratings.
- First packets are either DH key exchange or, if node known from peer introduction, PKI-encrypted session key, or if node known from previous session, new session key encrypted by old. Retry can be made after failed negotiation using prior knowledge, with the corresponding removal of any trust.
- Peer-set is made up from multiple sub-protocol slots.
- Each slot is given over to maximise rating for that particular sub-protocol.

There is no preordained inter-node message routing system (this is left to a higher-level), and no preordained high-level identity system (again, higher level).
