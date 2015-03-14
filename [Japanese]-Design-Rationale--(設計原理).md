Ethereum が既に試されテストされてきた5年前からのビットコインのような古くからの暗号通貨の様々なアイディアを借りて出来てきたとしても、
Ethereumには現在のプロトコルの機能を司るための大半の一般的な方法から分岐した様々な点がある。
それはEthereumが開発しなければならなかった全くもって新しい経済的なアプローチにおいての多くの状況も有る。
何故ならば、既存の他のシステムによっては提案されてこなかった機能が提供されているのだから。

この文書の目的は潜在的に自明ではない全てのことや、
もしくはその他のケースでEthereumのプロトコルを創りあげていく経過において、
議論の的となる物事に対して為されてきた決定についての詳細を書き、
同様に我々のアプローチと、その他の代替手段の可能性におけるリスクを示すことである。

##原理
Ethereumのプロトコルのデザインは幾つかの原理に基づいている。

1. **Sandwich Complexity Model**: 
我々はボトムレベルのEthereumの構造は出来る限りシンプルであるべきだと信じている。
そして、そのEthereuのインターフェイスは
（開発者にとっての高位のプログラミングの言語、そしてユーザーにとってユーザーインターフェイスを含めて）
出来る限り理解しやすいものであるべきだと信じている。

複雑であることが避けられないのであれば、それはプロトコルの"中間層"に押しやられるべきだ。
核となるコンセンサスの一部ではなく、だがエンドユーザーからも見ることが出来ない場所である。
高位の言語のコンパイラではシリアライゼーションとデシリアライゼーションを行うスクリプト、
ストレージデータストラクチャーのモデルの引数である。
leveldbストレージのインターフェースとワイヤプロトコルその他、しかしながらこれらの設定は絶対的なものではない。

2. **Freedom**:ユーザーはEthereumのプロトコルを使う目的に縛られるべきではない、
そして我々は優先的にEthereumの特定のコントラクトや、特定の意図を持って作られたトランザクションに対して、
賛成か反対かを選択的に述べようとするべきではない。
これは"本質的な中立状態"のコンセプトに基づくガイドのための原則に似ている。

この原理に従わない例を１つ上げると、ビットコインのトランザクションプロトコルの中で
ブロックチェーンを"一般的な使い方から外れた"の使い方のために使っていることが非難されることや、
（例えばデータストレージや、メタープロトコル）ある種の使い方の中で、
あからさまに外見上のプロトコルの変更が（例えばOP_RETURNの規制が40バイトのこと）
ブロックチェーンを"認められていない”使われ方で使っているアプリケーションへの攻撃の目的のために作られることが上げられる。

Ethereum では、我々はトランザクションのフィーをインセンティブと強く連動するように設計する思想に強く賛成している。
それはブロックチェーンの肥大を生み出す使い方をしてブロックチェーンを使うユーザーが
彼ら自身にかかるコストを内在化させるような使い方である。（例えば、Pigovian Taxation)

3. **一般化**: Ethereum におけるプロトコルの使用とOpcodes は出来るだローレベルのコンセプトを内包するべきである。それ故に
現在においては有用には見えないかもしれないが将来的には有用になりうる任意の使い方で組み合わされうるだろう。
そしてそのために効率的に他の必要ではない機能を削ぎ落とすことに依って、ローレベルのコンセプトの設計は一層効率的になりうるだろう。

この原理に従う例は、DAPPSのフィードの情報（とりわけ軽いクライアント）を取るための、ログ用のLOG OPCODEの選択の例がある。
内部で早い頃から提案されたように、シンプルに全てのトランザクションとメッセージのログを取ることとは対象的な例だ、。
”メッセージ”のコンセプトは、”関数の呼び出し”、"外部の観察者に対してのイベントリッスン"を含む、多くのコンセプトが集まっていて、メッセージと、トランザクションとは切り分けることが妥当であった。

4. **我々は機能を持たない**:一般化に対しての必然的な帰結として、
我々は直感的にプロトコルのパーツとなるような非常にありふれたハイレベルのユースケースの構築を断る。
もし人々が本当にそれを欲しているならば、常にサブのプロトコル（例えばEtherをベースとしたサブの貨幣や、bitcoin/litecoin/dogecoin/sidechain等）として契約の内部に作るだろう理解しているからだ。
例をあげるとBitcoinのようなロックタイムの機能がEthereumの中には無い。
そのような機能は、ユーザーがサインされたデータパケットを送ったり、
そしてそのデータのパケットを特定の契約にプロトコルを通じてシミュレートされていくかもしれない。

5. **リスクを嫌悪しない**: もしリスクが増大していくような変更がとても大きい利益をもたらすのであれば、我々は高いリスクを受け入れる。（例えば一般化された状態遷移や、50倍の速度のブロック承認時間、コンセンサスの合意性、等だ）
これらの理論は全てEthreumの開発の指南の中に含まれているが、絶対的ではない。
開発の時間を減らし、とても多くの急激な変更を一度に行わないようにしたいと思ったことがとある変更を行うにあたって遅延をもたらしたことがある。明らかに将来のリリースに向けてメリットが有ることであったとしても。（例えば Ethereum1.1)


## ブロックチェーンレベルでのプロトコル:
このセクションでは、Ethereumで為されたいくつかのブロックチェーンレベルでのプロトコルの変更についての詳細を記載する。
どのようにしてトランザクションが機能しているのか、そしてどのようにしてデータがシリアライズされ保存されているのか、アカウントの背後にあるメカニズムについてだ。

### アカウントと使われていないUTXOs
ビットコイン、そしてビットコインに付帯する多くのデリバティブ、ユーザーのバランスについてのデータは、
使われていないトランザクションのアウトプットに基づく構造の中にある。
システム内のすべての状態は、"Unspent Outputs"（例：コイン)、ｓのようなそれぞれのコインが所持者と価値を持ち、トランザクションは1つかより多くのコインを使い、1つやより多くの新しいコインを作り出す、下記のような制約が有効であることのもとに。

1.あらゆる参照されているインプットは正しく、そして未だ使われていない。
2.トランザクションは署名を持っていなければならず、全てのインプットの場合において、
　その署名はインプットの所持者のものと一致していなければならない。
3. インプットの合計の価値は、アウトプットの合計の価値と等しいか、もしくは少ない。

![](https://bitcoin.org/img/dev/en-transaction-propagation.svg)  
(Image from https://bitcoin.org/en/developer-guide)  

システムにおける有るユーザーの残高は、上記のようにして、
プライベートキーをもっているユーザーが有効な署名を作り出すことが出来るコインの合計の価値に等しい。

このスキームはより単純なアプローチに賛成である。
ある状態はそれぞれのアカウントが残高を所持しているアカウントのリストを保持し、
同様にEthereum特有のコードと内部のストレージデータを保持する。
そして、もし送り手が、送る分の価値に値する十分な残高を持っているならば、
送り手のアカウントから引き落とされ、受け取り手は送られた価値を受け取る。
もし受取り手のアカウントがコードを持っていたならば、コードは実行され内部のストレージも同時に変更されるかもしれない。
もしくは受け取り手のアカウントのコードは新たな追加のメッセージを生成し、
引き落としや、受け取りのために他のアカウントに送ることに繋がるかもしれない。

UTXOsのメリットは下記となる。:

1. **高水準ののプライバシー**: 
もしユーザーが各々のトランザクションで新しいアカウントを使うのであれば、
アカウントとアドレスとを結びつけることは通常難しいだろう。
これは通貨にとても適している性質だ。だが、任意のDapps(分散形のアプリ）としては適していない。
何故なら任意の分散形のアプリは複雑な塊となった情報のユーザーの状態を追いかける必要がよくあるため、
通貨システムのように、簡単なユーザーの状態の切り分けは存在しないかもしれないからだ。

2. **潜在的にスケーラビリティのある仕組み**:
UTXOsは理論的にある種のスケーラビリティがあるパラダイムとよく適合している。
何故なら我々は、マークルツリー上で所有権を証明している幾つかのコインの所有者だけに依存しているため
オーナーを含めた誰もがその所有権のデータを忘れると決めた場合でさえも、オーナーだけが損害を受けることで済むからだ。
アカウントの考え方では、有るアカウントに対応するマークルツリーの一片を失った
誰もがメッセージに依ってそのアカウントに影響を与えることが、そのアカウントに対してメッセージを送ることを含めて絶対にできなくなる。しかしながらUTXOに依存しないスケーラビリティの枠組みが確かに存在している。

アカウントによるメリットは、
大きなスペースの節約: 例えばひとつのアカウントが5つのUTXOを持っているとした時、UTXOモデルからアカウントモデルに切り替えれば、
必要なスペースは(20 + 32 + 8) * 5 = 300 bytes (20 はアドレスに, 32 はトランザクションIDに and 8は送信する価値に）t 20 + 8 + 2 = 30 bytes (20 はアドレスに, 8 は送信する価値に、2 はNonce（1度だけ使われる番号）に)).となる。
実際のスペースの節約はこれほど大きなものにはならない、何故ならアカウントはパトリシアツリー上に保存される必要があるためだ。
しかしそうであったとしても大きな節約だ。
加えて、トランザクションはより小さくすることが出来る（例えば、Ethereum上の100バイトと、Bitcoin上の200-250バイト）、何故ならばあらゆるトランザクションは1回の参照と1回の署名を行い、1つのアウトプットを出すだけを必要とするからだ。

大きな代替性: 特定のコインの元となるブロックチェーンレベルでないコンセプトであるがために、それは一層実用的ではなく、技術的でも法的でもあるレッドリストやブラックリストの仕組みを構築するために、そして何に依拠しているコイン何かに判別を付けることは一層

シンプルさ: より簡単に書いて、分かりやすく、とりわけ一旦一層複雑なスクリプトが含まれたときに。
だが、任意の分散形のアプリケーションをUTXOの考え方という、狭い場所へと押しこむことも出来るが、
根本的にスクリプトに対してどのようなUTXOに対して与えられたUTXOが使われる事が出来るかといったことを成約する能力を与え、
スクリプトが判別できる「アプリケーションの状態のルートの変更」のマークルツリーによる証明、を含めることを必要とすることは
そんな概念は、ただアカウントという概念を使うことよりも一層複雑で汚いものだ。

定量のライトなクライアントの参照: ライトなクライアントは
Constant light client reference: light clients can at any point access all data related to an account by scanning down the state tree in a specific direction. In a UTXO paradigm, the references change with each transaction, a particularly burdensome problem for long-running dapps that try to use the above mentioned state-root-in-UTXO propagation mechanism.
We have decided that, particularly because we are dealing with dapps containing arbitrary state and code, the benefits of accounts massively outweigh the alternatives. Additionally, in the spirit of the We Have No Features principle, we note that if people really do care about privacy then mixers and coinjoin can be built via signed-data-packet protocols inside of contracts.

One weakness of the account paradigm is that in order to prevent replay attacks, every transaction must have a "nonce", such that the account keeps track of the nonces used and only accepts a transaction if its nonce is 1 after the last nonce used. This means that even no-longer-used accounts can never be pruned from the account state. A simple solution to this problem is to require transactions to contain a block number, making them un-replayable after some period of time, and reset nonces once every period. Miners or other users will need to "ping" unused accounts in order to delete them from the state, as it would be too expensive to do a full sweep as part of the blockchain protocol itself. We did not go with this mechanism only to speed up development for 1.0; 1.1 and beyond will likely use such a system.

Merkle Patricia Trees

The Merkle Patricia tree/trie, previously envisioned by Alan Reiner and implemented in the Ripple protocol, is the primary data structure of Ethereum, and is used to store all account state, as well as transactions and receipts in each block. The MPT is a combination of a Merkle tree and Patricia tree, taking the elements of both to create a structure that has both of the following properties:

Every unique set of key/value pairs maps uniquely to a root hash, and it is not possible to spoof membership of a key/value pair in a trie (unless an attacker has ~2^128 computing power)
It is possible to change, add or delete key/value pairs in logarithmic time
This gives us a way of providing an efficient, easily updateable, "fingerprint" of our entire state tree. The Ethereum MPT is formally described here: https://github.com/ethereum/wiki/wiki/Patricia-Tree

Specific design decisions in the MPT include:

Having two classes of nodes, kv nodes and diverge nodes (see MPT spec for more details). The presence of kv nodes increases efficiency because if a tree is sparse in a particular area the kv node will serve as a "shortcut" removing the need to have a tree of depth 64.
Making diverge nodes hexary and not binary: this was done to improve lookup efficiency. We now recognize that this choice was suboptimal, as the lookup efficiency of a hexary tree can be simulated in a binary paradigm by storing nodes batched. However, because the trie construction is so easy to implement incorrectly and end up with at the very least state root mismatches, we have decided to table such a reorganization until 1.1.
No distinction between empty value and non-membership: this was done for simplicity, and because it works well with Ethereum's default that values that are unset (eg. balances) generally mean zero and the empty string is used to represent zero. However, we do note that it sacrifices some generality and is thus slightly suboptimal.
Distinction between terminating and non-terminating nodes: technically, the "is this node terminating" flag is unnecessary, as all tries in Ethereum are used to store static key lengths, but we added it anyway to increase generality, hoping that the Ethereum MPT implementations will be used as-is by other cryptographic protocols.
Using sha3(k) as the key in the "secure tree" (used in the state and account storage tries): this makes it much more difficult to DDoS the trie by setting up maximally unfavorable chains of diverge nodes 64 levels deep and repeatedly calling SLOAD and SSTORE on them. Note that this makes it more difficult to enumerate the tree; if you want to have enumeration capability in your client, the simplest approach is to maintain a database mapping sha3(k) -> k.
RLP

RLP ("recursive length prefix") encoding is the main serialization format used in Ethereum, and is used everywhere - for blocks, transactions, account state data and wire protocol messages. RLP is formally described here: https://github.com/ethereum/wiki/wiki/RLP

RLP is intended to be a highly minimalistic serialization format; its sole purpose is to store nested arrays of bytes. Unlike protobuf, BSON and other existing solutions, RLP does not attempt to define any specific data types such as booleans, floats, doubles or even integers; instead, it simply exists to store structure, in the form of nested arrays, and leaves it up to the protocol to determine the meaning of the arrays. Key/value maps are also not explicitly supported; the semi-official suggestion for supporting key/value maps is to represent such maps as [[k1, v1], [k2, v2], ...] where k1, k2... are sorted using the standard ordering for strings.

The alternative to RLP would have been using an existing algorithm such as protobuf or BSON; however, we prefer RLP because of (1) simplicity of implementation, and (2) guaranteed absolute byte-perfect consistency. Key/value maps in many languages don't have an explicit ordering, and floating point formats have many special cases, potentially leading to the same data leading to different encodings and thus different hashes. By developing a protocol in-house we can be assured that it is designed with these goals in mind (this is a general principle that applies also to other parts of the code, eg. the VM). Note that bencode, used by BitTorrent, may have provided a passable alternative for RLP, although its use of decimal encoding for lengths makes it slightly suboptimal compared to the binary RLP.

Compression algorithm

The wire protocol and the database both use a custom compression algorithm to store data. The algorithm can best be described as run-length-encoding zeroes and leaving other values as they are, with the exception of a few special cases for common values like sha3(''). For example:

>>> compress('horse')
'horse'
>>> compress('donkey dragon 1231231243')
'donkey dragon 1231231243'
>>> compress('\xf8\xaf\xf8\xab\xa0\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\xbe{b\xd5\xcd\x8d\x87\x97')
'\xf8\xaf\xf8\xab\xa0\xfe\x9e\xbe{b\xd5\xcd\x8d\x87\x97'
>>> compress("\xc5\xd2F\x01\x86\xf7#<\x92~}\xb2\xdc\xc7\x03\xc0\xe5\x00\xb6S\xca\x82';{\xfa\xd8\x04]\x85\xa4p")
'\xfe\x01'
Before the compression algorithm existed, many parts of the Ethereum protocol had a number of special cases; for example, sha3 was often overridden so that sha3('') = '', as that would save 64 bytes from not needing to store code or storage in accounts. However, a change was made recently where all of these special cases were removed, making Ethereum data structures much bulkier by default, instead adding the data saving functionality to a layer outside the blockchain protocol by putting it on the wire protocol and seamlessly inserting it into users' database implementations. This adds modularity, simplifying the consensus layer, and also allows continued upgrades to the compression algorithm to be deployed relatively easily (eg. via network protocol versions).

Trie Usage

Warning: this section assumes knowledge of how bloom filters work. For an introduction, see http://en.wikipedia.org/wiki/Bloom_filter

Every block header in the Ethereum blockchain contains pointers to three tries: the state trie, representing the entire state after accessing the block, the transaction trie, representing all transactions in the block keyed by index (ie. key 0: the first transaction to execute, key 1: the second transaction, etc), and the receipt tree, representing the "receipts" corresponding to each transaction. A receipt for a transaction is an RLP-encoded data structure:

[ medstate, gas_used, logbloom, logs ]
Where:

medstate is the state trie root after processing the transaction
gas_used is the amount of gas used after processing the transaction
logs is a list of items of the form [address, [topic1, topic2...], data] that are produced by the LOG0 ... LOG4 opcodes during the execution of the transaction (including by the main call and sub-calls). address is the address of the contract that produced the log, the topics are up to 4 32-byte values, and the data is an arbitrarily sized byte array.
logbloom is a bloom filter made up of the addresses and topics of all logs in the transaction.
There is also a bloom in the block header, which is the OR of all of the blooms for the transactions in the block. The purpose of this construction is to make the Ethereum protocol light-client friendly in as many ways as possible. In Ethereum, a light client can be viewed as a client that downloads block headers by default, and verifies only a small portion of what needs to be verified, using a DHT as a database for trie nodes in place of its local hard drive. Some use cases include:

A light client wants to know the state of an account (nonce, balance, code or storage index) at a particular time. The light client can simply recursively download trie nodes from the state root until it gets to the desired value.
A light client wants to check that a transaction was confirmed. The light client can simply ask the network for the index and block number of that transaction, and recursively download transaction trie nodes to check for availability.
Light clients want to collectively validate a block. Each light client C[i] chooses one transaction index i with transaction T[i] (with corresponding receipt R[i]) and does the following:
Initiate the state with state root R[i-1].medstate and R[i-1].gas_used (if i = 0 use the parent endstate and 0 gas_used)
Process transaction T[i]
Check that the resulting state root is R[i].medstate and the gas_used is R[i].gas_used
Check that the set of logs and bloom produced matches R[i].logs and R[i].logbloom
Checks that the bloom is a subset of the block header-level bloom (this detects block header-level blooms with false negatives); then pick a few random indices of the block header-level bloom where that bloom contains a 1 and ask other nodes for a transaction-level bloom that contains a 1 at that index, rejecting the block if no response is given (this detects block header-level blooms with false positives)
Light clients want to "watch" for events that are logged. The protocol here is the following:
A light client gets all block headers, checks for block headers that contain bloom filters that match one of a desired list of addresses or topics that the light client is interested in
Upon finding a potentially matching block header, the light client downloads all transaction receipts, checks them for transactions whose bloom filters match
Upon finding a potentially matching transaction, the light client checks its actual log RLP, and sees if it actually matches
The first three light client protocols require a logarithmic amount of data access and computation; the fourth requires ~O(sqrt(N)) since bloom filters are only a two-level structure, although this can be improved to O(log(N)) if the light client is willing to rely on multiple providers to point to "interesting" transaction indices and decommission providers if they are revealed to have missed a transaction. The first protocol is useful to simply check up on state, and the second in consumer-merchant scenarios to check that a transaction was validated. The third protocol allows Ethereum light clients to collectively validate blocks with a very low degree of trust. In Bitcoin, for example, a miner can create a block that gives the miner an excessive amount of transaction fees, and there would be no way for light nodes to detect this themselves, or upon seeing an honest full node detect it verify a proof of invalidity. In Ethereum, if a block is invalid, it must contain an invalid state transition at some index, and so a light client that happens to be verifying that index can see that something is wrong, either because the proof step does not check out, or because data is unavailable, and that client can then raise the alarm.

The fourth protocol is useful in cases where a dapp wants to keep track of some kind of events that need to be efficiently verifiable, but which do not need to be part of the permanent state; an example is a decentralized exchange logging trades or a wallet logging transactions (note that the light client protocol will need to be augmented with header-level coinbase and uncle checks for this to work fully with mining accounts). In Bitcoin terminology, LOG can be viewed as a pure "proof of publication" opcode.

Uncle incentivization

The "Greedy Heaviest Observed Subtree" (GHOST) protocol is an innovation first introduced by Yonatan Sompolinsky and Aviv Zohar in December 2013, and is the first serious attempt at solving the issues preventing much faster block times. The motivation behind GHOST is that blockchains with fast confirmation times currently suffer from reduced security due to a high stale rate - because blocks take a certain time to propagate through the network, if miner A mines a block and then miner B happens to mine another block before miner A's block propagates to B, miner B's block will end up wasted ("stale") and will not contribute to network security. Furthermore, there is a centralization issue: if miner A is a mining pool with 30% hashpower and B has 10% hashpower, A will have a risk of producing a stale block 70% of the time (since the other 30% of the time A produced the last block and so will get mining data immediately) whereas B will have a risk of producing a stale block 90% of the time. Thus, if the block interval is short enough for the stale rate to be high, A will be substantially more efficient simply by virtue of its size. With these two effects combined, blockchains which produce blocks quickly are very likely to lead to one mining pool having a large enough percentage of the network hashpower to have de facto control over the mining process.

As described by Sompolinsky and Zohar, GHOST solves the first issue of network security loss by including stale blocks in the calculation of which chain is the "longest"; that is to say, not just the parent and further ancestors of a block, but also the stale descendants of the block's ancestor (in Ethereum jargon, "uncles") are added to the calculation of which block has the largest total proof of work backing it.

To solve the second issue of centralization bias, we adopt a different strategy: we provide block rewards to stales: a stale block receives 7/8 (87.5%) of its base reward, and the nephew that includes the stale block receives 1/32 (3.125%) of the base reward as an inclusion bounty. Transaction fees, however, are not awarded to uncles or nephews.

In Ethereum, stale block can only be included as an uncle by up to the seventh-generation descendant of one of its direct siblings, and not any block with a more distant relation. This was done for several reasons. First, unlimited GHOST would include too many complications into the calculation of which uncles for a given block are valid. Second, unlimited uncle incentivization as used in Ethereum removes the incentive for a miner to mine on the main chain and not the chain of a public attacker. Finally, calculations show that restricting to seven levels provides most of the desired effect without many of the negative consequences.

A simulator that measures centralization risks is available at https://github.com/ethereum/economic-modeling/blob/master/ghost.py
A high-level discussion can be found at https://blog.ethereum.org/2014/07/11/toward-a-12-second-block-time/
Design decisions in our block time algorithm include:

12 second block time: 12 seconds was chosen as a time that is as fast as possible, but is at the same time substantially longer than network latency. A 2013 paper by Decker and Wattenhofer in Zurich measures Bitcoin network latency, and determines that 12.6 seconds is the time it takes for a new block to propagate to 95% of nodes; however, the paper also points out that the bulk of the propagation time is proportional to block size, and thus in a faster currency we can expect the propagation time to be drastically reduced. The constant portion of the propagation interval is about 2 seconds; however, for safety we assume that blocks take 12 seconds to propagate in our analysis.
7 block ancestor limit: this is part of a design goal of wanting to make block history very quickly "forgettable" after a small number of blocks, and 7 blocks has been proven to provide most of the desired effect
1 block descendant limit (eg. c(c(p(p(p(head))))), where c = child and p = parent, is invalid): this is part of a design goal of simplicity, and the simulator above shows that it does not pose large centralization risks.
Uncle validity requirements: uncles have to be valid headers, not valid blocks. This is done for simplicity, and to maintain the model of a blockchain as being a linear data structure (and not a block-DAG, as in Sompolinsky and Zohar's newer models). Requiring uncles to be valid blocks is also a valid approach.
Reward distribution: 7/8 of the base mining reward to the uncle, 1/32 to the nephew, 0% of transaction fees to either. This will make uncle incentivization ineffective from a centralization perspective if fees dominate; however, this is one of the reasons why Ethereum is meant to continue issuing ether for as long as we continue using PoW.
Difficulty Update Algorithm

The difficulty in Ethereum is currently updated according to the following rule:

diff(genesis) = 2^32

diff(block) = diff.block.parent + floor(diff.block.parent / 1024) *
    1 if block.timestamp - block.parent.timestamp < 9 else
    -1 if block.timestamp - block.parent.timestamp >= 9
The design goals behind the difficulty update rule are:

Fast updating: the time between blocks should readjust quickly given increasing or decreasing hashpower
Low volatility: the difficulty should not bounce excessively if the hashpower is constant
Simplicity: the algorithm should be relatively simple to implement
Low memory: the algorithm should not rely on more than a few blocks of history, and should include as few "memory variables" as possible. Assume that the last ten blocks, plus all memory variables placed in the block headers of the last ten blocks, are all that is available for the algorithm to work with
Non-exploitability: the algorithm should not excessively encourage miners to fiddle with timestamps, or mining pools to repeatedly add and remove hashpower, in an attempt to maximize their revenue
We have already determined that our current algorithm is highly suboptimal on low volatility and non-exploitability, and at the very least we plan to switch the timestamps compares to be the parent and grandparent, so that miners only have the incentive to modify timestamps if they are mining two blocks in a row. Another more powerful formula with simulations is located at https://github.com/ethereum/economic-modeling/blob/master/diffadjust/blkdiff.py (the simulator uses Bitcoin mining power, but uses the per-day average for the entire day; it at one point simulates a 95% crash in a single day).

Gas and Fees

Whereas all transactions in Bitcoin are roughly the same, and thus their cost to the network can be modeled to a single unit, transactions in Ethereum are more complex, and so a transaction fee system needs to take into account many ingredients, including cost of bandwidth, cost of storage and cost of computation. Of particular importance is the fact that the Ethereum programming language is Turing-complete, and so transactions may use bandwidth, storage and computation in arbitrary quantities, and the latter may end up being used in quantities that due to the halting problem cannot even be reliably predicted ahead of time. Preventing denial-of-service attacks via infinite loops is a key objective.

The basic mechanism behind transaction fees is as follows:

Every transaction must specify a quantity of "gas" that it is willing to consume (called startgas), and the fee that it is willing to pay per unit gas (gasprice). At the start of execution, startgas * gasprice ether are removed from the transaction sender's account.
All operations during transaction execution, including database reads and writes, messages, and every computational step taken by the virtual machine consumes a certain quantity of gas.
If a transaction execution processes fully, consuming less gas than its specified limit, say with gas_rem gas remaining, then the transaction executes normally, and at the end of the execution the transaction sender receives a refund of gas_rem * gasprice and the miner of the block receives a reward of (startgas - gas_rem) * gasprice.
If a transaction "runs out of gas" mid-execution, then all execution reverts, but the transaction is nevertheless valid, and the only effect of the transaction is to transfer the entire sum startgas * gasprice to the miner.
When a contract sends a message to the other contract, it also has the option to set a gas limit specifically on the sub-execution arising out of that message. If the sub-execution runs out of gas, then the sub-execution is reverted, but the gas is nevertheless consumed.
Each of the above components is necessary. For example:

If transactions did not need to specify a gas limit, then a malicious user could send a transaction that makes a multi-billion round loop, and no one would be able to process it since processing such a transaction would take longer than a block interval, but miners would not be able to tell beforehand, leading to a denial-of-service vulnerability.
The alternative to strict gas-counting, time-limiting, does not work because it is too highly subjective (some machines are faster than others, and even among identical machines close-calls will always exist)
The entire value startgas * gasprice has to be taken out at the start as a deposit so that there arise no situations where an account "bankrupts" itself mid-execution and becomes unable to pay for its gas costs. Note that balance checking is not sufficient, because an account can send its balance somewhere else.
If execution did not revert in the event of an insufficient gas error, then contracts would need to take strong and difficult security measures to prevent themselves from being exploited by transactions or messages that provide only enough gas halfway through, thereby leading to some of the changes in a contract execution being executed but not others.
If sub-limits did not exist, then hostile accounts could enact a denial-of-service attack against other contracts by entering into agreements with them, and then inserting an infinite loop at the beginning of computation so that any attempts by the victim contract to compensate the attack contract or send a message to it would starve the entire transaction execution.
Requiring transaction senders to pay for gas instead of contracts substantially increases developer usability. Very early versions of Ethereum had contracts pay for gas, but this led to the rather ugly problem that every contract had to implement "guard" code that would make sure that every incoming message compensated the contract with enough ether to pay for the gas that it consumed.
Note the following particular features in gas costs:

21000 gas is charged for any transaction as a "base fee". This covers the cost of an elliptic curve operation to recover the sender address from the signature as well as the disk and bandwidth space of storing the transaction.
A transaction can include an unlimited amount of "data", and there exist opcodes in the virtual machine which allow the contract receiving a transaction to access this data. The gas fee for data is 1 gas per zero byte and 5 gas per nonzero byte. This formula arose because we saw that most transaction data in contracts written by users was organized into a series of 32-byte arguments, most of which had many leading zero bytes, and given that such constructions seem inefficient but are actually efficient due to compression algorithms, we wanted to encourage their use in place of more complicated mechanisms which would try to tightly pack arguments according to the expected number of bytes, leading to very substantial complexity increase at compiler level. This is an exception to the sandwich complexity model, but a justified one due to the ratio of cost to benefit.
The cost of the SSTORE opcode, which sets values in account storage, is either: (i) 20000 gas when changing a zero value to a nonzero value, (ii) 5000 gas when changing a zero value to a zero value or a nonzero value to a nonzero value, or (iii) 5000 gas when changing a nonzero value to a zero value, plus a 20000 gas refund to be given at the end of successful transaction execution (ie. NOT an execution leading to an out-of-gas exception). Refunds are capped at 50% of the total gas spent by a transaction. This provides a small incentive to clear storage, as we noticed that lacking such an incentive many contracts would leave storage unused, leading to quickly increasing bloat, providing most of the benefits of "charging rent" for storage without the cost of losing the assurance that a contract once placed will continue to exist forever. The delayed refund mechanism is necessary to prevent denial-of-service attacks where the attacker sends a transaction with a low amount of gas that repeatedly clears a large number of storage slots as part of a long-running loop, and then runs out of gas, consuming a large amount of verifiers' computing power without actually clearing storage or spending a lot of gas. The 50% cap is needed to ensure that a miner given a transaction with some quantity of gas can still determine an upper bound on the computational time to execute the transaction.
There is no gas cost to data in messages provided by contracts. This is because there is no need to actually "copy" any data during a message call, as the call data can simply be viewed as a pointer to the parent contract's memory which will not change while the child execution is in progress.
Memory is an infinitely expandable array. However, there is a gas cost of 1 per 32 bytes of memory expansion, rounding up.
Some opcodes, whose computation time is highly argument-dependent, have variable gas costs. For example, the gas cost of EXP is 10 + 10 per byte in the exponent (ie. x^0 = 1 gas, x^1 ... x^255 = 2 gas, x^256 ... x^65535 = 3 gas, etc), and the gas cost of the copy opcodes (CALLDATACOPY, CODECOPY, EXTCODECOPY) is 1 + 1 per 32 bytes copies, rounding up (LOG also has a similar rule). The memory expansion gas cost is not sufficient to cover this, as it opens up a quadratic attack (50000 rounds of CALLDATACOPY of 50000 gas ~= 50000^2 computing effort, but only ~50000 gas before the variable gas cost was introduced)
The CALL opcode (and CALLCODE for symmetry) costs an additional 9000 gas if the value is nonzero. This is because any value transfer causes significant bloat to history storage for an archival node. Note that the actual fee charged is 6700; on top of this we add a mandatory 2300 gas minimum that is automatically given to the recipient. This is in order to ensure that wallets that receive transactions to at least have enough gas to make a log of the transaction.
The other important part of the gas mechanism is the economics of the gas price itself. The default approach, used in Bitcoin, is to have purely voluntary fees, relying on miners to act as the gatekeepers and set dynamic minimums; the equivalent in Ethereum would be allowing transaction senders to set arbitrary gas costs. This approach has been received very favorably in the Bitcoin community particularly because it is "market-based", allowing supply and demand between miners and transaction senders determine the price. The problem with this line of reasoning is, however, that transaction processing is not a market; although it is intuitively attractive to construe transaction processing as a service that the miner is offering to the sender, in reality every transaction that a miner includes will need to be processed by every node in the network, so the vast majority of the cost of transaction processing is borne by third parties and not the miner that is making the decision of whether or not to include it. Hence, tragedy-of-the-commons problems are very likely to occur.

Currently, due to a lack of clear information about how miners will behave in reality, we are going with a fairly simple approach: a voting system. Miners have the right to set the gas limit for the current block to be within ~0.0975% (1/1024) of the gas limit of the last block, and so the resulting gas limit should be the median of miners' preferences. The hope is that in the future we will be able to soft-fork this into a more precise algorithm.

Virtual Machine

The Ethereum virtual machine is the engine in which transaction code gets executed, and is the core differentiating feature between Ethereum and other systems. Note that the virtual machine should be considered separately from the contract and message model - for example, the SIGNEXTEND opcode is a feature of the VM, but the fact that contracts can call other contracts and specify gas limits to sub-calls is part of the contract and message model. Design goals in the EVM include:

Simplicity: as few and as low-level opcodes as possible, as few data types as possible and as few virtual-machine-level constructs as possible
Total determinism: there should be absolutely no room for ambiguity in any part of the VM specification, and the results should be completely deterministic. Additionally, there should be a precise concept of computational step which can be measured so as to compute gas consumption.
Space savings: EVM assembly should be as compact as possible (eg. the 4000 byte base size of default C programs is NOT acceptable)
Specialization to expected applications: the ability to handle 20-byte addresses and custom cryptography with 32-byte values, modular arithmetic used in custom cryptography, read block and transaction data, interact with state, etc
Simple security: it should be easy to come up with a gas cost model for operations that makes the VM non-exploitable
Optimization-friendliness: it should be easy to apply optimizations so that JIT-compiled and otherwise sped-up versions of the VM can be built.
Some particular design decisions that were made:

Temporary/permanent storage distinction - a distinction exists between temporary storage, which exists within each instance of the VM and disappears when VM execution finishes, and permanent storage, which exists on the blockchain state level on a per-account basis. For example, suppose the following tree of execution takes place (using S for permanent storage and M for temporary): (i) A calls B, (ii) B sets B.S[0] = 5, B.M[0] = 9, (iii) B calls C, (iv) C calls B. At this point, if B tries to read B.S[0], it will receive the value stored in B earlier, 5, but is B tries to read B.M[0] it will receive 0 because it is a new instance of the virtual machine with fresh temporary storage. If B now sets B.M[0] = 13 and B.S[0] = 17 in this inner call, and then both this inner call and C's call terminate, bringing the execution back to B's outer call, then B reading M will see B.M[0] = 9 (since the last time this value was set was in the same VM execution instance) and B.S[0] = 17. If B's outer call ends and A calls B again, then B will ses B.M[0] = 0 and B.S[0] = 17. The purpose of this distinction is to (1) provide each execution instance with its own memory that is not subject to corruption by recursive calls, making secure programming easier, and (2) to provide a form of memory which can be manipulated very quickly, as storage updates are necessarily slow due to the need to modify the trie.
Stack/memory model - the decision was made early on to have three types of computational state (aside from the program counter which points to the next instruction): stack (a standard LIFO stack of 32-byte values), memory (an infinitely expandable temporary byte array) and storage (permanent storage). On the temporary storage side, the alternative to stack and memory is a memory-only paradigm, or some hybrid of registers and memory (not very different, as registers basically are a kind of memory). In such a case, every instruction would have three arguments, eg. ADD R1 R2 R3: M[R1] = M[R2] + M[R3]. The stack paradigm was chosen for the obvious reason that it makes the code four times smaller.
32 byte word size - the alternative is 4 or 8 byte words, as in most other architectures, or unlimited, as in Bitcoin. 4 or 8 byte words are too restrictive to store addresses and big values for crypto computations, and unlimited values are too hard to make a secure gas model around. 32 bytes is ideal because it is just large enough to store 32 byte values common in many crypto implementations, as well as addresses (and provides the ability to pack address and value into a single storage index as an optimization), but not so large as to be extremely inefficient.
Having our own VM at all - the alternative is reusing Java, or some Lisp dialect, or Lua. We decided that having a specialized VM was appropriate because (i) our VM spec is much simpler than many other virtual machines, because other virtual machines have to pay a much lower cost for complexity, whereas in our case every additional unit of complexity is a step toward high barriers of entry creating development centralization and potential for security flaws including consensus failures, (ii) it allows us to specialize the VM much more, eg. by having a 32 byte word size, (iii) it allows us not to have a very complex external dependency which may lead to installation difficulties, and (iv) a full security review of Ethereum specific to our particular security needs would necessitate a security review of the external VM anyway, so the effort savings are not that large.
Using a variable extendable memory size - we deemed a fixed memory size unnecessarily restrictive if the size is small and unnecessarily expensive if the size is large, and noted that if statements for memory access are necessary in any case to check for out-of-bounds access, so fixed size would not even make execution more efficient.
Not having a stack size limit - no particular justification either way; note that limits are not strictly necessary in many cases as the combination of gas costs and a block-level gas limit will always act as a ceiling on the consumption of every resource.
Having a 1024 call depth limit - many programing languages break at high stack depths much more quickly than they break at high levels of memory usage or computational load, so the implied limit from the block gas limit may not be sufficient.
No types - done for simplicity. Instead, signed and unsigned opcodes for DIV, SDIV, MOD, SMOD are used instead (it turns out that for ADD and MUL the behavior of signed and unsigned opcodes is equivalent), and the transformations for fixed point arithmetic (high-depth fixed-point arithmetic is another benefit of 32-byte words) are in all cases simple, eg. at 32 bits of depth, a * b -> (a * b) / 2^32, a / b -> a * 2^32 / b, and +, - and * are unchanged from integer cases.
The function and purpose of some opcodes in the VM is obvious, however other opcodes are less so. Some particular justifications are given below:

ADDMOD, MULMOD: in most cases, addmod(a, b, c) = a * b % c. However, in the specific case of many classes of elliptic curve cryptography, 32-byte modular arithmetic is used, and doing a * b % c directly is therefore actually doing ((a * b) % 2^256) % c, which gives a completely different result. A formula that calculates a * b % c with 32-byte values in 32 bytes of space is rather nontrivial and bulky.
SIGNEXTEND: the purpose of SIGNEXTEND is to facilitate typecasting from a larger signed integer to a smaller signed integer. Small signed integers are useful because JIT-compiled virtual machines may in the future be able to detect long-running chunks of code that deals primarily with 32-byte integers and speed it up considerably.
SHA3: SHA3 is very highly applicable in Ethereum code as secure infinite-sized hash maps that use storage will likely need to use a secure hash function so as to prevent malicious collisions, as well as for verifying Merkle trees and even verifying Ethereum-like data structures. A key point is that its companions SHA256, ECRECOVER and RIPEMD160 are included not as opcodes but as pseudo-contracts. The purpose of this is to place them into a separate category so that, if/when we come up with a proper "native extensions" system later, more such contracts can be added without filling up the opcode space.
ORIGIN: the primary use of the ORIGIN opcode, which provides the sender of a transaction, is to allow contracts to make refund payments for gas.
COINBASE: the primary uses of the COINBASE opcode are to (i) allow sub-currencies to contribute to network security if they so choose, and (ii) open up the use of miners as a decentralized economic set for sub-consensus-based applications like Schellingcoin.
PREVHASH: used as a semi-secure source of randomness, and to allow contracts to evaluate Merkle tree proofs of state in the previous block without requiring a highly complex recursive "Ethereum light client in Ethereum" construction.
EXTCODESIZE, EXTCODECOPY: the primary uses here are to allow contracts to check the code of other contracts against a template, or even simulating them, before interacting with them. See http://lesswrong.com/lw/aq9/decision_theories_a_less_wrong_primer/ for applications.
JUMPDEST: JIT-compiled virtual machines become much easier to implement when jump destinations are restricted to a few indices (specifically, the computational complexity of a variable-destination jump is roughly O(log(number of valid jump destinations)), although static jumps are always constant-time). Hence, we need (i) a restriction on valid variable jump destinations, and (ii) an incentive to use static over dynamic jumps. To meet both goals, we have the rules that (i) jumps that are immediately preceded by a push can jump anywhere but another jump, and (ii) other jumps can only jump to a JUMPDEST. The restriction against jumping on jumps is needed so that the question of whether a jump is dynamic or static can be determined by simply looking at the previous operation in the code. The lack of a need for JUMPDEST operations for static jumps is the incentive to use them. The prohibition against jumping into push data also speeds up JIT VM compilation and execution.
LOG: LOG is meant to log events, see trie usage section above.
CALLCODE: the purpose of this is to allow contracts to call "functions" in the form of code stored in other contracts, with a separate stack and memory, but using the contract's own storage. This makes it much easier to scalably implement "standard libraries" of code on the blockchain.
SUICIDE: an opcode which allows a contract to quickly delete itself if it is no longer needed. The fact that SUICIDES are processed at the end of transaction execution, and not immediately, is motivated by the fact that having the ability to revert suicides that were already executed would substantially increase the complexity of the cache that would be required in an efficient VM implementation.
PC: although theoretically not necessary, as all instances of the PC opcode can be replaced by simply putting in the actual program counter at that index as a push, using PC in code allows for the creation of position-independent code (ie. compiled functions which can be copy/pasted into other contracts, and do not break if they end up at different indices). 