まずはじめに、whisper は DApp (分散型アプリケーション) が互いに通信するための通信プロトコルです。

# Use case

* DApp の中で、互いに少量の情報を掲示し、一定時間情報を保持するもの。
例えば、分散型通貨取引所 DApp では、あるレートで通貨を売って欲しいという要求を記録するためにこれを利用します。
この場合では、何十分何日間と続くかもしれません。オファーとは、取引を締結するものではなく、潜在する取引を開始するただの道標です。

* DApp の中で、ある取引において、（究極的には）協力するためにお互いに信号を送り合う必要があるようなもの。
例として、分散型通貨取引所 DApp は、取引所で作成する一つの取引（取引に依っては２つ）よりも優先するオファーの位置決めをするために、これを利用する必要があります。

* DApp の中で、リアルタイムでない僅かな情報もしくは普通の相互会話を提供するようなもの。
例として、小さなチャット部屋があります。

* DApp の中で、ブラックな（ネットワーク全解析で得た尤もらしい情報をもとに得た「否定」に関する（密告などの））コミュニケーションを、ハッシュ値以外の互いの情報を知らない二人ができるようにするもの。これは、例えば、内部告発者が著名なジャーナリストとコミュニケーションし、少量の確認材料を交換することで、その本体（密告文、密告データなど）を取り扱うために別のプロトコルへ移動するお膳立てをするような、DApp が考えられるでしょう。

一般的には、取引（成果物となる文書は除きます）や、過去の言及に遡る必要があるもの、あるいは自動執行と状態変化、といったものを考えればよいでしょう。


### Specs

* **Low-level**: API は DApp が利用するもので、ユーザーが利用するものではない。
* **Low-bandwidth** 大量のデータ転送のための設計ではない。
* **Uncertain-latency** RTCのための設計ではない。
* **Dark** パケットのトラッキングに利用するものではない。 
* 典型的な使用方法:
  * Low-latency, 1-1 or 1-N signalling messages.
  * High latency, high TTL 1-* publication messages.

Message は 64K bytes より小さく、典型的なものとしては約 256 bytes です。

### Existing solutions

* UDP: API レベルで類似している。本質的に multicast するための仕様。TTL や、security、privacy safeguard といった仕様はない。
* [0MQ](http://zeromq.org/): 分散メッセージシステムの一つ。privacy safeguard は内在しない。
* [Bitmessage](https://bitmessage.org/wiki/Main_Page): これは、P2P ネットワークが秘密の会話をするのに公開鍵暗号書名を基準に、メッセージをやりとりするという、基本的な方法と似ている。より高いレイヤでは（ e-mail の代わりとなりますが、ただし数千ものメールをやりとりする場合のみ ）、TTL が固定され、通信データを optimise する機能はない。実際にこれを利用する動機付けとなるものは、不明瞭である。
* [TeleHash](https://github.com/telehash/telehash.org/blob/master/network.md#paths): secure な connection-oriented RTC comms（匿名のコミュニケーション） です。BitTorrent (uses modified Kademila tech) と方法が似ているが、与えられたハッシュに対して peer をみつけるというよりかはむしろ、ハッシュ値が付与された受信者を辿る。DHT を使用して、決定性 routing を行う。そのため、大規模攻撃者とは性質の異なる、シンプルな統計的パケット解析攻撃に対してのセキュリティはない。
connection oriented であるため、TTL や非同期のデータ発信のための設計はない。
* [Tox](https://github.com/irungentoo/toxcore/blob/master/docs/updates/DHT.md): Higher-level (IM & AV chat) replacement.

### Basic Design

Uses the `"shh"` protocol string of ÐΞVP2P.

Rest coming soon, once I've finished prototyping. Gav.


<hr>

以下、英語構文が厳格でなく、翻訳を中断しています

### Considerations for Defeating Traffic Analysis


(from lokiverloren) 今あるどの、匿名メッセージシステム (location obscured instant messaging) ににおいても、routing を行うのに複雑な問題があります。

Bitmassage プロトコルは、メッセージをネットワーク上で、見えないようにして伝播し、その適切な受信者は、復号化の方法を知っており、（皆が受信するように）メッセージを受信します。しかし、その時、それを保存し、ユーザー（人）に対し、メッセージを受信したことを知らせます。
この方法での問題は、もちろん、一般的考え方として、簡単にアクセスされるべきでないような、暗号化されたメッセージを全ネットワークに対しにさらしてしまうということです。

上に上げているものでその他のものは全て、とりわけ、メッセージの発信地と到達地を隠すためのどんな手段も持ち合わせていません。
私は、送信者、受信者の所在を隠し、転送中に、受信者か送信者、もしくは両方の特定をすることができなくなるようにすることこそが、Whisper protocol の核となる題材であると、考えています。

Tor system は、二つのノードの間で、たとえ他方の所在がわからなくとも、コネクションを設営することができ、その裏では、Rendezvous protocol が使用されています。その仕組みは、すでにご存知の方も多いと思われますが、そこで起こることは、ある隠れたサービス（TCP/IP protocol に置ける port で待機するサーバと同等のもの）が、ランダムに一つの数字を選び、（たいては6だったと思います）すなわち6つの 'introducer' node  を選びます。


Most readers of this will already understand how it works, but what happens is that a hidden service (the equivalent of a server listening on a port in the TCP/IP protocol) selects at random a number (I think, usually 6) 'introducer' nodes. In order to do this it establishes the standard 3-hop chain to each of the introducers, and when a user wants to establish a connection with a hidden service, they propagate a request to connect with hidden service that is associated with a particular public key. I'm not sure about how this request is propagated, obviously it would also have to be done through a circuit or the rendezvous introducer node knows who might be about to establish a circuit. Once one's client knows a valid rendezvous node it then establishes a connection to it and requests to have traffic relayed through its' circuit to the hidden service.

I think there really isn't a better way to implement 'dark' parts of the connection protocol, because there has to be a different identity for a relay node as a client node, the same principle applies in Ethereum. However, using the rendezvous, it again makes possible something for connections between nodes that know each other (though they don't know the account or identity of the initiator) something other than the uniform 3 hop circuit. For generating a circuit to an exit node in Tor, it is of no danger that your client knows the identity of the relays in each hop along the way, but to do that *within* the network compromises location obfuscation security, tying your ethereum identity with your router identity (I think it may need to be explicitly pointed out that routing function is something that Ethereum nodes will perform).  When connecting to a rendezvous, you do not thereby reveal your location to the rendezvous, and the rendezvous does not know the location of the hidden service either, and establishing these connections only requires a public directory to be created for routers. It is not consequential information for an attacker and can be revealed directly by probing for a relevant server at your IP addresses anyway.

The contribution I would like to make to the discussion is this: 

1. It is implied that the whisper protocol has to work through a system of location obfuscation relays like Tor. Not only this, but it must therefore also be using some form of the rendezvous protocol used in Tor for hidden services.

2. However, as Tor was originally designed first to be a way to stop webservers logging your IP address in association with a session cookie and record, then secondarily added the ability to implement the equivalent of TCP/IP routing (listening ports) within the context of location obfuscation in the form of the rendezvous protocol, it is an artifact, a legacy of the first purpose that leads to the uniform utilisation of 3 hop nodes and connections that remain open rather than a session (connectionless) protocol like used in HTTP. What I suggest is that, depending on the needs of a particular connection, there can be occasion to mix up the obfuscation process so that it is further obscured from traffic analysis in the case of malicious nodes performing data gathering for an attacker. 

Instead of a connection like is normal between a browser and web server, for example, which is usually left open because of the latency of reestablishing such a connection, to make further requests, instead there can be a session cookie, and then you can alter the way your client sends data through the 'connection' to a hidden node. The connection, between two routing nodes, could be direct, 1 proxy intermediary, 2, or 3, it could use shared secrets instead and route fragments of datagrams across multiple heteregenous circuits, as well, and the receiver would then wait for sufficient fragments to assemble the original packets. Indeed, it could be possible in the case of (not quite related to whisper, but to the distributed data storage protocol) larger streams, break the stream up into parts and alter the obfuscation method through the process, further confusing the traffic analysis data.

The same considerations apply, fundamentally, the only differences have to do with the size of the data being transmitted, whether it's for a messaging system or distributed filesystem. The other criteria for deciding how to scramble the routing is latency. For some purposes one wants lower latency, and other purposes, greater security is vitally important. When in the process streams are fragmented into parts, it can also increase security to apply an All Or Nothing Transform to the entire package, then if part is intercepted but not the complete message, it is impossible to assemble the data, not even for cryptanalysis purposes.