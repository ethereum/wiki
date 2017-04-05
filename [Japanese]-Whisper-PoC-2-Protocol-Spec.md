この項では、 Whisper Wire Specification に加え、初期の proof-of-concept にたいし、Whisper protocol の全貌を詳細に記し、最終設計への
見通しを定めます。プロトタイプに磨きをかけるようにして、その枠組みの中で進化させたものです。
これにより、仕様に準拠した Whisper の実装を開発することができるでしょう。このドキュメントは、基礎となる仕様のみを与えることを目的とします。Whisper の実装へとつながる多くの側面はゲーム理論によるもので、仕様に置いて処方されているものがベストなものではなく、
むしろ個々の開発チームが選択できるようにしています。

### What Whisper Is (and Is Not)

Whisper は DHT と datagram メッセージシステム（例. UDP）両方の側面を合体させたものです。ですので、これら両方と似たものであったり、比較できるものであったりします。質量とエネルギーの双対関係に似ていなくもありません。（基礎的かつ美しい自然の原理に対する純粋な冒涜行為であることに関しては物理学者にお詫び申し上げます）

Whisper combines aspects of both DHTs and datagram messaging systems (e.g. UDP). As such it may be likened and compared to both, not dissimilar to the matter/energy duality (apologies to physicists for the blatant abuse of a fundamental and beautiful natural principle).

Whisper は、純粋な identity(身元) に基づいたメッセージシステムです。
Whisper は、低級の（アプリケーションを特定しない）ものですが、簡単にアクセス可能な API です。
これは、低級ハードウェアの要素および特性によって偏りがないような、とりわけシンギュラーな（特異な）端点をもちません、

Whisper is a pure identity-based messaging system. Whisper provides a low-level (non-application-specific) but easily-accessible API without being based upon or prejudiced by the low-level hardware attributes and characteristics, particularly the notion of singular endpoints.

Whisper は、言い換えると「 entry 毎に設定可能な TTL と、署名や暗号化の慣習（道具）を伴った、ひとつの DHT 」として捉えることができるのかもしれません。このような考え方で、Whisper は、複数 (のkey）からの参照をもつ、唯一のものとはかぎらない複数の entry を保持する能力を提供します。

Alternatively, Whisper may be likened to a DHT with a per-entry configurable TTL and conventions for the signing and encryption of values. In this sense, Whisper provides the ability to have multiply-indexable, non-unique entries (i.e. the same entry having multiple keys, some or all of which may be the same as other entries).

ですので、Whisper は、典型的なコミュニケーションのシステムとは違います。TCP/IP、UDP、HTTPや他の既存のプロトコルを置換しうるような設計ではありません。connection oriented のシステムを提供するためのものではなく、
単にネットワーク上の一組の端点間でデータをやりとりするものでもありません。
帯域を最大化したり、レイテンシを最小化したりする目的をもつようなものではありません。
（これらは、どの転送システムにおいても憂慮すべきものではありますが。）

As such, Whisper is not a typical communications system. It is not designed to replace or substitute TCP/IP, UDP, HTTP or any other traditional protocols; it is not designed to provide a connection oriented system, nor for simply delivering data betwixt a pair of specific network endpoints; it does not have a primary goal of maximising bandwidth or minimising latency (though as with any transmission system, these are concerns).

Whisper は、新しいパラダイム化におけるアプリケーション開発のために特別に設計された新しいプロトコルです。
それは徹底的に、効率的かつ容易な複製や発信をするために設計されています。同時に、低級の部分非同期コミュニケーションは一つの重要な目標です。価値の低い交通の低減あるいは後回しにすることがもう一つの目標です（これはQoSの向上を探訪するようなものです）。
それは、大規模かつたくさんのデータ探索、信号による交渉や、適切な転送を必要とする次世代型分散アプリケーション（ÐApp）を構築するときに用いる、必要最低限な構成かつ完全なプライバシーの合理的保証が期待される、建築資材となるように設計されています。

Whisper is a new protocol designed expressly for a new paradigm of application development. It is designed from the ground up for easy and efficient multi-casting and broadcasting. Similarly, low-level partially-asynchronous communications is an important goal. Low-value traffic reduction or retardation is another goal (which might also be likened to the quest for QoS). It is designed to be a building block in next generation ÐApps which require large-scale many-to-many data-discovery, signal negotiation and modest transmissions with an absolute minimum of fuss and the expectation that one has a very reasonable assurance of complete privacy.

### Pitch-Black Darkness

Whisper は、DAppコンテンツと究極的にはユーザアクティビティに関し,リークする情報の量をユーザが設定できる、という理念に基づいて動作します。情報のリークについて理解するには、単なる暗号化と、*darkness*(不透明化)を区別して考えることが重要です。
多くのプロトコルでは、これら双方の p2p まわりあるいは従来型サーバクライアント方式まわりの設計は、ある水準の暗号化を提供します。いくつかは、暗号化方式はプロトコルの本質的な部分で、単独で適用され、その根本的な要求に答えます。非中央集約化と暗号化は、倫理的な「ポスト・スノーデン期」のウェブを構築する大きな一歩であり、これははじまりにすぎません。

Whisper operates around the notion of being user-configurable with regard to how much information it leaks concerning the ÐApp content and ultimately, the user activities. To understand information leakage, it is important to distinguish between mere encryption, and *darkness*. Many protocols, both those designed around p2p and more traditional client/server models provide a level of encryption. For some, encryption forms an intrinsic part of the protocol and, applied alone, delivers its primary requirement. While decentralising and encrypting is a great start on building a legitimately "post-Snowden" Web, it is not the end.

暗号化通信においてでさえ、資金の潤沢な攻撃者は、個人のプライバシーを危険に晒すことが可能で、それはしばしばいとも簡単に行われます。
壊れたメタデータの集まりは、通信解読の新しい戦場となり、ビッグデータなどによる次世代型産業として公ではあるものの権威により、あるひとつのプライバシーへの関心は、重要視されなくなります。単純なサーバークライアント モデルでは、メタデータは通信するホストと共謀して裏切りを行うものです。
通信の内容がホストから大部分決定可能な状況において、プライバシーが漏れるということは、これは、しばしば十分なデータ量があるということです。
Even with encrypted communications, well-funded attackers are still able to compromise ones privacy, often quite easily. Bulk metadata collection becomes the new battleground, and is at once dismissed as a privacy concern by authorities yet trumpeted as the Next Big Thing by big-data outfits. In the case of a simple client/server model, metadata betrays with which hosts one communicates - this is often plenty enough to compromise privacy given that content is, in many cases, largely determinable from the host.

非中央集約型のシステムにおいて、例えば、一つの基本となるルートを持たない（non-routed）しかし暗号化されたVoIPあるいはTelehashコミュニケーションでは、ネットワークを監視することでの攻撃では転送の特定の内容を決定することはできないかもhしれませんが、プロバイダ（ISP）の管理するIPアドレスログの助けがあれば、誰と、いつ、どれくらいの頻度で、通信しているのかを決定することができます。
様々な法治国家におけるある種の法の適用に対し、これは十分に、関心に値するプライバシの欠如です。

With decentralised communications systems, e.g. a basic non-routed but encrypted VoIP call or Telehash communication, a network packet-sniffing attacker may not be able to determine the specific content of a transmission, but with the help of ISP IP address logs they would be able to determine to whom one communicated, when and how often. For certain types of applications in various jurisdictions, this is enough to be a concerning lack of privacy.

暗号化され、第三者のリレーノードを通して、パケットが転送される場合においてさえ、ある決まった（誰の目にも明らかな）転送情報の収集家であれば、ネットワーク定数の知識やネットワーク参加者が有限でしかないという事実を用いて、タイミングと帯域を選ぶことで、統計的攻撃をすることが可能です。（ノードは、同じ二つの peer の間で。極小のレイテンシでもってメッセージをやりとりする傾向にあるということや、同じリレーを介して通信しようとするノードのペアがすべてではないということなどのネットワークの知識があれば情報をあつめることができます。）
この種の攻撃を和らげる方法はあり、複数の第三者のリレーノードを使い、ランダムにそれらを切り替える、あるいは厳格な枠組みを利用することですが、双方ともに不完全なもので、致命的な非効率性へとつながりかねません。

Even with encryption and packet forwarding through a third relay node, there is still ample room for a determined bulk transmissions-collector to execute statistical attacks on timing and bandwidth, effectively using their knowledge of certain network invariants and the fact that only a finite amount of actors are involved (i.e. that nodes tend to forward messages between the same two peers with minimal latency and that there aren't all that many pairs of nodes that are trying to communicate via the same relay). There are ways to mitigate this attack vector, such as using multiple third-party relays and switching between them randomly or to use very strict framing, however both are imperfect and can lead to substantial inefficiencies.

真の dark システムとは、完全にメタデータの漏洩を防ぐものです。
その最大のセキュリティモードの操作において、Whisper は（帯域とレイテンシの相当な代償を得て）100%　dark な操作となります。
さらによいことに、これは、peer間の仲介ノード（背骨となるすべてのネットワークをつなぐデバイス）からのメタデータの集合に適用されるだけでなく、より大変な "100% - 2" 攻撃 でさえ、除外します。つまり、晒されたネットワーク上のすべてのノードが、他の誰にも知られずに通信したい人たちのためのDAppを走らせるノードの組を保護するものです。

A truly dark system is one that is utterly uncompromising in information leakage from metadata. At its most secure mode of operation, Whisper can (at a considerable cost of bandwidth and latency) deliver 100% dark operation. Even better, this applies not only for metadata collection from inter-peer conduits (i.e. backbone dragnet devices), but even against a much more arduous "100% - 2" attack; i.e. where every node in the network were compromised (though functional) save a pair running ÐApps for people that wanted to communicate without anybody else knowing.

### Routing and Lack Thereof
基礎的なところでは、少なくともコンピュータサイエンスの現況では、あらゆるシステムは決定的ルーティングと darkness とを天秤にかけています。
これらとWhisperとの違いのひとつはユーザーが、ルーティングの効率性とプライバシーとの天秤を設定することが出きるという事です、

Fundamentally, at least with the present state of computer science, all systems present a trade off between the efficiency of deterministic (and thus supposedly optimal) routing and darkness (or, put another way, routing privacy). One of Whisper's differences is in providing a user-configurable trade-off between ones routing privacy and ones routing efficiency.

最大限に dark となるように設定した状態では、Whisper ノードは全体として反応し、
データを受けとって、記録をし、情報転送の利便性を最大化するようにして、それらを伝播します。これらの情報の集まりは *topic* として知られるものを含みます。Topic は、ひとつのセキュアな確率論的ルーティングの端点、あるいは、ひとつの DHT multi-key の双方どちらとも捉えることができます

At its most dark, Whisper nodes are entirely reactive - they receive and record pieces of data and forward them trying to maximise the utility of information transmission to the peers. These pieces of information include what is known as a *topic*, which may be viewed both as a secure-probabilistic routing endpoint and/or a DHT multi-key.

しかし、Whisper は二つの方法を確率論的に利用してルーティングができるようにも設計されています。双方が極小ルートの情報を破棄する方法と、双方が大規模のメタデータの集合を利用した統計的攻撃に例外的に耐性をもつ方法です。

However, Whisper is also designed to be able to route probabilistically using two methods, both giving away minimal routing information and both being exceptionally resilient to statistical attacks from large-scale metadata collection.

ひとつめの方法は、ÐΞV-p2p バックエンドの機能上に構築します。
このバックエンドは、peer を評価し、時間軸上で役立つ情報を配達する peer の集合を確率論的に設定します。
究極的に、ネットワークが成長し、peer の集合が切り替えられるにつれて、この peer と役立つ情報を提供する他のすべての peer との ホップ数 は０に収束します。

The first builds on the functionality of the ÐΞV-p2p backend.
This backend provides the ability of Whisper to rate peers and, over time, probabilistically alter (or *steer*) its set of peers to those which tend to deliver useful (on-topic, timely, required for ones ÐApps to function) information.
Ultimately, as the network evolves and the peer-set is steered, the number of hops between this peer and any others that tend to be good conduits of useful information (be they the emitters or simply the well-positioned hubs) will tend to 0.

Peer の選定作業は、ノードが peer にとって役立つ情報を提供するインセンティブをも与えます。
パフォーマンスの悪い peer として認識され、他のノードと取り替えられるという恐怖は、
協力して、最も有用な情報を共有するインセンティブをすべてのノードに与えます。
ここで意味するところの有用とは、簡単にだれでも著作できるようなものではない、ことで、
（ここで proof of work が役に立ちます）、低い time-to-live と「有用であろう提供済みの他の情報にたいしても、性質の良い対等性をもつものです。

Peer steering also provides the incentive for nodes to provide useful information to peers.
The fear of being identified as an under-performing peer and thus being rotated out in favour of other nodes gives all nodes incentive to cooperate and share the most useful information.
Useful in this case means provably difficult to author (use of a proof-of-work function helps here); a low time-to-live; and well-corresponding to any provided information on what might be useful (read on for more).

ふたつめの方法はより、動的なものです。
ノードは、DApp にから、どの種の topic が有用であるかを知らされます。
そのとき、ノードは、その topic を記述する各 peer に対し、宣伝することを許可されます。
topic は、マスクや Bloom filter を用いて、記述されるかもしれん。ですので、完全な方法とはいかないまでも、漏洩する情報を減らし、passove statistical 攻撃をすべて遂行困難にします。
peer に与える情報量の決定は、ユーザが明示的に設定するか、topic/traffic モデルに基づいて行うかの双方です。「このpeer からの情報と、全トラフィックからの情報分散した私のモデルが与えられたとして、わたしは、期待しただけの価値ある情報量をえることができるのか？もし、得られるならば、情報の負荷によって、それの占める場所を狭めることが必要かもしれません。」

The second is more dynamic.
Nodes are informed by their ÐApps over what sort of topics are useful.
Nodes are then allowed to advertise to each peer describing these topics. Topics may be described, using masks or Bloom filters, and thus in an incomplete manner, to reduce the amount of information leaked and make passive statistical attacks arbitrarily difficult to execute.
The determination of the amount of information to give to peers is both made through an explicit setting from the user and through topic/traffic modelling: "Given the information coming from this peer and my models of information distribution made from total traffic, am I receiving the amount of useful valuable information that I would expect to receive? If so, narrowing it down with additional description information to the peer may be warranted."

これらの設定は peer 毎、DApp 毎に設定することさえ可能です。
proof of work アルゴリズムを最大限に利用し、DOS attack と everything-but 攻撃の方法による変化を最小化します。

These settings can even be configured per-peer (more trusted/longer-lasting peers may be afforded greater amounts of information), and per-ÐApp (those ÐApps that may be more sensitive could be afforded less advertising than others). We can also make use of proof-of-work algorithms to minimise the changes of both DoS attacks and 'everything-but' attacks (where a peer is flooded with almost-useful information prompting it to give away more about its true requirements than it would do otherwise).

Bloom filter / mask を結合し、減らすことで、weaker Nth-level の情報が、peer に関心あるものを提供されることが可能で、確率論的にノードのあたりでtopicを吸収する渦を生成し、
topic　空間の重力が弱まって、関心をもつどのpeerからもnetwork のホップ数で遠ざけられたものはほぼさだかではありません。

Through combining and reducing the Blooms/masks, weaker Nth-level information can be provided to peers about their peers' interests, forming a probabilistic topic-reception vortex around nodes, the "topic-space" gravity-well getting weaker and less certain the farther away with the network hop distance from any interested peers.
## Basic Protocol Elements

### Envelopes

There are two key concepts in Whisper: *Envelopes* and *Messages*. Envelopes may be considered items should Whisper be considered a DHT. Should Whisper be considered a datagram messaging system then envelopes become the packet format which house the potentially encrypted datagrams. Envelopes are necessarily comprehensible by any node (i.e. they themselves are unencrypted).

They contain information to the entry/datagram such as the original time to live (*TTL*), the absolute time for *expiry* and, importantly, the *topics*. Topics are a set of indexes to this item, recorded in order to help an interested party ("recipient") find it or have it routed to them. They might be likened to binary tags or keywords. Envelopes additionally contain a nonce allowing the original insertion node ("sender") to prove that some arbitrary amount of work was done in its composition. Finally, envelopes contain a message-*data* field; this stores the actual payload, together with some information and potentially a signature; together this forms a message. This field may be encrypted.

Envelopes are transmitted as RLP-encoded structures. The precise definition is given by:

[`expiry`: `P`, `ttl`: `P`, [`topic0`: `B_4`, `topic1`: `B_4`, ...], `data`: `B`, `nonce`: `P`]

Here, `ttl` is given in seconds, `expiry` is the Unix time of the intended expiry date/time for the envelope. Following this point in time the envelope should no longer be transmitted (or stored, unless there is some extenuating circumstance). Prior to this point in time less the `ttl` (i.e. the implied insertion time), the envelope is considered utterly invalid and should be dropped immediately and the transmitting peer punished.

`nonce` is an arbitrary value. We say the work proved through the value of the SHA3 of the concatenation of the nonce and the SHA3 of the RLP of the packet save the nonce (i.e. just a 4-item RLP list), each of the components as a fixed-length 256-bit hash. When this final hash is interpreted as a BE-encoded value, the smaller it is, the higher the work proved. This is used later to judge a peer.

### Topics

Topics are cryptographically secure, probabilistic partial-classifications of the message. Each topic in the set (order is unimportant) is determined as the first (left) 4 bytes of the SHA3-256 hash of some arbitrary data given by the original author of the message. These might e.g. correspond to "twitter" hash-tags or an intended recipient's public key hashed with some session nonce or application-identity.

Four bytes was chosen to minimise space should a large number of topics be mentioned while still keeping a sufficiently large space to avoid large-scale topic-collision (though it may yet be reviewed and possibly made dynamic in later revisions of the protocol).

### Messages

A message is formed as the concatenation of a single byte for flags (at present only a single flag is used), followed by any additional data (as stipulated by the flags) and finally the actual payload. This series of bytes is what forms the `data` item of the envelope and is always encrypted.

In the present protocol version, no explicit authentication token is given to indicate that the data field is encrypted; any would-be readers of the message must know ahead of time, through the choice of topic that they have specifically filtered for, that the message is encrypted with a particular key. This is likely to be altered in a further PoC to include a MAC.

Any determination that the message is indeed from a particular sender is left for a higher-level to address. This is noted through the Javascript API allowing the `to` parameter to be passed only at the point of specifying the filter. Since the signature is a part of the message and not outside in the envelope, those unable to decrypt the message data are also unable to access any signature.

- `flags`: 1 byte
- (`signature`: 65 bytes)
- `payload`: not fixed

Bit 0 of the flags determines whether the signature exists. All other bits are not yet given a purpose and should be set randomly. A message is invalid if bit 0 is set but the total data is less than 66 bytes (since this wouldn't allow it to contain a signature).

Payloads are encrypted in one of two ways. If the message has a specific recipient, then by using ECIES with the specific recipient's SECP-256k1 public key. If the message has no recipient, then by AES-256 with a randomly generated key. This key is then XORed with each of the *full* topics to form a salted topic. Each salted topic is stored prior to the encrypted data *in the same order as the corresponding topics are in the envelope header*.

As a recipient, payloads are decrypted in one of two ways. Through use of topics, it should be known whether the envelope is encrypted to a specific recipient (in which case use the private key to decrypt) or to a general multicast audience. In the latter case, we assume that at least one topic is known (since otherwise, the envelope could not be properly "identified"). In this case, we match the known *full* topic to one of the abridged topics in the envelope, determine the index and de-salt the according salted-key at the beginning of the data segment in order to retrieve the final key.

Encryption using the full topic with "routing" using the abridged topic ensures that nodes which are merely transiently storing the message and have no interest in the contents (thus have access only to routing information via the abridged topics) have no intrinsic ability to read the content of the message.

The signature, if provided, is the SHA3-256 hash of the unencrypted payload signed using ECDSA with the insertion-identity's secret key.

The signature portion is formed as the concatenation of the *r*, *s* and *v* parameters of the SECP-256k1 ECDSA signature, in that order. `v` is non-normalised and should be either 27 or 28. `r` and `s` are both big-endian encoded, fixed-width 32-byte unsigned.

The payload is otherwise unformatted binary data.

In the Javascript API, the distinction between envelopes and messages is blurred. This is because DApps should know nothing about envelopes whose message cannot be inspected; the fact that nodes pass envelopes around regardless of their ability to decode the message (or indeed their interest in it at all) is an important component in Whisper's dark communications strategy.

### Basic Operation

Nodes are expected to receive and send envelopes continuously, as per the [protocol specification](https://github.com/ethereum/wiki/wiki/Whisper-Wire-Protocol). They should maintain a map of envelopes, indexed by expiry time, and prune accordingly. They should also efficiently deliver messages to the front-end API through maintaining mappings between topics and envelopes.

When a node's envelope memory becomes exhausted, a node may drop envelopes it considers unimportant or unlikely to please its peers. Nodes should consider peers good that pass them envelopes with low TTLs and high proofs-of-work. Nodes should consider peers bad that pass then expired envelopes or, worse, those that have an implied insertion time prior to the present.

Nodes should always keep messages that its ÐApps have created. Though not in PoC-1, later editions of this protocol may allow ÐApps to mark messages as being "archived" and these should be stored and made available for additional time.

Nodes should retain a set of per-ÐApp topics it is interested in.

### Inserting (Authoring) Messages

To insert a message, little more is needed than to place the envelope containing it in the node's envelope set that it maintains; the node should, according to its normal heuristics retransmit the envelope in due course. Composing an envelope from a basic payload, possible identities for authoring and access, a number of topics, a time-to-live and some parameters concerning work-proving targets is done though a few steps:

- Compose `data` through concatenating the relevant flag byte, a signature of the payload if the user specified a valid author identity, and the user-given payload.
- Encrypt the data if an access ("destination") identity's public key is given by the user.
- Compose `topics` from the first 4 bytes of the SHA3 of each topic.
- Set user-given attribute `ttl`.
- Set the `expiry` as the present Unix time plus the time-to-live.
- Set the `nonce` as that which provides the most work proved as per the previous definition, after some fixed amount of time of cycling through candidates or after a candidate surpasses some boundary; either should be given by the user.

### Topic Masking and Advertising

Nodes can advertise their topics of interest to each other. For that purpose they use a special type of Whisper message (TopicFilterPacket). The size of Bloom Filter they send to each other must be 64 bytes. Subsequently the rating system will be introduced -- peers sending useful messages will be rated higher then those sending random messages. 

A message matches the bloom filter, if any one of the topics in this message, converted to the Whisper bloom hash, will match the bloom filter. 

Whisper bloom function accepts AbridgedTopic as a parameter (size: 4 bytes), and produces a 64-byte hash, where three bits (at the most) are set to one, and the rest are set to zeros, according to the following algorithm:

0. Set all the bits in the resulting 64-byte hash to zero.

1. We take 9 bits form the AbridgedTopic, and convert to integer value (range: 0 - 511).

2. This value defines the index of the bit in the resulting 512-bit hash, which should be set to one.

3. Repeat steps 1 & 2 for the second and third bit to be set in the resulting hash.

Thus, in order to produce the bloom, we use 27 bits out of 32 in the AbridgedTopic. For more details, please see the implementation of the function: TopicBloomFilterBase<N>::bloom() [libwhisper/BloomFilter.h].

### Coming changes

Also being considered for is support for plausible deniability through the use of session keys and a formalisation of the multicast mechanism.