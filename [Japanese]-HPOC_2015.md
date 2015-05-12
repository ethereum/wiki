HPoC 2015

昨年私達は、暗号通貨とCryptoeconomic のテクノロジーが主流のものとなるのを妨げている
幾つかの技術的、経済的な難しい問題を叙述し、"Hard Problems of Cryptocurrency"の文書を出しました。

そのリストには、幾つもの他の困難とともに例えばスケーラビリティや、より効率的なコンセンサスのアルゴリズム、公益となるインセンティブの仕組みを叙述しました。

昨年に渡って多くの問題が解決されました、少なくとも追加された更新はもはや、0から1への解決が必要な根本的な問題ではなくなり、
むしろ現実世界への確かな実装の問題となった。
そして、同時に、かなりの数の試練が未だに残っている。それにもかかわらずここにある問題はかなり限定的d、
過去のものよりも広域に渡るものではない。

メタコンセンサス

技術的な視野が急速に広がることに関連し続けるために、ソフトウェアは更新されなければならず、
更新しないソフトウェアは代替手段が無いものの、ゆっくりとより優れた技術へと取って代わられて死に絶える。
このことは何故この問題が分散形の暗号プロトコルの同様に当てはまるべきでないのかの理由とはならない。
しかしながら分散形の暗号システムは自然な疑問へと結びつく。誰が更新のプロセスをコントロールするのだろうか？
このことを書いている間に、Bitcoinのコミュニティはブロックサイズを1MBから20MBに増やすかどうかを決定するプロセスを辿っていて、意図的に機関のものによる意思決定のシステムが、既定意思決定のプロセスがなされる状況へと結びつくことになっていて、
コンポネt機に政治的な議論が小数のコアの開発者の中で行われている。

内部でのメタコンセンサスのプロトコルは有るだろうか、ー即ち、プロトコルの更新に対しての合意形成を円滑化する、
プロトコルの内部にあるメカニズムはあるだろうか？もしかすると、

In order to remain relevant in a rapidly evolving technological landscape, software must update, and software that does not update has no alternative but to slowly fade away and die as it is replaced by superior technology. There is no reason why this principle should not apply to decentralized crypto-protocols as well. However, the need to make upgrades to decentralized crypto-systems leads to a natural question: who controls the updating process? As of the time of this writing, the Bitcoin community is going through the process of deciding whether or not to increase the maximum block size from 1 MB to 20 MB, and the lack of a deliberately instituted decision-making process has lead to the situation where the de-facto decision-making process essentially amounts to political debate among a small number of core developers.

Is there a way of building intra-protocol meta-consensus - that is, a mechanism inside the protocol that facilitates agreement on upgrades to the protocol, perhaps using some kind of consensus voting? Such a system would have a number of benefits, including greater flexibility, less reliance on political processes that end up being centralization risks, and can arguably ensure a greater voice to all participants in the protocol, not just those that are socially well-connected (see Jo Freeman's The Tyranny of Structurelessness for more on this).

Desirable properties of meta-consensus include:

Incentives to agree on beneficial protocol changes
Some kind of guarantee that applications built under the old protocol will remain maximally effective under the new protocol. Note that a part of this will likely be some kind of object-oriented encapsulation mechanisms in order to steer developers away from using features which are highly protocol-specific
Resistance against the mechanism being corrupted in some sense and forced to lead to an undesirable protocol change
The ability for users to automatically upgrade to a newly chosen protocol without having to download a new client
Existing work on this includes Arthur Breitman's Tezos, Bitshares' delegated proof of stake, and the not-yet-released Ethereum 2.0 proposal.

Public Goods Preference Revelation and Incentivization

The other major problem in crypto-protocol development is the analogous funding problem: how will protocol upgrades, and more generally public-good ecosystem middleware, be paid for? In the initial phase of development, the concept of "crowdsales" has proven to be absolutely invaluable, providing millions of dollars of funding for efforts which would have otherwise been relegated to being little more than volunteer side projects. However, crowdsales by themselves have a fundamental problem: they are front-loaded, creating heavy incentives for short-term marketing but less so for long-term performance, and they are limited one-time events from which the funds received eventually run out. And even if the funds could somehow be managed so as not to run out, crypto-protocol development would be permanently centralized in a single organization. What would be ideal is, once again, some kind of intra-protocol mechanism for incentivizing the development of public goods.

Public goods incentivization has a long and established literature, including numerous results from mechanism design and assurance contract theory on the topic of both how to fund the development of public goods and on the equally important question of deciding which public goods are worth funding in the first place. In a blockchain context, the funding problem is somewhat easier than in a private-market context, because we have the ability to "tax" all currency holders via inflation and taking the excess of transaction fees minus consensus costs, but the preference revelation problem is much harder; a human government can try to use informal judgement to figure out which public goods are worth supporting and which ones are not, but a blockchain literally cannot tell the difference between a trustworthy development fund, an untrustworthy development fund, a tobacco lobbying company, the "zero address" from which coins can never be spent and a scammer. Hence, it must rely entirely on some kind of mechanism design to elicit this information from the community.

The challenge here is applying these guarantees to a blockchain context. Particular challenges include:

Being incentive-compatible even given the assumption that users can securely and trustlessly bribe each other, eg. via contracts (cooperative game theory can help here)
Being incentive-compatible even given the assumption that the attacker may have a superior ability to coordinate versus the individual participants (cf. P + epsilon attacks; cooperative game theory may be insufficient here as it generally assumes that collections of users can either coordinate totally or not coordinate at all)
Avoiding Caplanian "rational irrationality" critiques, ie. providing sufficiently large incentives for users to learn about which funds are worth supporting and not simply immediately click on what looks nicer
The general model can be thought of as follows:

There exist N public goods, represented by addresses owned by funds that promise to spend the money on particular public goods (eg. development). Anyone can register an address in this set, perhaps after paying some fee
Users need to, using some mechanism, select how to split up funding pool D between these goods (D may be a maximum size; the mechanism may end up "burning" some of D)
Attackers creating fake "public goods" that really just send money to themselves can be seen simply as being public goods with a payoff ratio of 1 that happens to be concentrated entirely in a single player. The reason why attackers winning is socially bad is that other "legitimate" public goods will have much higher payoff ratios
If necessary, we can rely on the users having security deposits, eg. being validators
Coercion-proof Voting

Ari Juels in 2001 came up with a protocol for "coercion-resistant electronic elections" - a way of doing voting online such that not only is it possible for users to vote without revealing to others who they voted for, but it is in fact not possible to prove to anyone else who you voted for even if you wanted to - making it impossible to coerce or bribe people to vote. One possible route to solving the preference revelation problem, as well as voting games in general, is to implement this kind of scheme, making it much harder to coordinate. It is by no means a complete solution, since a user that controls two identities can still have those two identities securely collude with each other, but combined with a wealth deconcentration assumption it would make systems substantially more robust.

The challenge is, is it possible to implement some kind of coercion-resistant electronic election into a decentralized public blockchain context? The primary challenge here is the complete lack of any specific parties that have private keys and can be trusted not to collude with each other; the system would need to be completely public.

Note: an alternative to making cryptographically coercion-resistant electronic elections is making cryptoeconomically coercion-resistant electronic elections: come up with some kind of mechanism such that if a voter proves to another party any nonzero quantity of information about who they voted for, that party can exploit them for an expected nonzero return at their expense (if this exploitation can be made to be unprovable, then even better, as it reduces the possibility of effective circumvention through reputation).

Anti-pre-revelation

There are many protocols such as Schellingcoin, Augur, N-of-N protocols in random number generation, etc, that rely on some notion of users first cryptographically "committing" to some value x, eg. by submitting H(x) for some hash function, and then in a later stage "revealing" the value for x that they submitted - with non-revelation or incorrect revelation being punishable by security deposit loss. However, many of these protocols only work effectively if users' answers during the first phase are private; if users can collude to share information, and particularly if they can provably share information, then the mechanism's effectiveness decreases as collusion becomes easier.

Suppose a commit-reveal protocol where all users must supply either 0 or 1, and they are "supposed to" randomly choose this value. In the first step, each user must pick a (irrelevant) salt s, and provide H([s, v]) where v is either 0 or 1. In the second step, the user must supply their values of s and v, and have them be checked against the commitment. The sum of all values v mod 2 is then taken as a single bit of random output. Here, hypothetically all players can talk to each other and provably coordinate on voting either 0 or 1. However, we can design an anti-pre-revelation protocol as follows: any party can choose to register a bet against any other party that they will vote on some specific value with at last a 60% probability (ie. if A bets that B will bet 0, then if B ends up betting 0 then B gets 2 units out of A's security deposit, and if B ends up betting 1 then A gets 3 units out of B's security deposit). Hence, if party P[i] reveals to party P[j] their v value, or even a zero-knowledge proof of their v value with more than 60% probability, they can be exploited.

A challenge is to either take this path and try to expand it into an environment where the potential value space is much larger and probabilities of voting may not be known a priori, or find some other approach to make it cryptographically impossible or cryptoeconomically expensive to reveal one's hidden value before one is supposed to. Tailoring to specific applications (eg. linear Schellingcoin price voting) is suboptimal but acceptable. A particular problem is the existence of zero-knowledge proofs: one can use zk-SNARKs (or simply plain ZKPs) in order to reveal any information about v and prove that this information is correct by using the provided hash H([s, v]) without revealing anything about s or any information about v that is undesired; hence, a solution should be secure against all kinds of partial information revelation that may be useful, and not simply revealing the exact value of v.

Random Number Generation

A number of protocols, including consensus protocols, blockchain-based lotteries, scalable sampling schemes, etc, require some kind of random number generation in the protocol. Proof of work provides an answer easily, because one cannot compute ahead of time what the result will be, and once a result is found not revealing it carries a large opportunity cost. However, proof of work is extremely expensive, requiring constant expenditure equal to the security margin.

An alternative is N-of-N commit-reveal, as exemplified in Tomlion's RANDAO protocol, works as follows:

n players P[1] ... P[n] with security deposits of size D in the protocol choose random values V[1] ... V[n] with 0 <= v[i] < 2**256, and P[i] submits H(V[i]) during round one.
In round two, every player who submitting a value H(V[i]) must submit V[i].
If all players submit their value correctly, then (V[1] + ... + V[n]) % 2**256 is taken as a random seed, and all players are rewarded with D * i where i is an interest rate (eg. 0.0001 per period).
If not all players submitted their value correctly, then all players who participated get D * i, all players who did not participate lose their entire deposit and are kicked out, and the remaining players repeat the game until eventually all N players cooperate and reveal their values.
If you are the last player to participate, then you can manipulate the value at a very high cost, and even then only by replacing it with a random value next time around (ie. one can avoid unfavorable values, but one cannot target favorable values or even choose between two known values). However, the mechanism has the limitations that (i) it relies on a non-collusion assumption, (ii) the randomness that it provides is not nearly as fast as proof of work randomness, making it useless for some specific intra-block applications where proof of work shines, and (iii) it can lead to honest players losing large amounts of money from a network attack. Mitigating problem (iii) necessarily involves exacerbating problem (ii), and vice versa.

The open-ended challenge is to come up with a mechanism inside of a cryptoeconomic context which provides random numbers as output with maximally relaxed security assumptions and maximal robustness and resilience to attackers - ideally, a mechanism with the same properties as proof of work but without (or with only a negligible fraction of) its cost.

Anti-censorship

Public blockchains particularly have a strong need to be censorship-resistant, ie. to be able to get transactions into the chain even if powerful parties do not want them included. This is important not just for controversial Wikileaks-style applications; even ordinary financial applications, commit-reveal protocols, etc, could have their security threatened if there was a way of effectively preventing "challenge" messages from entering the blockchain.

If the set of participants willing to collude to censor is smaller than 33%, then the problem is fairly trivial. If the set is greater than 33%, however, then we know that those participants have the power to shut down the network, albeit at very high cost to themselves. Hence, the problem becomes somewhat different: how do we make it impossible for colluding censors to censor specific transactions without censoring everything else as well?

One possible route to take is some kind of commit-reveal protocol, where users submit a hash of a transaction plus proof that the transaction is available, and then the transaction sender is somehow bound to include the transaction itself once it is revealed (ie. blocks that do not include the transaction will be invalid). However, this has the problem that (i) proof of availability is very hard to implement, and (ii) colluding censors can simply require the submission of a transaction alongside its proof. In general, any protocol will likely be vulnerable to "colluding censors can simply require a zero-knowledge proof of X", but the challenge is making such a requirement maximally inconvenient for all parties to implement.

Privacy

Blockchains are often hailed as an alternative to centralized networks that rely on trusting specific parties. However, one of the major areas in which people actually do strongly distrust centralized parties in real life is in the area of privacy, and blockchain protocols by themsleves are even more public than centralized systems. Hence, blockchain-based solutions to privacy will likely need to go a bit beyond simply creating a decentralized database and being done with it; effort must be made into creating protocols for maximally broad categories of applications (eg. payments, financial contracts, identity and reputation info publication) that combine blockchain-based data publication with protocols that preserve individual privacy, and allow individuals maximum freedom in what facts about themselves to expose to what parties.

Sub-problems of this problem include:

Fully anonymous currency (for existing work see Zerocash; Zerocash's largest current flaw is arguably the long 90s transaction signing period arising from its usage of zkSNARKs)
A privacy-preserving reputation system that allows users to create "zero-knowledge reputation certificates" proving that they have some reputation score according to the target's desired metric but revealing minimal other personal information
A financial contract system that allows users to hide the code and contents of their contracts to a maximal possible extent
A maximally privacy-preserving decentralized internet-of-things platform
