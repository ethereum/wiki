**NOTE:** *This is a work-in-progress*

Ethereum が成功ををおさめ、そして、Ethereum が達成されるという究極の目標のためには、Ethereum は数々のセキュアな分散データシステムを採り入れる必要がある。一般化されたチューリング完全性、拡張可能な状態をもつブロックチェーンは、この構成要素の一つですが、分散アプリケーション DApps を構築するのに、その潜在能力を十分に発揮するには、データシステム一式を付加してやる必要があります。各分散データシステムは特定の需要を解決します。一般にはどのデータシステムが究極的に必要とされるのかを予測するのは困難です。というのは非中央集約化のパラダイムはすぐに比較検討できるようなものでなく、従来の中央集約型設計のシステムとは似てもにつかわないものなのです。

データシステムには３つのタイプがあり、とりわけ、現行の大規模不特定多数のユーザアプリケーション（ MMA や MMORPGs のような web上モバイルアプリ ）で必要とされます。トランザクションのデータベース ( Ethereum ) に加え、出版およびダウンロードするためのシステムが、ひとつの一般化された低級「掲示板」に加えて、必要となることでしょう。

しかし、将来も含めてたくさんのそのようなタイプの分散コミュニケーションシステムを想像にえがくことができます。たとえば、identity-based RTC に対するものです。これらの各構成要素は数々の shared prerequisities を要します。 peer-discovery / recording , network will-formedness , transport-level buffering , scheduling / framing , authentication / security などです。これらのsaltに値するコンピュータサイエンティストは誰でも、手早く「抽象的な実戦の機会」をつかむことができます！  

### What it is

libp2p ( aka ÐΞVp2p ) は 軽量抽象レイヤ を提供することを目的とし、それにより低級アルゴリズムやある透明な枠組におけるプロトコルやサービスが、結果としてのプロトコルのユースケースを決定することなく、提供されます。

その特別な目的は、言語に依存しないAPIおよび仕様を提供することです。以下の要件を満たします。

- *Universal:* Pairwise addressing (ala Telehash), broadcast (ala Bitcoin), groupwise (some DHT designs & filesharing) はすべて道理をみたすものです。他のものもあるかもしれません。ネットワークの構造のみを提供するものであって、その上のレイヤの使用方法を記述するものであってはなりません。
- *Ubiquitous:* すべてのプロトコルで、同じ peer-set/-network が使えるべきです。
- *Secure:* 物理レイヤ上でのpeer間の暗号は一つあたえられています。Peer の導入 は systematic MitM 攻撃 にたいするよいレベルの防御を提供することが可能です。 
- *Efficient:* 各プロトコル上での QoS を保証するための枠組みと優先順位付け。 Framing and prioritisation to guarantee QoS over each protocol. 優先度を増すことによって、制限のあるリソースへのアクセスがp2p プロトコルの間で簡単に制御できるようになります。Multiplexing allows access to limited resources to be easily controlled between p2p protocols. Kademlia 方式のネットワークの堅牢性は、あるネットワークやグループにおけるどの peer に対する low maximum hope distance をも保証します。
Kademlia-style network well-formedness guarantees a low maximum hope distance to any peer in the network and its group.
- *Simple:* A minimal, developer-driven API gives source-level future-proofing.

究極的には、さらなる二次的特徴もまた、探されるであろう。
- *DPI security:* Framing と bandwidth 制御は、交通網の形を制御するために使用されうる。
- *Transport-layer Agnostic:* TCP/IP v4 と v6 は、初期のプロトコル仕様で与えられています。他は、後に、API を変更することなく付け加えると言う形で付与されます。複雑なネットワーキングデバイスであれば、究極的には、自らこれらを実装することが可能でしょう。

### Basic Design

- peer はそれぞれ node-ID によって識別される。
- 複数の peer とコミュニケートするの能力 *だけ* しか提供しない。
- すべては、型づけられ、順序づけられた datagram 上でのやりとりです。
- datagram の型はいくつでも登録可能です。handshakeの際に自動的に交渉がなされます。
- peer は libp2p の物理的端点を介して request されます
- peer は、ローカルな測度でもってプロトコル毎に位付けされることができます。
- peer の集合は、動的で、libp2p にとって内部で舵取りされますが、これはレーティングによるものです
- 最初のパケットのまとまりはDH key exchange または、もし node が peer 紹介を受けているものであれば、PKI暗号化された session key であるか、またもし node が以前の session から知られているものであれば、前の session key により発行されるあたらしい session key であるかです。優先される知識を用いて交渉がなされ、失敗すればリトライし、どんな信用もそれに相応するだけ排除します。
- peer-set は 複数のサブプロトコルの slot からつくられます。
- 各 slot は、その特定のサブプロトコルの評価レートを最大化するようにあたえられます。

はじめから決まったような（きまりきった）、「ノード間 message routing システム」はなく（これはある高レイヤのものとしてとってあります）、さらに高レイヤの個人認証システムもありません。
