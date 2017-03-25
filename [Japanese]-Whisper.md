一言で述べると、whisper は DApp (分散型アプリケーション) が互いに通信するための通信プロトコルです。

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

* **Low-level**: API は DApp が利用するもので、ユーザーが利用するものではありません。
* **Low-bandwidth** 大量のデータ転送のための設計ではありません。
* **Uncertain-latency** RTCのための設計ではありません。
* **Dark** パケットのトラッキングに利用するものではありません。 
* 典型的な使用方法:
  * Low-latency, 1-1 or 1-N signalling messages.
  * High latency, high TTL 1-* publication messages.

Message は 64K bytes より小さく、典型的なものとしては約 256 bytes です。

### Existing solutions

* UDP: API レベルで類似している。本質的に multicast するための仕様。TTL や、security、privacy safeguard といった仕様はない。
* [0MQ](http://zeromq.org/): 分散メッセージシステムの一つ。privacy safeguard は内在しない。
* [Bitmessage](https://bitmessage.org/wiki/Main_Page): これは、P2P ネットワークが秘密の会話をするのに公開鍵暗号書名を基準に、メッセージをやりとりするという、基本的な方法と似ています。より高いレベルでは（ e-mail の代わりとなりますが、ただし数千ものメールをやりとりする場合のみ ）、TTL が固定され、通信データを optimise する機能はありません。これを利用する動機付けは不明瞭です。
* [TeleHash](https://github.com/telehash/telehash.org/blob/master/network.md#paths): secure な connection-oriented RTC comms（匿名のコミュニケーション） です。BitTorrent (uses modified Kademila tech) と方法が似ていますが、与えられたハッシュに対して peer をみつけるというよりかはむしろ、ハッシュ値が付与された受信者を辿ります。DHT を使用して、決定論的 routing を行います。そのため、大規模攻撃者とは性質の異なる、シンプルな統計的パケット解析攻撃に対してのセキュリティはありません。
connection oriented であるため、TTL や非同期データ発信のための設計はありません。
* [Tox](https://github.com/irungentoo/toxcore/blob/master/docs/updates/DHT.md): Higher-level (IM & AV chat) replacement.

### Basic Design

Uses the `"shh"` protocol string of ÐΞVP2P.

Rest coming soon, once I've finished prototyping. Gav.

### Considerations for Defeating Traffic Analysis
(from lokiverloren) All existing protocols for location obscured instant messaging have complicated problems to do with routing. 

The Bitmessage protocol propagates messages blindly across the network, and the proper recipient knows how to decrypt it and receives it (just like everyone else) but then stores it and lets its' user know it's got a new message. The problem with this of course is that it greatly increases the exposure of the whole network to a body of encrypted material that ideally should not be easily accessed at all.

None of the others listed above particularly have any means to hide the source and destination of messages. I believe it is one of the core objectives of the Whisper protocol to hide location of sender and receiver and in transit, make it difficult if not impossible to establish one, the other or both.

The Tor system has a protocol for enabling connections between two nodes without either knowing where the other is, it is the Rendezvous protocol used for hidden services. Most readers of this will already understand how it works, but what happens is that a hidden service (the equivalent of a server listening on a port in the TCP/IP protocol) selects at random a number (I think, usually 6) 'introducer' nodes. In order to do this it establishes the standard 3-hop chain to each of the introducers, and when a user wants to establish a connection with a hidden service, they propagate a request to connect with hidden service that is associated with a particular public key. I'm not sure about how this request is propagated, obviously it would also have to be done through a circuit or the rendezvous introducer node knows who might be about to establish a circuit. Once one's client knows a valid rendezvous node it then establishes a connection to it and requests to have traffic relayed through its' circuit to the hidden service.

I think there really isn't a better way to implement 'dark' parts of the connection protocol, because there has to be a different identity for a relay node as a client node, the same principle applies in Ethereum. However, using the rendezvous, it again makes possible something for connections between nodes that know each other (though they don't know the account or identity of the initiator) something other than the uniform 3 hop circuit. For generating a circuit to an exit node in Tor, it is of no danger that your client knows the identity of the relays in each hop along the way, but to do that *within* the network compromises location obfuscation security, tying your ethereum identity with your router identity (I think it may need to be explicitly pointed out that routing function is something that Ethereum nodes will perform).  When connecting to a rendezvous, you do not thereby reveal your location to the rendezvous, and the rendezvous does not know the location of the hidden service either, and establishing these connections only requires a public directory to be created for routers. It is not consequential information for an attacker and can be revealed directly by probing for a relevant server at your IP addresses anyway.

The contribution I would like to make to the discussion is this: 

1. It is implied that the whisper protocol has to work through a system of location obfuscation relays like Tor. Not only this, but it must therefore also be using some form of the rendezvous protocol used in Tor for hidden services.

2. However, as Tor was originally designed first to be a way to stop webservers logging your IP address in association with a session cookie and record, then secondarily added the ability to implement the equivalent of TCP/IP routing (listening ports) within the context of location obfuscation in the form of the rendezvous protocol, it is an artifact, a legacy of the first purpose that leads to the uniform utilisation of 3 hop nodes and connections that remain open rather than a session (connectionless) protocol like used in HTTP. What I suggest is that, depending on the needs of a particular connection, there can be occasion to mix up the obfuscation process so that it is further obscured from traffic analysis in the case of malicious nodes performing data gathering for an attacker. 

Instead of a connection like is normal between a browser and web server, for example, which is usually left open because of the latency of reestablishing such a connection, to make further requests, instead there can be a session cookie, and then you can alter the way your client sends data through the 'connection' to a hidden node. The connection, between two routing nodes, could be direct, 1 proxy intermediary, 2, or 3, it could use shared secrets instead and route fragments of datagrams across multiple heteregenous circuits, as well, and the receiver would then wait for sufficient fragments to assemble the original packets. Indeed, it could be possible in the case of (not quite related to whisper, but to the distributed data storage protocol) larger streams, break the stream up into parts and alter the obfuscation method through the process, further confusing the traffic analysis data.

The same considerations apply, fundamentally, the only differences have to do with the size of the data being transmitted, whether it's for a messaging system or distributed filesystem. The other criteria for deciding how to scramble the routing is latency. For some purposes one wants lower latency, and other purposes, greater security is vitally important. When in the process streams are fragmented into parts, it can also increase security to apply an All Or Nothing Transform to the entire package, then if part is intercepted but not the complete message, it is impossible to assemble the data, not even for cryptanalysis purposes.