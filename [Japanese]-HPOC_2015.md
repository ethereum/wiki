---
name: HPOC 2015
category: 
---

HPoC 2015

昨年私達は、暗号通貨とCryptoeconomic のテクノロジーが主流のものとなるのを妨げています。
幾つかの技術的、経済的な難しい問題を叙述し、"Hard Problems of Cryptocurrency"の文書を出しました。

そのリストには、幾つもの他の困難とともに例えばスケーラビリティや、より効率的なコンセンサスのアルゴリズム、公益となるインセンティブの仕組みを叙述しました。

昨年に渡って多くの問題が解決されました、少なくとも追加された更新はもはや、0から1への解決が必要な根本的な問題ではなくなり、
むしろ現実世界への確かな実装の問題となりました。
そして、同時に、かなりの数の試練が未だに残っている。それにもかかわらずここにある問題はかなり限定的で、
過去のものよりも広域に渡るものではありません。

メタコンセンサス（コンセンサスのためのコンセンサス）

技術的な視野が急速に広がることに関連し続けるために、ソフトウェアは更新されなければならず、
更新しないソフトウェアは代替手段が無いものの、ゆっくりとより優れた技術へと取って代わられて死に絶えます。
このことは何故この問題が分散形の暗号プロトコルの同様に当てはまるべきでないのかの理由とはなりません。
しかしながら分散形の暗号システムは自然な疑問へと結びつく。誰が更新のプロセスをコントロールするのでしょうか？
このことを書いている間に、Bitcoinのコミュニティはブロックサイズを1MBから20MBに増やすかどうかを決定するプロセスを辿っていて、
これは、意図を明確化し、新しく機関を設立して意思決定をするというプロセスを得ずして、
既定路線による意思決定のプロセスがなされる状況へと結びつくこととなっていて、
政治的な議論が、小数のコア開発者の中で行われる、という状況を生み出しています。

meta-consensus（大衆による合意形成を形作っていくもの）となる intra-protocol（内部プロトコル）は有るのでしょうか、
ー 即ち、プロトコルの更新に対しての合意形成を円滑化する、
例えば consensus 決定に関する投票 のような、プロトコルの内部にあるメカニズムはあるでしょうか？
あるいは、何故この理論は分散形の暗号プロトコルに同様に当てはめられるべきではないのでしょうか。
そんなシステムがあったとすれば、
恩恵は計り知れず、
柔軟性が格段と広がり、
中央集約型のリスクとなる政治的な癒着に頼らずにすみ、
かつ、参加者に対して大きな発言権を得ることができ、
社会的に "well-connected" であるような人々のみならず、あらゆる人にその恩恵が行き渡るでしょう。
 (see Jo Freeman's The Tyranny of Structurelessness for more on this).



In order to remain relevant in a rapidly evolving technological landscape, software must update, and software that does not update has no alternative but to slowly fade away and die as it is replaced by superior technology. There is no reason why this principle should not apply to decentralized crypto-protocols as well. However, the need to make upgrades to decentralized crypto-systems leads to a natural question: who controls the updating process? As of the time of this writing, the Bitcoin community is going through the process of deciding whether or not to increase the maximum block size from 1 MB to 20 MB, and the lack of a deliberately instituted decision-making process has lead to the situation where the de-facto decision-making process essentially amounts to political debate among a small number of core developers.

Is there a way of building intra-protocol meta-consensus - that is, a mechanism inside the protocol that facilitates agreement on upgrades to the protocol, perhaps using some kind of consensus voting? Such a system would have a number of benefits, including greater flexibility, less reliance on political processes that end up being centralization risks, and can arguably ensure a greater voice to all participants in the protocol, not just those that are socially well-connected (see Jo Freeman's The Tyranny of Structurelessness for more on this).

望ましいメタコンセンサスのプロトコルは下記を含みます: 
Desirable properties of meta-consensus include:

* 利益となるプロトコルの変更に対しての合意のインセンティブ
* ある種の古いプロトコルのもとに作られるアプリケーションが新しいプロトコルのもとでも最大限効果的であること。
* ある種のオブジェクト指向のカプセル化のメカニズムのように、開発者があるプロトコル特有の機能を使わなければならないようにすることから離れるようにする。(interface の実装) 
* メカニズムが壊れないように抗うということは、望ましくないプロトコルの変更をしなければならないことに繋がっていきます。
* ユーザーが自動的に新しく選ばれたプロトコルへと、新しいクライアントをダウンロードせずに更新することが出来るようにすること。
* 現時点でこのメタプロトコルに対する取り組みは、Arthur Bretman's Tezos を含む、Bitsharesの delegated proof of stake そして未だリリースされていない Ethereum 2.0 の提案があります。

Some kind of guarantee that applications built under the old protocol will remain maximally effective under the new protocol. Note that a part of this will likely be some kind of object-oriented encapsulation mechanisms in order to steer developers away from using features which are highly protocol-specific
Resistance against the mechanism being corrupted in some sense and forced to lead to an undesirable protocol change
The ability for users to automatically upgrade to a newly chosen protocol without having to download a new client
Existing work on this includes Arthur Breitman's Tezos, Bitshares' delegated proof of stake, and the not-yet-released Ethereum 2.0 proposal.

公共益の設定による解放と、インセンティブの仕組み

暗号プロトコルの開発における他の大きな問題は、類似しているファンディングの問題です。どのようにしてプロトコルが更新され、そしてより多くの一般的で公共益に繋がるミドルウェアとが結びつくか、という問題です。
最初の開発フェイズにおいては、Crowdsaleのコンセプトは絶対的に欠かせないものです。何百万ドルの努力に対してのファンディング
そうでなければ・・・以下略
The other major problem in crypto-protocol development is the analogous funding problem: how will protocol upgrades, and more generally public-good ecosystem middleware, be paid for? In the initial phase of development, the concept of "crowdsales" has proven to be absolutely invaluable, providing millions of dollars of funding for efforts which would have otherwise been relegated to being little more than volunteer side projects. However, crowdsales by themselves have a fundamental problem: they are front-loaded, creating heavy incentives for short-term marketing but less so for long-term performance, and they are limited one-time events from which the funds received eventually run out. And even if the funds could somehow be managed so as not to run out, crypto-protocol development would be permanently centralized in a single organization. What would be ideal is, once again, some kind of intra-protocol mechanism for incentivizing the development of public goods.

公共益に対するインセンティブ化（動機付け）の仕組みは、長い文章を確立するというものを含んでいます。
その文章は、
メカニズムの仕様の設計から生まれる数多くのことと、そしてどのようにして公共益の開発に投資し、どの公共益が第一に投資されるに値するか、を決めるということを含んでいます。
Blockchainの文脈では、
個人的なマーケットの文脈よりもファンディングの問題は幾分簡単です。
なぜなら我々には全ての通貨の保持者に
インフレーションによる課税をし、トランザクションフィーからコンセンサスのコストを引いた超過分を得ることが出来るからです。
しかし、preferece revelation の問題はいっそう困難なものです。というのは、人間の政府は非公式な決定を用いようとし、サポートに値する公共益が何かを判断するための非公式な判断を用いようとしますが、しかし、Blockchainは文脈的に信頼に足るファンドと、信頼に足らないファンドの違いを知ることが出来ません。例えば、とあるタバコ会社は、そのスキャムであり、使用不可能な "zero address" かどうか判断することができません。
それ故に、完全にある種の明白なコミュニティからもたらされる情報に対して設計されたメカニズムに頼らなければなりません。

Public goods incentivization has a long and established literature, including numerous results from mechanism design and assurance contract theory on the topic of both how to fund the development of public goods and on the equally important question of deciding which public goods are worth funding in the first place. 
In a blockchain context, 
the funding problem is somewhat easier than in a private-market context, 
because we have the ability to "tax" all currency holders via inflation and taking the excess of transaction fees minus consensus costs, 
but the preference revelation problem is much harder; 
a human government can try to use informal judgement to figure out which public goods are worth supporting and which ones are not, 
but a blockchain literally cannot tell the difference between a trustworthy development fund, 
an untrustworthy development fund, 
a tobacco lobbying company, 
the "zero address" from which coins can never be spent and a scammer. Hence, it must rely entirely on some kind of mechanism design to elicit this information from the community.

この困難は、Blockchainの文脈に対してのこれらの保証について当てはめます。
この困難に対する特定の挑戦は下記の内容を含みます:
The challenge here is applying these guarantees to a blockchain context. Particular challenges include:

* インセンティブと結びついたシステムの設計は、ユーザーが安全で信頼をおかずにお互いに贈り合うことさえ出来ます
* 例えばコントラクトを通じて（Game theoryでの協調ゲームはこの点にある）
* インセンティブと結びついたシステムの設計は、攻撃者が（P+ epsilon攻撃のように、協調ゲーム理論が不十分かもしれない、何故なら全体的にユーザーの集合は、完全に強調するか、それとも一切協調しないかのどちらかであることが期待されるからだ。）
* Caplanianの"理性的な非理性　(rational irrationality"の批判には、例えば十分に大きな報酬をユーザーに対して与えることで、どのファンドがサポートに値するかを知り、そして単純にすぐにどのようにみえるのがより良いかをすぐに単純にクリックしない。

一般的なモデルは下記のように考えられる。:
* Nの公共益が存在し、それらのファンドが有る公共益に対して使うと約束しているアドレスによって表されているとする。誰でもそのアドレスをこのセットに登録することは出来る、ユーザーが必要とする幾らかの手数料を支払った後かも知れない。、どのようにfunding pool Dを分割するかを選択し、（Dは最大の大きさかもしれない、そのメカニズムは、幾らかのDを燃やすかもしれない。）
* 攻撃者は、そこにただお金を送って欲しいだけの偽物の公共益を作り、公共液として、1が分配される和ありアイが完全に一つのプレイヤーに対していくように見えるかもしれない。
* 攻撃者がどうして攻撃に成功することが社会的に悪である理由は、他の"公式な”公共益が、より高い支払いの割合を持っているかもしれないからだ

Being incentive-compatible even given the assumption that users can securely and trustlessly bribe each other, eg. via contracts (cooperative game theory can help here)
Being incentive-compatible even given the assumption that the attacker may have a superior ability to coordinate versus the individual participants (cf. P + epsilon attacks; cooperative game theory may be insufficient here as it generally assumes that collections of users can either coordinate totally or not coordinate at all)
Avoiding Caplanian "rational irrationality" critiques, ie. providing sufficiently large incentives for users to learn about which funds are worth supporting and not simply immediately click on what looks nicer
The general model can be thought of as follows:
もし必要であるならば、我々はユーザーがセキュリティのデポジットを持っていることに頼る事が出来る。例 承認者であること、脅されていないことが証明できる投票）
There exist N public goods, represented by addresses owned by funds that promise to spend the money on particular public goods (eg. development). Anyone can register an address in this set, perhaps after paying some fee
Users need to, using some mechanism, select how to split up funding pool D between these goods (D may be a maximum size; the mechanism may end up "burning" some of D)
Attackers creating fake "public goods" that really just send money to themselves can be seen simply as being public goods with a payoff ratio of 1 that happens to be concentrated entirely in a single player. The reason why attackers winning is socially bad is that other "legitimate" public goods will have much higher payoff ratios
If necessary, we can rely on the users having security deposits, eg. being validators
Coercion-proof Voting

2001年のAri Juel は、"脅迫耐性のある電子投票システム" のプロトコル を考えついた。　それは投票をオンラインで、ユーザーが他者に誰に投票したくぉ公表すること無く投票が出来るだけでなく、さらには実際に、望むのでさえあれば他の誰かに投票することを証明することが出来ないようになっている。ー脅迫したり賄賂を投票する人に対して送ることが不可能となる。
投票問題と同様には全般的に、revelation問題の設定を解決する有る一つの可能性のある方法であうｒ．
このようなスキームを実装することが出来る。それによって協調することが更に困難となる。
ユーザーが2つのIDをコントロール出来るために、2つのIDが安全に腐敗した取引を互いに行うことは出来、完全な解決策は決してないが、
富の集中を防ぐことの思い込みはシステムをかなる頑強なものとする。
Ari Juels in 2001 came up with a protocol for "coercion-resistant electronic elections" - a way of doing voting online such that not only is it possible for users to vote without revealing to others who they voted for, but it is in fact not possible to prove to anyone else who you voted for even if you wanted to - making it impossible to coerce or bribe people to vote. One possible route to solving the preference revelation problem, as well as voting games in general, is to implement this kind of scheme, making it much harder to coordinate. It is by no means a complete solution, since a user that controls two identities can still have those two identities securely collude with each other, but combined with a wealth deconcentration assumption it would make systems substantially more robust.

試練は、どのようにしてある種の脅迫耐性のある電子投票システムをDecenralizedな公共のblockchainの文脈に実装するかということだ。その最大の問題は、完全に特定の秘密鍵を持っている当事者がおらず、互いに不正をしない信頼される事ができ、システムは完全に公共であることが必要だろう。

注意: 暗号学的に脅迫耐性のある投票システムを作る大体の手段は、暗号経済学的に脅迫耐性のある電子投票のシステムを制作することだ。ある種のメカニズムを思いつき、もし投票者が他の当事者に対して証明することが出来たのであれば、ゼロでないクオリティの誰が投票したかに対しての情報を得ることが出来る。
Note: an alternative to making cryptographically coercion-resistant electronic elections is making cryptoeconomically coercion-resistant electronic elections: come up with some kind of mechanism such that if a voter proves to another party any nonzero quantity of information about who they voted for, that party can exploit them for an expected nonzero return at their expense (if this exploitation can be made to be unprovable, then even better, as it reduces the possibility of effective circumvention through reputation).

事前に解放されることに反対すること

Schellingcoinのように沢山のプロトコルがある、AugurではN-of-Nのプロトコルを乱数生成において使用し、ユーザーが最初に暗号的に
ある値xを"コミット”したことに気付き、例えば、H(x)をあるハッシュの関数に対してサブミットすること、そして後になってサブミットされた値xを公表する段階となって、何もrevelationや、誤りとなる解放は無く、セキュリティのデポジットの負担によって罰することが出来る。
しかしながら、これらの多くのプロトコルは、もしユーザーの答えが最初のフレーズの間はプライベートであるのであれば効果的に機能する。
そして、取り分け情報を共有した事が証明できるために、不正が簡単になるに連れて、このメカニズムの効率性は、減少していくこととなる。
There are many protocols such as Schellingcoin, Augur, N-of-N protocols in random number generation, etc, that rely on some notion of users first cryptographically "committing" to some value x, eg. by submitting H(x) for some hash function, and then in a later stage "revealing" the value for x that they submitted - with non-revelation or incorrect revelation being punishable by security deposit loss. However, many of these protocols only work effectively if users' answers during the first phase are private; if users can collude to share information, and particularly if they can provably share information, then the mechanism's effectiveness decreases as collusion becomes easier.

あらゆるユーザーが0か1かを提供するコミットが明らかにするプロトコを考えて見て欲しい、そしてランダムにこの値を選ぶことを考えてみて欲しい最初は、どちらのユーザーも関係のないsalt s を選ばなければならない、そしてH([s, v]) を提出しvが0か1かとなる。
そして2番目のステップではユーザーはsとvの値を提出しなければならない、そしてコミットに対してチェックされなければならない。
全てのvの値の合計をmod2で行ったものは、ランダムな出力の1つを取り上げたもので、ここにおいて


Suppose a commit-reveal protocol where all users must supply either 0 or 1, and they are "supposed to" randomly choose this value. In the first step, each user must pick a (irrelevant) salt s, and provide H([s, v]) where v is either 0 or 1. In the second step, the user must supply their values of s and v, and have them be checked against the commitment. The sum of all values v mod 2 is then taken as a single bit of random output. Here, hypothetically all players can talk to each other and provably coordinate on voting either 0 or 1. However, we can design an anti-pre-revelation protocol as follows: any party can choose to register a bet against any other party that they will vote on some specific value with at last a 60% probability (ie. if A bets that B will bet 0, then if B ends up betting 0 then B gets 2 units out of A's security deposit, and if B ends up betting 1 then A gets 3 units out of B's security deposit). Hence, if party P[i] reveals to party P[j] their v value, or even a zero-knowledge proof of their v value with more than 60% probability, they can be exploited.

困難なことはこの道を選ぶことが出来るか、取りうる値が一層大きく、投票の可能性が前提条件として知られていないかもしれない、もしくは暗号学的に不可能にするか、暗号経済的に誰かの隠された値を明らかにすることをハイコストにすることによって
その他のアプローチ

A challenge is to either take this path and try to expand it into an environment where the potential value space is much larger and probabilities of voting may not be known a priori, or find some other approach to make it cryptographically impossible or cryptoeconomically expensive to reveal one's hidden value before one is supposed to. Tailoring to specific applications (eg. linear Schellingcoin price voting) is suboptimal but acceptable. A particular problem is the existence of zero-knowledge proofs: one can use zk-SNARKs (or simply plain ZKPs) in order to reveal any information about v and prove that this information is correct by using the provided hash H([s, v]) without revealing anything about s or any information about v that is undesired; hence, a solution should be secure against all kinds of partial information revelation that may be useful, and not simply revealing the exact value of v.

乱数の生成
Random Number Generation

コンセンサスのプロトコルを含む様々なプロトコルにおいてblockchain に基づいたくじ、スケーラブルなサンプル抽出のスキーム当
ある種の乱数生成をプロトコル内に必要としている。
Proof of workでは簡単な答えがある、何故なら結果が分かる前に誰かがその回答を計算することが出来ないからだ。そして一旦結果が見つかれば、後悔しないことは大きな機会損失となる。
しかしながらproof of workは極端に高価であり、セキュリティを保っておくために定常的なコストを必要とする。
A number of protocols, including consensus protocols, blockchain-based lotteries, scalable sampling schemes, etc, require some kind of random number generation in the protocol. Proof of work provides an answer easily, because one cannot compute ahead of time what the result will be, and once a result is found not revealing it carries a large opportunity cost. However, proof of work is extremely expensive, requiring constant expenditure equal to the security margin.


N of N のコミットが明らかにすることの代わりとしてTomilionのRANDAOのプロトコルが上げられる。
働き方は以下となる。

n のPlayerが　P[1] ... P[n] としてサイズDのセキュリティのデポジットをプロトコル内に持ち、ランダムな値V[1]... V[n]を
ラウンド1の間にP[i]はH(V[i])をサブミットする。
ラウンド2では、H(V[i])を提出した全てのプレイヤーはV[i}を提出しなければならない。
もし全てのプレイヤーがそれらの値を正しく提出したならば、(V[1] + ... + V[n]) % 2**256 はランダムなシードとして取り上げられ、そして全てのプレイヤーは自らの値を正しく公開する。
n players P[1] ... P[n] with security deposits of size D in the protocol choose random values V[1] ... V[n] with 0 <= v[i] < 2**256, and P[i] submits H(V[i]) during round one.
In round two, every player who submitting a value H(V[i]) must submit V[i].
If all players submit their value correctly, then (V[1] + ... + V[n]) % 2**256 is taken as a random seed, and all players are rewarded with D * i where i is an interest rate (eg. 0.0001 per period).
If not all players submitted their value correctly, then all players who participated get D * i, all players who did not participate lose their entire deposit and are kicked out, and the remaining players repeat the game until eventually all N players cooperate and reveal their values.
もし最後のプレイヤーが参加
If you are the last player to participate, then you can manipulate the value at a very high cost, and even then only by replacing it with a random value next time around (ie. one can avoid unfavorable values, but one cannot target favorable values or even choose between two known values). However, the mechanism has the limitations that (i) it relies on a non-collusion assumption, (ii) the randomness that it provides is not nearly as fast as proof of work randomness, making it useless for some specific intra-block applications where proof of work shines, and (iii) it can lead to honest players losing large amounts of money from a network attack. Mitigating problem (iii) necessarily involves exacerbating problem (ii), and vice versa.

The open-ended challenge is to come up with a mechanism inside of a cryptoeconomic context which provides random numbers as output with maximally relaxed security assumptions and maximal robustness and resilience to attackers - ideally, a mechanism with the same properties as proof of work but without (or with only a negligible fraction of) its cost.

検閲耐久性
Anti-censorship

Public blockchains particularly have a strong need to be censorship-resistant, ie. to be able to get transactions into the chain even if powerful parties do not want them included. This is important not just for controversial Wikileaks-style applications; even ordinary financial applications, commit-reveal protocols, etc, could have their security threatened if there was a way of effectively preventing "challenge" messages from entering the blockchain.

If the set of participants willing to collude to censor is smaller than 33%, then the problem is fairly trivial. If the set is greater than 33%, however, then we know that those participants have the power to shut down the network, albeit at very high cost to themselves. Hence, the problem becomes somewhat different: how do we make it impossible for colluding censors to censor specific transactions without censoring everything else as well?

One possible route to take is some kind of commit-reveal protocol, where users submit a hash of a transaction plus proof that the transaction is available, and then the transaction sender is somehow bound to include the transaction itself once it is revealed (ie. blocks that do not include the transaction will be invalid). However, this has the problem that (i) proof of availability is very hard to implement, and (ii) colluding censors can simply require the submission of a transaction alongside its proof. In general, any protocol will likely be vulnerable to "colluding censors can simply require a zero-knowledge proof of X", but the challenge is making such a requirement maximally inconvenient for all parties to implement.

プライバシー
Privacy

Blockchains are often hailed as an alternative to centralized networks that rely on trusting specific parties. However, one of the major areas in which people actually do strongly distrust centralized parties in real life is in the area of privacy, and blockchain protocols by themsleves are even more public than centralized systems. Hence, blockchain-based solutions to privacy will likely need to go a bit beyond simply creating a decentralized database and being done with it; effort must be made into creating protocols for maximally broad categories of applications (eg. payments, financial contracts, identity and reputation info publication) that combine blockchain-based data publication with protocols that preserve individual privacy, and allow individuals maximum freedom in what facts about themselves to expose to what parties.

この問題の副次的な問題は以下を含む
Sub-problems of this problem include:

完全に匿名な通貨（現在はZerocashがある。Zerocashの論証可能な大きな欠点は、xkSNARKsを使っていることがら生じる90秒の間トランザクションの署名の時間を必要としていることだ。）
Privacyが担保された名声のシステムはユーザーが”Zero-knowledgeのreputationの証明"システムを作ることが出来るようにし、ターゲットの望ましい数値に応じて幾らかの名声のスコアを持つことが出来、しかし最小の他の個人的な情報を晒していることだ。

対象が求める基準に従ってreputation scoreを持つことが、最小の個人的な情報だけを公開する事で出来る
"zero-knowledge reputation certificates"を作ることが出来るようにしているプライバシーが担保されたreputationシステム

A privacy-preserving reputation system that allows users to create "zero-knowledge reputation certificates" proving that they have some reputation score according to the target's desired metric but revealing minimal other personal information

ユーザーがそのコントラクトのコードとコンテントを最大限可能な限り隠せるようにしている金融のコントラクトのシステム

最大限プライバシーを保護している分散形の internet of thingsのプラットフォーム
A maximally privacy-preserving decentralized internet-of-things platform
