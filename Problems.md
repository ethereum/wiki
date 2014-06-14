## Hard Problems of Cryptoeconomics

Satoshi Nakamoto's Bitcoin was a marvelous invention. While much of cryptographic research since the 1980s has been delving into greater and greater depths of complexity and obscurity, developing protocols based on arcane constructions such as ideal lattices, elliptic curve pairing functions and fully homomorphic encryption, Bitcoin managed to solve a thirty-year-old problem in computer science using nothing but hashes and public keys. However, although the discovery was an amazing feat, it was only a first draft, and since that day we have learned a number of important insights about how cryptocurrency and digital consensus can be further improved. There are ever-present worries about mining centralization, problems in scalability, as well as challenges in the way of developing higher-level applications like reputation systems. Many of these problems will require yet another fundamental, zero-to-one, breakthrough in order to see much improvement. The purpose of this document is to identify specific problems and aspects of decentralized consensus architectures that can be improved, and coalesce them into a set of "problems" with reasonably formal definitions.

### Introduction to Cryptoeconomics

The advent of cryptocurrency brings a new, and interesting, dimension to cryptography. While before cryptography was, by and large, a purely computational and information-theoretic science, with strong guarantees built on security assumptions that are close to absolute, once money enters the picture the perfect world of mathematics must interact with a much more messy reality of human social structures, economic incentives and partial guarantees and known vulnerabilities that can only be mitigated, and not outright removed. While a cryptographer is used to assumptions of the form "this algorithm is guaranteed to be unbreakable provided that these underlying math problems remain hard", in the world of cryptocurrency there are such concepts as "51% attacks" and market manipulation attacks where the difficulty of the problem in question cannot be cleanly defined. Empirical factors such as the difficulty of collusion attacks, efficiency of specialized hardware and sub- or superlinearity of computing power versus wealth are parameters that require heavy real-world observation to understand, and the efficacy of a solution to a given problem can depend entirely on sociocultural circumstances.

In traditional applied cryptography, security assumptions tend to look something like this:

1. No one can do more than 2^79 computational steps
2. Factoring is hard (ie. superpolynomial)
3. Taking nth roots modulo composites is hard
4. The elliptic curve discrete logarithm problem cannot be solved faster than in 2<sup>n/2</sup> time

In cryptoeconomics, on the other hand, the basic security assumptions that we depend on are the following:

1. No set of individuals that control more than 25% of all computational resources is capable of colluding
2. No set of individuals that control more than 25% of all money is capable of colluding
3. The amount of computation of a certain proof of work function that can be accomplished with a given amount of money is not superlinear beyond a point which is reasonably low
4. There exist a non-negiligible number of altruists and a non-negligible number of crazies or political opponents of the system, and the majority of users can be reasonably modeled as being close to economically rational
5. The number of users of a system is large, and users can appear or disappear at any time, although at least some users are persistent
6. Censorship is impossible
7. It is trivial to generate a very large number of IP addresses, and one can purchase an unlimited amount of network bandwidth
8. Many users are anonymous, so negative reputations and debts are close to unenforceable

There will also be additional security assumptions specific to certain problems. Thus, quite often it will not even be possible to definitively say that a certain protocol is secure or insecure or that a certain problem has been solved. Rather, it will be necessary to create solutions that are optimized for particular empirical and social realities, and continue further and further optimizing them over time.

### Technology

The Bitcoin technology is impressive to a very large extent because of its simplicity. A 30-year-old problem in computer science was solved via a mechanism which is simple to implement, and so simple to understand that even some semi-technical teenagers can describe the entirety of how it works. However, at the same time the technology in its current form is very limited. The scalability in Bitcoin is very crude; the fact that every full node needs to process every transaction is a large roadblock to the future success of the platform, and a factor preventing its effective use in micropayments (arguably the one place where it is the most useful). Timestamping is flawed, and proof-of-computation algorithms are very limited in the types of computation that they can support. The fact that the original solution was so "easy", however, suggests that there is still a large opportunity to improve, and there are a number of directions in which improvement could be directed.

**1. Blockchain Scalability**

One of the largest problems facing the cryptocurrency space today is the issue of scalability. It is an often repeated claim that, while mainstream payment networks process something like 2000 transactions per second, in its current form the Bitcoin network can only process seven. On a fundamental level, this is not strictly true; simply by changing the block size limit parameter, Bitcoin can easily be made to support 70 or even 7000 transactions per second. However, if Bitcoin does get to that scale, we run into a problem: it becomes impossible for the average user to run a full node, and full nodes become relegated only to that small collection of businesses that can afford the resources. Because mining only requires the block header, even miners can (and in practice most do) mine without downloading the blockchain.

The main concern with this is trust: if there are only a few entities capable of running full nodes, then those entities can conspire and agree to give themselves a large number of additional bitcoins, and there would be no way for other users to see for themselves that a block is invalid without processing an entire block themselves. Although such a fraud may potentially be discovered after the fact, power dynamics may create a situation where the default action is to simply go along with the fraudulent chain (and authorities can create a climate of fear to support such an action) and there is a coordination problem in switching back. Thus, in the limit, Bitcoin with 7000 transactions per second has security properties that are essentially similar to a centralized system like Paypal, whereas what we want is a system that handles 7000 TPS with the same levels of decentralization that cryptocurrency originally promised to offer.

Ideally, a blockchain design should exist that works, and has similar security properties to Bitcoin with regard to 51% attacks, that functions even if no single node processes more than `1/n` of all transactions where `n` can be scaled up to be as high as necessary, although perhaps at the cost of linearly or quadratically growing secondary inefficiencies and convergence concerns. This would allow the blockchain architecture to process an arbitrarily high number of TPS but at the same time retain the same level of decentralization that Satoshi envisioned.

**Problem**: create a blockchain design that maintains Bitcoin-like security guarantees, but where the maximum size of the most powerful node that needs to exist for the network to keep functioning is substantially sublinear in the number of transactions.

**Additional Assumptions and Requirements**:

* There exist a large number of miners in the network
* Miners may be using specialized hardware or unspecialized hardware. Specialized hardware should be assumed to be more powerful than unspecialized hardware by a large (eg. 10000) constant factor at specific tasks.
* Ordinary users will be using unspecialized hardware
* Ideally, after some number of blocks (perhaps logarithmic in the total size of the network) every transaction should require 51% of network hashpower to reverse. However, solutions where transactions can pay very small fees for a lower "level" of security are acceptable, though one should take care to avoid situations where an attacker can profit by performing one attack to reverse very many small transactions at the same time
* Ideally, the solution should work for and maintain as many properties as possible of a generalized account-based blockchain (eg. Ethereum), though solutions specific to currency, domain registrations or other specialized use caes are acceptable

**2. Timestamping**

An important property that Bitcoin needs to keep is that there should be roughly one block generated every ten minutes; if a block is generated every day, the payment system becomes too slow, and if a block is generated every second there are serious centralization and network efficiency concerns that would make the consensus system essentially nonviable even assuming the absence of any attackers. To ensure this, the Bitcoin network adjusts difficulty so that if blocks are produced too quickly it becomes harder to mine a new block, and if blocks are produced too slowly it becomes easier. However, this solution requires an important ingredient: the blockchain must be aware of time. In order to solve this problem, Bitcoin requires miners to submit a timestamp in each block, and nodes reject a block if the block's timestamp is either (i) behind the median timestamp of the previous eleven blocks, or (ii) more than 2 hours into the future, from the point of view of the node's own internal clock. This algorithm is good enough for Bitcoin, because time serves only the very limited function of regulating the block creation rate over the long term, but there are potential vulnerabilities in this approach, issues which may compound in blockchains where time plays a more important role.

**Problem**: create a distributed incentive-compatible system, whether it is an overlay on top of a blockchain or its own blockchain, which maintains the current time to high accuracy.

**Additional Assumptions and Requirements**

* All legitimate users have clocks in a normal distribution around some "real" time with standard deviation 20 seconds.
* No two nodes are more than 20 seconds apart in terms of the amount of time it takes for a message originating from one node to reach any other node.
* The solution is allowed to rely on an existing concept of "N nodes"; this would in practice be enforced with proof-of-stake or non-sybil tokens (see #9).
* The system should continuously provide a time which is within 120s (or less if possible) of the internal clock of >99% of honestly participating nodes. Note that this also implies that the system should be self-consistent to within about 190s.
* The system should exist without relying on any kind of proof-of-work.
* External systems may end up relying on this system; hence, it should remain secure against attackers controlling < 25% of nodes regardless of incentives.

**3. Arbitrary Proof of Computation**

Perhaps the holy grail of the study zero-knowledge proofs is the concept of an arbitrary proof of computation: given a program P with input I, the challenge is to create a zero-knowledge proof that you ran P with input I and received output O, such that the proof can be verified quickly (ie. in polylogarithmic or ideally constant time) even if the original computation took a very large number of steps to complete. In an ideal setup, the proof would even hide the value of I, just proving that you ran P with some output with result O, and if I needs to be made public it can be embedded into the program. Such a primitive, if possible, would have massive implications for cryptocurrency:

1. The blockchain scalability problem would be much easier to solve. Instead of miners publishing blocks containing a list of transactions, they would be publishing a proof that they ran the blockchain state updater with some list of transactions and produced a certain output; thus, instead of transactions needing to be verified by every node in the network, they could be processed by one miner and then every other miner and user could quickly verify the proof of computation and if the proof turns out correct they would accept the new state.
2. The blockchain privacy problem would essentally be solved. The blockchain scalability solution above would hide the details behind individual transactions; it would only reveal the fact that all of them are legitimate, so transactions would be hidden from everyone but the sender and the receiver.
3. It would become computationally viable to use a Turing-complete consensus network as a generic distributed cloud computing system; if you have any computation you wanted done, you would be able to publish the program for miners and miners would be able to run the program for you and deliver the result alongside a proof of its validity.

There is a large amount of existing research on this topic, including a protocol known as "SCIP" (succinct computational integrity and privacy) or "zkSNARK" that is already working in test environments, although with the limitation that a trusted third party is required to initially set up the keys; use of this prior work by both its original developers and others is encouraged.

**Problem**: create programs `POC_PROVE(P,I) -> (O,Q)` and `POC_VERIFY(P,O,Q) -> { 0, 1 }` such that `POC_PROVE` runs program `P` on input `I` and returns the program output `O` and a proof-of-computation `Q` and `POC_VERIFY` takes `P`, `O` and `Q` and outputs whether or not `Q` and `O` were legitimately produced by the `POC_PROVE` algorithm using `P`.

**Requirements And Additional Assumptions**

* The runtime of `POC_PROVE` should be in `O(n*polylog(n))` where `n` is the number of steps required to run the program.
* The runtime of `POC_VERIFY` should be either constant or logarithmic in the number of steps, and at most linear in the maximum memory usage of the program.
* The protocol should require no trusted third parties. If TTPs are required, the protocol should include a mechanism for simulating one efficiently using secure multiparty computation.

### Consensus

One of the key elements in the Bitcoin algorithm is the concept of "proof of work". In any Byzantine-fault-tolerant system, the security level is often defined as the minimum percentage of hostile nodes - for example, in the context of secret sharing, the Berlekamp-Welch algorithm with 2x redundancy is guaranteed to provide the correct output assuming that the total number of hostile nodes does not exceed 25% of the network, and in the context of Bitcoin mining the requirement is that the size of the set of honest nodes exceeds the size of any individual hostile coalition. However, all of these security guarantees have one important qualification: there must be some way to define what an individual node is. Before Bitcoin, most fault-tolerant algorithms had high computational complexity and assumed that the size of the network would be small, and so each node would be run by a known individual or organization and so it is possible to count each node individually.

With Bitcoin, however, nodes are numerous, mostly anonymous, and can enter or leave the system at any time. Unless one puts in careful thought, such a system would quickly run into what is known as a Sybil attack, where a hostile attacks simply creates five times as many nodes as the rest of the network combined, whether by running them all on the same machine or rented virtual private server or on a botnet, and uses this supermajority to subvert the network. In order to prevent this kind of attack, the only known solution is to use a resource-based counting mechanism. For this purpose, Bitcoin uses a scheme known as proof-of-work, which consists of solving problems that are difficult to solve, but easy to verify. The weight of a node in the consensus is based on the number of problem solutions that the node presents, and the Bitcoin system rewards nodes that present such solutions ("miners") with new bitcoins and transaction fees.

Bitcoin's proof of work algorithm is a simple design known as Hashcash, invented by Adam Back in 1995. The hashcash function works as follows:

    def hashcash_produce(data, difficulty):
        nonce = random.randrange(2**256)
        while sha256(data + str(nonce)) > 2**256 / difficulty:
            nonce += 1
        return nonce

    def hashcash_verify(data, nonce, difficulty):
        return sha256(data + str(nonce)) <= 2**256 / difficulty

Note that in the actual Bitcoin protocol nonces are limited to 32 bits; at higher difficulty levels, one is required to also manipulate transaction data in the block as a sort of "extranonce".

Originally, the intent behind the Bitcoin design was very egalitarian in nature. Every individual would mine on their own desktop computer, producing a highly decentralized network without any point of control and a distribution mechanism that spread the initial supply a BTC across a wide number of users. And for the first 18 months of Bitcoin's existence, the system worked. In the summer of 2010, however, developers released a Bitcoin miner that took advantage of the massive parallelization offered by the graphics processing unit (GPU) of powerful computers, mining about 10-50 times more efficiently than CPUs. In 2013, specialization took a further turn, with the introduction of devices called "application-sepcific integrated circuits" - chips designed in silicon with the sole purpose of Bitcoin mining in mind, providing another 10-50x rise in efficiency. CPU and GPU mining are now completely unprofitable, and the only way to mine is to either start a multimillion-dollar ASIC manufacturing company or purchase an ASIC from one that already exists.

Another related issue is mining pool centralization. Theoretically, the legitimate function of a mining pool is simple: instead of mining on their own and receiving a small chance of earning the block reward of 25 BTC, miners mine for a pool, and the pool sends them a proportionate constant payout (eg. 0.002 BTC per block). There are centralized mining pools, but there are also P2P pools which serve the same function. However, P2P pools require miners to validate the entire blockchain, something which general-purpose computers can easily do but ASICs are not capable of; as a result, ASIC miners nearly all opt for centralized mining pools. The result of these trends is grim. Right now, nearly 25% of all new ASIC hashpower is produced in a single factory in Shenzhen, and nearly 50% of the network is controlled by a single mining pool.

The second problem is easy to alleviate; one simply creates a mining algorithm that forces every mining node to store the entire blockchain. The first problem, that of mining centralization, is much harder. There is the possibility that the problem will solve itself over time, and as the Bitcoin mining industry grows it will naturally become more decentralized as room emerges for more firms to participate. However, that is an empirical claim that may or may not come to pass, and we need to be prepared for the eventuality that it does not. Furthermore, the wasted energy and computation costs of proof of work as they stand today may prove to be entirely avoidable, and it is worth looking to see if that aspect of consensus algorithms can be alleviated.

**4. ASIC-Resistant Proof of Work**

One approach at solving the problem is creating a proof-of-work algorithm based on a type of computation that is very difficult to specialize. One specific ideas involves creating a hash function that is "memory-hard", making it much more difficult to create an ASIC that achieves massive gains through parallelization. This idea is simple, but fundamentally limited - if a function is memory-hard to compute, it is also generally memory-hard to verify. Additionally, there may be ways to specialize hardware for an algorithm that have nothing to do with hyperparallelizing it. Another approach involves randomly generating new mining functions per block, trying to make specialization gains impossible because the ASIC ideally suited for performing arbitrary computations is by definition simply a CPU. There may also be other strategies aside from these two.

Ultimately, perfect ASIC resistance is impossible; there are always portions of circuits that are going to be unused by any specific algorithm and that can be trimmed to cut costs in a specialized device. However, what we are looking for is not perfect ASIC resistance but rather economic ASIC resistance. Economic ASIC resistance can be defined as follows. First of all, we note that in a non-specialized environment mining returns are sublinear - everyone owns one computer, say with N units of unused computational power, so up to N units of mining cost only the additional electricity cost, whereas mining beyond N units costs both electricity and hardware. If the cost of mining with specialized hardware, including the cost of research and development, is higher per unit hashpower than the cost of those first N units of mining per user then one can call an algorithm economically ASIC resistant.

**Problem**: Create two functions, `PoWProduce(data,diff) -> nonce` and `PoWVerify(data,nonce,diff) -> { 0, 1 }`, to serve as alternatives to Hashcash such that it is economically unattractive to produce an ASIC for `PoWProduce`

**Additional Assumptions And Requirements**:

* `PoWProduce` must have expected runtime linear in `diff`
* `PoWVerify` must have runtime at most polylogarithmic in `diff`
* Running `PoWProduce` should be the most efficient, or very close to the most efficient, way to produce values that return `1` when checked with `PoWVerify` (ie. no software optimization)
* `PoWProduce` must not be superlinear in computational power or time; that is to say, the expected number of successful `PoWProduce` computations for a node with `N` dollars worth of hardware after `t` seconds should be bounded by `kNt` for some `k`. Furthermore, the linearity should kick in quickly; ie. $1000 worth of mining hardware should function with over 90% efficiency.
* It should be shown with reasonably rigorous technological and economic analysis that the algorithm is economically ASIC resistant.

**5. Useful Proof of Work**

Another related economic issue, often pointed out by detractors of Bitcoin, is that the proof of work done in the Bitcoin network is essentially wasted effort. Miners spend 24 hours a day cranking out SHA256 (or in more advanced implementations Scrypt) computations with the hopes of producing a block that has a very low hash value, and ultimately all of this work has no value to society. Traditional centralized networks, like Paypal and the credit card network, manage to get by without performing any proof of work computations at all, whereas in the Bitcoin ecosystem about a million US dollars of electricity and manufacturing effort is essentially wasted every day to prop up the network.

One way of solving the problem that many have proposed is making the proof of work function something which is simultaneously useful; a common candidate is something like Folding@home, an existing program where users can download software onto their computers to simulate protein folding and provide researchers with a large supply of data to help them cure diseases. The problem is, however, that Folding@home is not "easy to verify"; verifying the someone did a Folding@home computation correctly, and did not cut corners to maximize their rounds-per-second at the cost of making the result useless in actual research, takes as long as doing the computation oneself. If either an efficiently verifiable proof-of-computation for Folding@home can be produced, or if we can find some other useful computation which is easy to verify, then cryptocurrency mining could actually become a huge boon to society, not only removing the objection that Bitcoin wastes "energy", but even being socially beneficial by providing a public good.

Note that there is one major concern with this approach that has been identified: if the useful PoW is implemented incorrectly, it can potentially reduce the cost of an attack on the network. If the useful PoW is useful in such a way that it is sometimes economically viable for certain very large entities to perform the computation even without the currency incentive, then those entities have an incentive to launch attacks against the network at no cost, since they would be performing the computations anyway. One simple, though crude and imperfect, way of addressing this problem is to make the PoW a half-and-half mix between useful and useless, making the cost of an attack at least 50% of what it would be in a useless-PoW environemnt. In practice, the overhead of making PoW verifiable may well introduce over 2x inefficiency unintentionally. Another economic solution is to make the computation a "pure" public good such that no individual entity derives a significant benefit from it. Proposed solutions to this problem should include a rigorous analysis of this issue.

**Problem**: Create two functions, `PoWProduce(data,diff) -> nonce` and `PoWVerify(data,nonce,diff) -> { 0, 1 }`, to serve as alternatives to Hashcash such that the outputs of `PoWProduce` are independently useful.

**Requirements**

* `PoWProduce` must have expected runtime linear in `diff`
* `PoWVerify` must have expected runtime at most polylogarithmic in `diff`
* Running `PoWProduce` should be the most efficient way to produce values that return `1` when checked with `PoWVerify`
* `PoWProduce` must not be superlinear in computational power or time; that is to say, the expected number of successful `PoWProduce` computations for a node with `N` dollars worth of hardware after `t` seconds should be bounded by `kNt` for some `k`. Furthermore, the linearity should kick in quickly; ie. $1000 worth of mining hardware should function with over 90% efficiency.
* `PoWProduce` must produce a public good, such that the total value to everyone of the public good produced is greater than the cost of all resources invested into the mining process.
* The system must be able to exist without a trusted third party, but it is reasonable to allow a trusted third party to serve as a data source for useful computations. If the trusted third party acts maliciously in any way, the public good may be negated but the blockchain mining should not be compromised.

**6. Proof of Stake**

Another approach to solving the mining centralization problem is to abolish mining entirely, and move to some other mechanism for counting the weight of each node in the consensus. The most popular alternative under discussion to date is "proof of stake" - that is to say, instead of treating the consensus model as "one unit of CPU power, one vote" it becomes "one currency unit, one vote".

A very simple proof of stake algorithm requires the miner mining the block to sign it with the private key to the address holding their coins, where the block is valid if `sha256(PREVHASH + ADDRESS + TIMESTAMP) <= 2^256 * BALANCE / DIFFICULTY` where `PREVHASH` is the hash of the previous block, `ADDRESS` is the signer's address with balance `BALANCE`, `TIMESTAMP` is the current Unix time in seconds and `DIFFICULTY` is an adjustable parameter to regulate the frequency of successful signatures. At first glance, this algorithm has the basic required properties: every miner has some random chance per second of succeeding, and if your address has twice as much money in it then you have double the chance of success.

However, this algorithm has one important flaw: there is "nothing at stake". In the event of a fork, whether the fork is accidental or a malicious attempt to rewrite history and reverse a transaction, the optimal strategy for any miner is to mine on every chain, so that the miner gets their reward no matter which fork wins. Thus, assuming a large number of economically interested miners, an attacker may be able to send a transaction in exchange for some digital good (usually another cryptocurrency), receive the good, then start a fork of the blockchain from one block behind the transaction and send the money to themselves instead, and even with 1% of the total stake the attacker's fork would win because everyone else is mining on both.

Another problem to keep in mind is the issue of so-called "long-range attacks" - attacks where the miner attempts to start a fork not five or ten blocks behind the head of the main chain, as happens normally, but hundreds of thousands of blocks back. If an algorithm is designed incorrectly, it may be possible for an attacker to start from that far back, and then mine billions of blocks into the future (since no proof of work is required), and new users would not be able to tell that the blockchain with billions of blocks more is illegitimate. This can generally be solved with timestamping, but special corner cases do tend to appear in overcomplicated designs.

The Slasher algorithm, described [here](http://blog.ethereum.org/2014/01/15/slasher-a-punitive-proof-of-stake-algorithm/) and implemented by Zack Hess as a proof-of-concept [here](https://github.com/zack-bitcoin/slasher), represents my own attempt at fixing the nothing-at-stake problem. The core idea is that (1) the miners for each block are determined ahead of time, so in the event of a fork a miner will either have an opportunity to mine a given block on all chains or no chains, and (2) if a miner is caught signing two distinct blocks with the same block number they can be deprived of their reward. The algorithm is viable and effective, but it suffers from two flaws of unknown significance. First, if all of the miners for a given block learn each other's identities beforehand, they can meet up and collude to shut down the network. Second, the nothing-at-stake problem remaing for attacks going back more than 3000 blocks, although this is a smaller issue because such attacks would be very obvious and can automatically trigger warnings.

**Problem**: create a proof-of-stake algorithm that solves the nothing-at-stake problem and long-range attack problems, without introducing new collusion risks that require less than 25% of stakeholders to succeed.

**Additional Requirements And Assumptions**

* The expected return from mining should be bounded by `k` times the miner's stake for some `k`, and assuming $1 billion total participating stake a stake of $1000 should be able to reach 90% of this maximum efficiency.
* The algorithm should be fully incentive-compatible, addressing the double-voting issue defined above and the collusion issue defined above at both short and long range.

**7. Proof of research**

One interesting, and largely unexplored, solution to the problem of distribution speifically (there are reasons why it cannot be so easily used for mining) is using tasks that are socially useful but require original human-driven mathematical or algorithmic research. For example, one can come up with a "proof of proof" currency that rewards players for coming up with mathematical proofs of certain theorems. There is no generic algorithm, aside from brute force, for proving theorems, and yet proofs of theorems are theoretically computationally easy to verify: one simply needs to write every step of the proof in a formal language, allowing the use of only one inference rule (eg. `a + b = b + a` or `a * (b + c) = a * b + a * c` but not `a * (b + c) = a * c + b * a`) between each step, and having a program verify the correctness of the inferences at each step.

For example, a proof of a common algebraic factorization problem appears as follows:

      a^4 + 4
    = a^4 - 2*a^3 + 2*a^3 + 4
    = a^4 - 2*a^3 + 2*a^3 + 4*a^2 - 4*a^2 + 4
    = a^4 - 2*a^3 + 2*a^3 + (2+2)*a^2 - 4*a^2 + 4
    = a^4 - 2*a^3 + 2*a^3 + 2*a^2 + 2*a^2 - 4*a^2 + 4
    = a^4 - 2*a^3 + 2*a^3 + 2*a^2 + 2*a^2 - 4*a^2 + 4
    = a^4 - 2*a^3 + 2*a^2 + 2*a^3 + 2*a^2 - 4*a^2 + 4
    = a^4 - 2*a^3 + 2*a^2 + 2*a^3 - 4*a^2 + 2*a^2 + 4
    = a^4 - 2*a^3 + 2*a^2 + 2*a^3 - 4*a^2 + 2*a^2 + 4*a - 4*a + 4
    = a^4 - 2*a^3 + 2*a^2 + 2*a^3 - 4*a^2 + 4*a + 2*a^2 - 4*a + 4
    = a^2*a^2 - a^2*2*a + a^2*2 + 2*a*a^2 - 2*a*2*a + 2*a*2 + 2*a^2 - 2*2*a + 2*2 (doing many steps in parallel)
    = a^2*(a^2 - 2*a + 2) + 2*a*a^2 - 2*a*2*a + 2*a*2 + 2*a^2 - 2*2*a + 2*2
    = a^2*(a^2 - 2*a + 2) + 2*a*(a^2 - 2*a + 2) + 2*a^2 - 2*2*a + 2*2
    = a^2*(a^2 - 2*a + 2) + 2*a*(a^2 - 2*a + 2) + 2*(a^2 - 2*a + 2)
    = (a^2 + 2*a + 2)*(a^2 - 2*a + 2)

Each step of the proof can be verified using pattern matching algorithms; however, it would be very hard for a computer to make the inference that we need to add and subtract `2*a^3 + 2*a^2 + 2*a^2 + 4*a` in order to make the proof work (technically, in this case specialized algorithms can do it, but in more general cases especially involving second-order logic it becomes intractable). The question is, can either a proof of proof, or perhaps a proof of making progress on a given artificial intelligence problem, be used as a viable means of mining and distribution?

**Problem**: create a proof-of-excellence distribution/mining mechanism that rewards solving research problems that have definite direct or indirect positive impact in an area of research that has benefit to humanity.

**Additional Assumptions and Requirements**

* Given a well-justified extrapolation of the global levels of human and computer competence at the underlying problem, over 75% of the voting shares in the system should be provided by human labor, either unaided or aided only by commodity hardware and open-source software.
* The algorithm must ideally be future-proof; that is to say, it must continue rewarding value production in the long term and should not be an area that will eventually be "solved" completely.

### Economics

The second part of cryptoeconomics, and the part where solutions are much less easy to verify and quantify, is economics. Cryptocurrencies are not just cryptographic systems, they are also economic systems, and both kinds of security need to be taken into account. Sometimes, cryptographic security may even be slightly compromised in favor of an economic approach - if a signature algorithm takes more effort to crack than one could gain from cracking it, that is often a reasonable substitute for true security. At the same time, economic problems are also much more difficult to define. One cannot usually definitively know whether or not a problem has been solved without extensive experimentation, and the result will often depend on cultural factors or the other organizational and social structures used by the individuals involved. However, if the economic problems can be solved, the solutions may often have reach far beyond just cryptocurrency.

**9. Stable-value cryptoassets**

One of the main problems with Bitcoin is the issue of price volatility. The value of a bitcoin often experiences very large fluctuations, rising or falling by as much as 25% in a single day and 3x in a month. The main economic reason behind this is that the supply of bitcoins is fixed, so its price is directly proportional to demand (and therefore, by efficient market hypothesis, the expected discounted future demand), and demand is very unpredictable. It is not known if Bitcoin will be simply a niche payment method for transcations requiring a high degree of privacy, a replacement for Western Union, a mainstream consumer payment system or the reserve currency of the world, and the expected value of a bitcoin differs over a thousandfold between these various levels of adoption. Furthermore, the utility of the Bitcoin protocol is heavily dependent on the movements of the Bitcoin price (ie. people are interested in Bitcoin more if the price is going up), creating a positive feedback loop, which has arguably been responsible for both Bitcoin's great meteoric rises and its many-month-long periods of rapid decline.

To solve this problem, there are generally two paths that can be taken. The first is to have the network somehow detect its current level of economic usage, and have a supply function that automatically increases supply when usage increases. This reduces uncertainty; even though the expected future level of adoption of the protocol may have a variance of 10-100x, the circumstance where adoption increases 100x will also have 100x more supply and so the value of the currency will remain the same. There is a problem that if usage decreases there is no way to remove units from circulation, but even still the lack of upward uncertainty should reduce upward volatility, and downward volatility would also naturally reduce because it is no longer bad news for the value of the currency when an opportunity for increased usage is suddenly removed. Furthermore, in the long term the economy can be expected to grow, so the zero-supply-growth floor may not even ever be reached in practice. The problem is that measuring an economy in a secure way is a difficult problem. The most obvious metric that the system has access to is mining difficulty, but mining difficulty also goes up with Moore's law, and there is no known way to estimate the impact of Moore's law alone and so the currency cannot know if its difficulty increased by 10x due to better hardware, a larger user volume or a combination of both. Other metrics, such as transaction count, are potentially gameable by entities that want the supply to change in a particular direction (generally, holders want a lower supply, miners want a higher supply).

Another approach is to attempt to create a currency which tracks a specific asset, using some kind of incentive-compatible scheme likely based on the game-theoretic concept of Schelling points, to feed price information about the asset into the system in a decentralized way. This could then be combined with a supply function mechanism as above, or it can be incorporated into a zero-total-supply currency system which uses debts collateralized with other cryptographic assets to offset its positive supply and thus gain the ability to grow and shrink with changes to usage in either direction. The problem here is constructing the scheme in such a way that there is no incentive for entities to feed in false price information in order to increase or decrease the supply of the asset in their favor.

**Problem**: construct a cryptographic asset with a stable price.

**Requirements**

* The expected root-mean-square daily change in the logarithm of the price of the asset should be less than 25% of that of Bitcoin under similar conditions. Ideally, the asset should be guaranteed to maintain a value within 10% of an arbitrary cryptographic or real-world asset for which price information is easily accessible
* The expectation analysis should take into account black swan risks (ie. systems where the variance is 0% 99% of the time but 10x in a day the other 1% of the time are unacceptable)
* The solution must come with a model, including parameters such as short-term-consumption purchases, medium-term purchases, speculative purchases, positive and negative media, adoption and regulatory events, irrational actors and actors with political motives, show that their model well fits the history of Bitcoin and potentially major altcoins without overfitting, and show that under the model the other two requirements hold
* Zero-total-supply assets, ie. assets where each unit is balanced by a collateralized debt of a unit, are allowed, although such systems must include a robust margin-calling mechanism because it is assumed that most users are anonymous and can therefore trivially run away from debts

**10. Reputation systems** 

One very important ingredient in any successful cryptoeconomy is a functional reputation system. In the context of a trade between two often anonymous, or even automated, parties in different jurisdictions, a trust mechanism is absolutely crucial to ensure that honest participants are not so excessively scammed by the dishonest that participation ceases to be worth it. Especially in the context of an asynchronous trade, where party first A sends money to party B and then waits for party B to send A a product, in a naive marketplace implementation the incentive is obvious for B to simply never ship. Ordinarily, this problem is solved by persistent identities; B can make a few honest trades, perhaps at a steep discount, in order to establish his reliability, and then B can be said to possess a reputation `R` of value `Rv`. If A then purchases product P with production cost `Pv < Rv`, then the incentive is for B to follow through because if he does not then he loses `Rv` whereas if he does he only loses `Pv` (in most cases, the amount that A pays to B is between `Pv` and `Rv`, so honesty is profitable and dishonesty is not).

However, in the above setup there are many problems:

1. How do we know how much to increase someone's reputation after a particular interaction? A common attack on informal reputation systems is the "long con" - act honestly but passively and cheaply for a very long time, accumulate trust, and then suddenly go all out and destructively capitalize on one's reputation as much as possible. The initial dormant phase is cheap for the attacker, but ends up resulting in the attacker accumulating a disproportionately large amount of trust for the community and thereby ultimately causing much more damange than good. Overcompensate for this too much, however, and there ends up being no opportunity to gain trust.
2. How do we incorporate secondary trust? In general, when `A` is deciding whether or not to trust `B`, `A` has not had any prior dealings with `B`, and therefore has no way of knowing whether or not `B` is trustworthy. One approach is to just look at all ratings for `B`, but then we run into the issue of Sybil attacks: what if `B` creates 50000 fake users, all of whom rate each other highly, to give good ratings to him? To solve this problem, reputation systems rely on a fallback known as a web of trust: find some chain of people `P[1] ... P[k]` such that `A` trusts `P[1]`, `P[i]` trusts `P[i+1]` for all `i`, and `P[k]` trusts `B`. Under the "six degrees of separation hypothesis", any two people in the world except those completely disconnected from society have such a chain of maximum length `k = 5` (so at most six hops total). However, the question arises, if `A` has a certain rating for `P[1]` and `P[1]` has a certain rating for `B`, what should the reputation system recommend to `B`? Systems that are too aggressive risk allowing malicious users to accumulate exponential amounts of reputation with linear effort through self-reinforcing cycles, and systems that are too conservative are ineffective because they tell people not to trust anyone at all.
3. How do we deal with double use attacks? Specifically, suppose that `A` has a reputation with value `Rv = $1000`. Using this reputation, `A` has a business dealing where `P[1]` trusts her for $600. Then, she simultaneously engages in such a dealing with `P[2], P[3] ... P[10]`, each of whom individually believe that `A` will not betray them since $600 < $1000, and then runs away with $6000 taking the $1000 hit from the value of her reputation. How do we prevent such fractional reserve-like scenarios?
4. Is `Pv < Rv` a good enough criterion for economically justified trust? Specifically, if `B` chooses to run away, he might deny that he did so and claim that he sent the product, and it is `A` that is lying. Neither side may be able to prove their case, so `B` may remain trustworthy in the eyes of many.
5. Sometimes, it's possible to cheat without the fact that cheating was done being apparent for a long time (eg. using inferior-quality ingredients)

Currently, "pure" cryptoeconomies tend to be mostly darknet environments like Silk Road and TorWallet; the most interesting examples of existing reputation systems succeeding and failing are among buyers, sellers and operators on such sites (for example, a result that can easily be derived from economic principles is that anonymous financial markets tend to disappear much more often than anonymous markets). In the future, however, cryptoeconomics will begin to deal much more heavily in an environment where human intervention is impractical for a completely different reason: autonomous agents. For example, reputation systems are important in incentivized cloud computing and networking protocols, and on the larger scale it is entirely possible to expect autonomous organizations to eventually engage in commerce with each other.

**Problem**: design a system which generates a trust probability score `rep(A,B,R) -> P` where `A` is the trusting party, `B` is the trusted party, `R` is the amount that `B` is trusted with, and `P` is the probability that the interaction will go well.

**Additional Assumptions**

* The system has access to a record of all finalized transactions inside the system and all transactions in progress, although entities are of course able to choose to make deals outside the system
* It is allowed to introduce mechanisms like charity donations and sacrifices as a way of increasing one's reputation
* All interactions are of the form "A pays, then B sends the product, and A receives". There is no possibility for A to receive negative utility from an interaction (eg. food poisoning) and there is no concept of quality; when A receives the product it is immediately obvious that it is either junk or exactly what A expected

**Requirements**

* The system should continue to be reasonably accurate whether the parties involved are simple programs (eg. micropayment software protocols), more complicated AIs, DAOs, individual humans or human centralized or decentralized organizations
* A success metric for the system can be defined as the sum over all transactions of `V * (S * log(p) - (1-S) * log(1-p))`, where `S = 1` if the transaction succeeded and `S = 0` if there was a registered complaint, `p` is the assigned probability and `V` is the value of the transaction. The objective is to maximize this metric.

**11. Anti-Sybil systems**

A problem that is somewhat related to the issue of a reputation system is the challenge of creating a "unique identity system" - a system for generating tokens that prove that an identity is not part of a Sybil attack. The naive form of anti-Sybil token is simple: a sacrifice or proof of deposit. In a sacrifice setup, such identities simply cost $X, and in a PoD system identities require a deposit of $Y in order to be active, where perhaps the deposit can be taken away or destroyed under certain circumstances. However, ideally we would like to have a system that has nicer and more egalitarian features than "one-dollar-one-vote". To date, there have been two strategies to this end:

1. **Proof of humanity** - also known as CAPTCHAs. CAPTCHAs are temporarily effective, and have the advantage that they provide proof-that-you-are-a-human directly, but the problem is that, so far, all CAPTCHAs that have been created either only work for a certain subclass of very specialized and skilled humans (eg. playing Go) or are eventually broken (eg. CAPTCHAs with OCR).
2. **Social proof** - best seen with invite-only social networks, especially those where the support of multiple individuals is required in order to enter. 

A major concern with cryptocurrencies is the problem of distribution. Originally, Bitcoin was intended to be widely distributed, giving everyone with a CPU the ability to acquire bitcoins. Of course, even that approach was imperfect; the poorest people in the world rely on low-power electronics such as smartphones or OLPC laptops, which simply have no chance at competing with high-power laptops and desktops even in a CPU mining economy. Furthermore, the development of professional mining rigs and more recently specialized hardware devices now ensures that only individuals with a substantial amount of economic resources can participate. Essentially, one's ability to earn bitcoins in the modern economy is linearly proportional to the amount of wealth that one already has at best, and superlinear in one's wealth at worst. Ideally, a cryptocurrency would have a more egalitarian way of distributing itself, and preferably one which is resistant against potential attacks in the long term so that the currency can be fairly distributed to future generations as well as the present.

One potential solution is to come up with a proof-of-work algorithm which is dominated by human labor, and not computers. This is not as difficult as it may seem; although computers get more and more powerful every year, there are a number of problems that have remained out of computers' reach for decades, and it may even be possible to identify a class of problems that are the artificial-intelligence-theoretic equivalent of "NP-complete" - problems such that, if they can be solved, it with high probability implies that AI can essentially replicate human activity in its entirety, in which case we are essentially in a post-scarcity utopia and money may not even be necessary. The harder challenge is identifying problems that are computer-friendly to administer, but computer-hard to solve, and particularly identifying problems which work in this way even if the entire code is open-source. The closest that we currently have is strategy games like Go, where the number of potential states is astronomical and so human-like pattern-matching approaches, rather than the preferred computational strategy of brute force augmented by heavy use of pruning heuristics, if the dominant approach. However, even Go is not perfect, since the number of skilled Go players, just like players of any game, is highly concentrated. The question is, can we do better?

*

The question is, can we use these mechanisms, either separately or together, and perhaps in combination with cryptoeconomic protocols and sacrifices as a fallback in order to create an anti-Sybil system which is highly egalitarian? We will accept that any scheme can be cracked at some cost; however, what we want is for it to be much more efficient for individuals to obtain _one_ anti-Sybil token "the proper way" rather than purchasing one off the grey/black market.

**Problem** - create a mechanism for distributing anti-Sybil tokens

**Additional Assumptions**:

* Everyone is part of a social network with similar characteristics to social networks now found in the real world, and social data can be provably provided to cryptoeconomic systems (eg. blockchains, Ethereum contracts)

**Requirements**

* The cost of obtaining one anti-Sybil token for a human should be as low as possible
* The cost of obtaining multiple anti-Sybil tokens for a human should be as high as possible
* The cost of obtaining anti-Sybil tokens for an automated system should be as high as possible (this is a more important criterion than high cost for multi-obtainment for humans)
* The system should not create dependency on centralized parties (eg. government passport offices) that have the power to cheat the system

### DAOification

One of the most interesting developments out of the cryptocurrency space has been the concept of decentralized autonomous organizations: digital entities that still hire humans to perform certain kinds of creative labor that cannot be automated, but where the management is done autonomously. This serves as a sort of counterpart to traditional factory-style automation: instead of digitizing the core and humans managing the center, here we have human intelligence at the edges and automation at the center. Theoretically, DAOs allow higher degrees of efficiency, political neutrality and resistance to human corruption. However, the implementation of a DAO introduces some very complex challenges.

**12. Decentralized Public Goods Incentivization**

One of the challenges in economic systems in general is the problem of "public goods". For example, suppose that there is a scientific research project which will cost $1 million to complete, and it is known that if it is completed the resulting research will save one million people $5 each. In total, the social benefit is clear: if everyone contributes $1, then each individual person will see a benefit of $5 - $1 = $4 for $4 million total. However, the problem is that from the point of view of each individual person contributing does not make sense - whether or not you contribute has close to zero bearing on whether enough money will be collected, so everyone has the incentive to sit out and let everyone else throw their money in, with the result that no one does. So far, most problems to public goods have involved centralization; some large organization, whether a big company or a government, agrees to offer some of its private services only to those individuals who participate in paying for the public good. Often this is done implicitly: for example, some of the money from each purchase of an iPad goes toward research and development (some of which is a public good, and some of which is an excludable "club good"). At other times, it's more explicit, as in the case of taxation. In order for decentralized systems to be effective at replacing centralized systems, solutions to the public goods problem are required. A few possible approaches include:

* **Assurance contracts** - the idea behind an assurance contract is that `N` people may or may not put their funds into a pool, where that pool pays to produce a public good if and only if at least $X in total is contributed. Otherwise, the pool pays everyone back. If the pool creator acts optimally, the tipping point will be right at the top of the bell curve that is the probability distribution for how much other people might contribute, meaning that the chance that one user with their contribution of `X/N` will be pivotal should approach `~1/sqrt(n)`, creating a `sqrt(n)`-sized amplifying effect on their donation. A special type of assurance contract, called a dominant assurance contract, involves an entrepreneur that pays all contributors back slightly more than 100% of what they put in if the fund fails to reach its target (and takes profits if the fund succeeds); this provides an incentive for parties to create optimal assurance contracts.
* **Currency issuance** - a cryptoeconomic system can contain its own currency or token system which is somehow necessary or useful in some part of the system. These currency units can then either be generated by the system and then sold or directly assigned to reward contribution. This approach gets around the free-rider problem because no one needs to pay the $1 explicitly; the value arises out of the emergent value of the network which is does not cost people to support.
* **Status goods issuance** - a status good can be defined as a good that confers only relative benefit to its holder and not absolute benefit to society; for example, you may stand out in the public if you wear an expensive diamond necklace, but if everyone could trivially obtain such a necklace the situation would be very similar to a world with no diamond necklaces at all. A cryptoeconomic system can release its own status goods, and then sell or award them. One example of a status good is a "badge"; some online forums, for example, show a special badge beside users that have contributed funds to support the forum's development and maintenance. Another important example of a status good is a namespace; for example, a decentralized messaging protocol may be able to fund itself by selling off all of the 1-4 letter usernames; given the rates for which domain names are currently sold, an entity like Google should be willing to pay a large amount for the one-letter name "g".
* **Recursive incentivization** - this is in some ways a mirror image of the concept of "recursive punishment" that arguably underlies a large number of social protocols. For example, consider the case of tax-funded police forces. Ordinarily, in a Hobbesian jungle equilibrium, there are many opportunities in which it is, materially or psychically, profitable to attack other people. The way that societies solve this problem is by adding a punishment - an act which is purely destructive in itself, but which serves the important function of shifting the incentives so that attacking other people is no longer on net beneficial to the perpetrator. However, simple punishment has a problem: there is no incentive to participate in the punishment process. The next layer is thus to make punishment obligatory; in a tax-funded police scenario this means that everyone is required to pay funds to support the police. Those who do not participate are themselves subjected to the punishment process. Recursive incentivization is the opposite: here, we reward a desirable action, and people who participate in the rewarding mechanism (eg. by giving reward recipients a discount in shops) are themselves to be rewarded.

Many of these approached can arguably be done in concert, or even simultaneously within one mechanism.

**Problem**: come up with and implement a method for incentivizing public goods production in a decentralized environment.

**Additional Assumptions**

* A fully trustworthy oracle exists for determining whether or not a certain public good task has been completed (in reality this is false, but this is the domain of another problem)
* The agents involved can be a combination of individual humans, teams of humans, AIs, simple software programs and other DAOs
* A certain degree of cultural filtering or conditioning may be required for the mechanism to work, but this should be as small as possible

**Requirements**

* The mechanism should be executable fully mechanically
* No reliance on trusted parties or centralized parties should be required
* A certain degree of cultural filtering or conditioning may be required for the mechanism to work, but this should be as small as possible

**13. Decentralized contribution metrics**

Incentivizing the production of public goods is, unfortunately, not the only problem that centralization solves. The other problem is determining, first, which public goods are worth producing in the first place and, second, determining to what extent a particular effort actually accomplished the production of the public good. This challenge deals with the latter issue. For some tasks, like computing many billions of rounds of SHA256, it is easy to verify that the task has been done to a very high degree of accuracy. For general computation, it's also possible, though somewhat harder, and playing strategy games and proving theorems are also tasks that fall into that category. However, in the case of non-computational tasks, the situation is completely different. If a cryptoeconomic system wants to incentivize users to build better graphical user interfaces to its own system, how would it rate people's contributions? [Even more problematically](http://en.wikipedia.org/wiki/Underhanded_C_Contest), what about potentially quasi-adversarial tasks like incentivizing updates to its own code? What about a DAO that funds healthcare, or tries to incentivize combating climate change?

The only way to resolve any such issues is some concept of social proof - essentially, a formalism for "A did X if everyone says that A did X"; this is how all such issues are resolved in the real world. However, there are challenges involved in doing so efficiently. Where does the input come from? Who do we trust? How do we prevent the system from being vulnerable to cliques or Sybil takeover attacks?

**Problem**: come up with and implement a method for determining whether or not a particular task was performed by a specific person in a decentralized environment, and for estimating the quality of the work

**Additional Assumptions**

* The agents involved can be a combination of individual humans, teams of humans, AIs, simple software programs and other DAOs
* There is no cryptographically verifiable information about the completion of any task; the system must rely entirely on some form of social proof

**14. Decentralized outcome metrics**

Another, related, problem to the problem of decentralized contribution metrics is the problem of decentralized outcome metrics. On the macroscopic scale, how do we know if, and to what extent, an organization has succeeded in accomplishing its objectives? In the case of something like Bitcoin, there is a simple, but imperfect, answer: success can be measured by the hashpower of the network. This setup is reasonably effective, but is flawed in two ways: first, hashpower is an imperfect proxy for price, because the development or nondevelopment of ASICs may skew the results, and second, price is an imperfect proxy for success, because the currency may have greater success as something with a lower market capitalization if it is more used in other ways. In the case of a DAO funding healthcare or anti-climate-change efforts, however, no such heuristic exists at all. Once again, some concept of social proof is the only option.

**Problem**: come up with and implement a method for measuring and evaluating the success of certain objectives in a decentralized context.

**Additional Assumptions**

* The agents involved can be a combination of individual humans, teams of humans, AIs, simple software programs and other DAOs

**Requirements**

* The system should be able to measure anything that humans can currently reach a rough consensus on (eg. price of an asset, temperature, global CO2 concentration)

**15. Futarchy implementation**

One political system that was proposed by the economist Robin Hanson as an alternative to pure democracy is what is referred to as [futarchy](http://hanson.gmu.edu/futarchy.html): rule by prediction markets. Essentially, the values that the political system should aim for (eg. freedom, prosperity, health) and the metrics to use as proxies for those values (eg. GDP, HDI) are chosen by democracy, but the problem of which measures best optimize those values is determied by running a prediction market. For example, if there is a conflict between policy `A` and policy `B`, then one might release token `T[A]` which will be redeemable for $1 if `A` is chosen and $0 otherwise, token `T[B]` likewise for `B`, token `R[A]` redeemable after N years for $X if A is chosen and $0 otherwise where $X refers to the value of a chosen metric, and token `R[B]` likewise for `B`. These tokens are then floated on the market and allowed to obtain their natural price. The theory is that `price(R[x]) / price(T[x])` will be equal to the expected value of the metric if `x` is adopted, and so the policy with the higher expectation will be chosen. An alternative implementation, and the one proposed by Hanson himself, has only tokens `R[A]` and `R[B]`, with the proviso that all trades for token `R[x]` are reversed if `x` is not adopted; this model is somewhat cleaner but has the difficulty that it is difficult to reverse trades on secondary markets. In fact, if one uses the first model, and one has the primary market for `R[x]` be against `P[x]`, then one can see that the two models are actually simply different ways for reasoning about the exact same thing. 

Although it is hard to tell how well or how easily futarchy can be integrated into existing democracies, the automated nature of the model makes it very attractive and easy to implement in the context of a cryptoeconomic platform. For example, one can easily graft futarchy onto a decentralized currency as an issuance model, with the objective being to maximize the protocol's mining hashpower as a proxy for price. In theory, such a currency would automatically allocate funds to wherever speculators feel (and are willing to put their money down on) is most useful, funding research, protocol and software development, adoption efforts, etc. Combined with decentralized success metrics, it can create what is basically a complete turnkey solution for decentralized autonomous organization governance. Of course, there is the question of where futarchy-based decision-making should be used and where a stakeholder voting approach is more practical, but that is likely to be ultimately decided by experimentation.

**Problem**: come up with and implement a design for a futarchy as a cryptoeconomic platform (whether ethereum contract, independent blockchain or whatever other fully decentralized mechanism), and show that it delivers good decision-making ability in a fully decentralized manner.

**Requirements**

* The system should be set up to maximize an easily measurable or quasi-measurable statistic (eg. price, completion of a computational task, success toward an optimization problem)
