The below is a work in progress.

Issues:

* A lot of these are VERY subjective. Is my initial approach for determining winning criteria a good one or do we just say "work on this, if we like the idea we'll accept it"
* Is this a good format for descriptions? Do we want to add more detail for people who aren't yet as experienced with cryptocurrency?

### Big Problems in Cryptocurrency

**1. Create a blockchain design that provides a fundamental improvement over the current model where every node must process every transaction**. 

One of the largest problems facing the cryptocurrency space today is the issue of scalability. It is an opt-repeated claim that, while mainstream payment networks process something like 2000 transactions per second, in its current form the Bitcoin network can only process seven. On a fundamental level, this is not strictly true; simply by changing the block size limit parameter, Bitcoin can easily be made to support 70 or even 7000 transactions per second. However, if Bitcoin does get to that scale, we run into a problem: it becomes impossible for the average user to run a full node, and full nodes become relegated only to that small collection of businesses that can afford the resources. Because mining only requires the block header, even miners can (and in practice most do) mine without downloading the blockchain.

The main concern with this is trust: if there are only a few entities capable of running full nodes, then those entities can conspire and agree to give themselves a large number of additional bitcoins, and there would be no way for other users to see for themselves that a block is invalid without processing an entire block themselves. Although such a fraud may potentially be discovered after the fact, power dynamics may create a situation where the default action is to simply go along with the fraud (and authorities can create a climate of fear to support such an action) and there is a coordination problem in invalidating the fraudulent chain. Thus, in the limit, Bitcoin with 7000 transactions per second has security properties that are essentially similar to a centralized system like Paypal, and what we want is a system that handles 7000 TPS with the same levels of decentralization that cryptocurrency originally promised to offer.

Ideally, a blockchain design should exist that works, and has similar security properties to Bitcoin with regard to 51% attacks, that functions even if no single node processes more than `1/n` of all transactions where `n` can be scaled up to be as high as necessary, perhaps to be balanced with secondary inefficiencies and convergence concerns. This would allow the blockchain architecture to process an arbitrarily high number of TPS but at the same time retain the same level of decentralization that Satoshi envisioned.

**Problem**: create a blockchain design that maintains Bitcoin-like security guarantees, but where the maximum size of the most powerful node that needs to exist for the network to keep functioning is sublinear in the number of transactions.

**Assumptions**:

* There exist a large number of miners in the network
* Miners may be using specialized hardware or unspecialized hardware. Specialized hardware should be assumed to be more powerful than unspecialized hardware by a large (eg. 10000) constant factor at specific tasks.
* Ordinary users will be using unspecialized hardware
* No coalitions above 25% are possible
* Over 75% of the agents (weighted by resources) are rational and interested in maximizing profits

**Requirements: Level 1**

* If N is the number of transactions, a transaction made should reach the point where a double spend on that transaction has a probability of success less than `p` in `O(log(N)*log(1/p))` time; as time approaches infinity, obviously `p -> 0`
* The largest honest node that needs to exist should have size sublinear in N. That is to say, as N grows the portion of transaction data that each node needs to store should shrink to an arbitrarily small fraction.

**Requirements: Level 2**

* No node should need to have more than `O(sqrt(N))` resources.

**2. Create a proof-of-work algorithm that is resistant to mining centralization**

One of the key elements in the Bitcoin algorithm is the concept of "proof of work". In any Byzantine-fault-tolerant system, the security level is often defined as the minimum percentage of hostile nodes - for example, in the context of secret sharing, the Berlekamp-Welch algorithm with 2x redundancy is guaranteed to provide the correct output assuming that the total number of hostile nodes does not exceed 25% of the network, and in the context of Bitcoin mining the requirement is that the size of the set of honest nodes exceeds the size of any individual hostile coalition. However, all of these security guarantees have one important qualification: there must be some way to define what an individual node is. Before Bitcoin, most fault-tolerant algorithms had high computational complexity and assumed that the size of the network would be small, and so each node would be run by a known individual or organization and so it is possible to count each node individually. With Bitcoin, however, nodes are numerous, mostly anonymous, and can enter or leave the system at any time. Unless one puts in careful thought, such a system would quickly run into what is known as a Sybil attack, where a hostile attacks simply creates five times as many nodes as the rest of the network combined, whether by running them all on the same machine or rented virtual private server or on a botnet, and uses this supermajority to subvert the network. In order to prevent this kind of attack, the only known solution is to use a resource-based counting mechanism. For this purpose, Bitcoin uses a scheme known as proof-of-work, which consists of solving problems that are difficult to solve, but easy to verify. The weight of a node in the consensus is based on the number of problem solutions that the node presents, and the Bitcoin system rewards nodes that present such solutions ("miners") with new bitcoins and transaction fees.

Bitcoin's proof of work algorithm is a simple design known as Hashcash, invented by Adam Back in 1995. The hashcash function works as follows:

    def hashcash_produce(data,difficulty):
		nonce = 0
		while sha256(data + str(nonce)) > 2**256 / difficulty:
		    nonce += 1
	    return nonce

    def hashcash_verify(data,nonce,difficulty):
	    if sha256(data + str(nonce)) <= 2**256 / difficulty:
		    return True
        else:
		    return False

Originally, the intent behind the Bitcoin design was very egalitarian in nature. Every individual was intended to participate in the mining process from their own desktop computer, and the result was to produce both a network that is highly decentralized and resistant to any attempt at control and a distribution mechanism that spreads the initial supply of BTC across a very large number of users. And for the first one and a half years of Bitcoin's existence, that is indeed how the system worked. In the summer of 2010, however, developers released a Bitcoin miner that took advantage of the massive parallelization offered by the graphics processing unit (GPU) of powerful computers, mining about 10-50 times more efficiently than CPUs. Over the course of about nine months, CPU mining became completely unprofitable. At that point, an inequality started to emerge - not many people have GPUs with powerful graphics cards, so the revenue that could be earned by an average person from mining dwindled, and dedicated hobbyist mining operations, purchasing a large quantity of graphics cards and plugging them into a powerful computer, began to grow. Thus, Bitcoin mining remained accessible to individuals, but now increasingly primarily to individuals with money and interest in purchasing hardware.

In 2013, the Bitcoin mining economy took a further turn. A number of companies, led by Avalon, ASICMiner and Butterfly Labs, started producing devices known as "application-specific integrated circuits" (ASICs) - chips designed in silicon with the sole purpose of Bitcoin mining in mind. These devices were once again 10-50 times more efficient than GPUs, and after less than six months GPU mining also became unprofitable. And that is where we are now: the only way to mine is to either start a multimillion-dollar ASIC manufacturing company, purchase an ASIC from a company that already exists and run it at home, or simply purchase a "mining contract" as an investment, where the company produces and runs the devices in-house and returns to the contract purchasers a share of the revenue. The first option is arguably less efficient, and thus only exists because of the hobbyist nature of the current Bitcoin community; once large purely profit-oriented players start to dominate, we have no guarantees that mining will not further centralize into a few large corporations with high barriers to entry.

**Problem**: Create two functions, `PoWProduce(data,diff) -> nonce` and `PoWVerify(data,nonce,diff) -> { 0, 1 }`, to serve as alternatives to Hashcash such that it is economically unattractive to produce an ASIC for `PoWProduce`

**Requirements, Level 1**:

* `PoWProduce` must have expected runtime linear in `diff`
* `PoWVerify` must have runtime at most polylogarithmic in `diff`
* Running `PoWProduce` should be the most efficient way to produce values that return `1` when checked with `PoWVerify`
* `PoWProduce` must not be superlinear in computational power or time; that is to say, the expected number of successful `PoWProduce` computations for a node with `N` dollars worth of hardware after `t` seconds should be bounded by `kNt` for some `k`. Furthermore, at a difficulty level which assumes $1 billion worth of hardware in the network producing one successful `PoWProduce` computation per minute, $1000 worth of hardware should be able to provide `5000k` successful `PoWProduce` computations after 10 seconds
* It should be shown with reasonably rigorous technological and economic analysis that either (1) ASICs are not expected to be more than 10x more efficient than generic CPU/GPU hardware per dollar spent on electricity and production with initial and fixed costs infinitely amortized at a 5% discount rate and assuming a firm size of $100 billion, or (2) any ASIC for the system can be simultaneously used as an improved general-purpose or semi-general-purpose computational device such that if this technology were to be integrated into the mainstream the ASIC would not be more than 10x more efficient than generic CPU/GPU hardware.

**Requirements, Level 2**

* Same as level 1, except substituting CPU/GPU with CPU only.

**3. Create a proof-of-work algorithm where the work is made up of independently useful computation**

One major economic issue, often pointed out by detractors of Bitcoin, is that the proof of work done in the Bitcoin network is essentially wasted effort. Miners spend 24 hours a day cranking out SHA256 (or in more advanced implementations Scrypt) computations with the hopes of producing a block that has a very low hash value, and ultimately all of this work has no value to society. Traditional centralized networks, like Paypal and the credit card network, manage to get by without performing any proof of work computations at all, whereas in the Bitcoin ecosystem about a million US dollars of electricity and manufacturing effort is essentially wasted every day to prop up the network.

One way of solving the problem that many have proposed is making the proof of work function something which is simultaneously useful; a common candidate is something like Folding@home, an existing program where users can download software onto their computers to simulate protein folding and provide researchers with a large supply of data to help them cure diseases. The problem is, however, that Folding@home is not "easy to verify"; verifying the someone did a Folding@home computation correctly, and did not cut corners to maximize their rounds-per-second at the cost of making the result useless in actual research, takes as long as doing the computation oneself. If either an efficiently verifiable proof-of-computation for Folding@home can be produced, or if we can find some other useful computation which is easy to verify, then cryptocurrency mining could actually become a huge boon to society, not only removing the objection that Bitcoin wastes "energy", but even being socially beneficial by providing a public good.

Note that there is one major concern with this approach that has been identified: if the useful PoW is implemented incorrectly, it can potentially reduce the cost of an attack on the network. If the useful PoW is useful in such a way that it is sometimes economically viable for certain very large entities to perform the computation even without the currency incentive, then those entities have an incentive to launch consensus takeover attacks ("51% attacks") against the network at no cost, since they would be performing the computations anyway. One simple, though crude and imperfect, way of addressing this problem is to make the PoW a half-and-half mix between useful and useless, making sure that every entity would need to pay at least 50% of the intended cost to launch an attack. By looking at existing attempts to make useful work verifiable, it is in fact quite likely that there will be at least 2x cryptographic overhead in the process by default. Another economic solution is to make the computation a "pure" public good such that no individual entity derives a significant benefit from it. Proposed solutions to this problem should include a rigorous analysis of this issue.

**Requirements**

* `PoWProduce` must have expected runtime linear in `diff`
* `PoWVerify` must have expected runtime at most polylogarithmic in `diff`
* Running `PoWProduce` should be the most efficient way to produce values that return `1` when checked with `PoWVerify`
* `PoWProduce` must not be superlinear in computational power or time; that is to say, the expected number of successful `PoWProduce` computations for a node with `N` dollars worth of hardware after `t` seconds should be bounded by `kNt` for some `k`. Furthermore, at a difficulty level which assumes $1 billion worth of hardware in the network producing one successful `PoWProduce` computation per minute, $10000 worth of hardware should be able to provide `50000k` successful `PoWProduce` computations after 10 seconds
* `PoWProduce` must produce a public good, such that the total value to everyone of the public good produced is greater than the cost of all resources invested into the mining process.

Discussed in:

* Laurie, B., & Clayton, R. (2004, September). “Proof-of-Work” proves not to work; version 0.2. In Workshop on Economics and Information, Security.
* Wash, R., & MacKie-Mason, J. K. (2007, August). Security when people matter: structuring incentives for user behavior. In Proceedings of the ninth international conference on Electronic commerce (pp. 7-14). ACM.

**4. Create an incentive-compatible mechanism for constructing a cryptographic asset whose value is expected to be reasonably stable**

One of the main problems with Bitcoin is the issue of price volatility. The value of a bitcoin often experiences very large fluctuations, rising or falling by as much as 25% in a single day and 3x in a month. The main economic reason behind this is that the supply of bitcoins is fixed, so its price is directly proportional to demand (and therefore, by efficient market hypothesis, the expected discounted future demand), and demand is very unpredictable. It is not known if Bitcoin will be simply a niche payment method for transcations requiring a high degree of privacy, a replacement for Western Union, a mainstream consumer payment system or the reserve currency of the world, and the expected value of a bitcoin differs over a thousandfold between these various levels of adoption. Furthermore, the utility of the Bitcoin protocol is heavily dependent on the movements of the Bitcoin price (ie. people are interested in Bitcoin more if the price is going up), creating a positive feedback loop, which has arguably been responsible for both Bitcoin's great meteoric rises and its many-month-long periods of rapid decline.

To solve this problem, there are generally two paths that can be taken. The first is to have the network somehow detect its current level of economic usage, and have a supply function that automatically increases supply when usage increases. This reduces uncertainty; even though the expected future level of adoption of the protocol may have a variance of 10-100x, the circumstance where adoption increases 100x will also have 100x more supply and so the value of the currency will remain the same. There is a problem that if usage decreases there is no way to remove units from circulation, but even still the lack of upward uncertainty should reduce upward volatility, and downward volatility would also naturally reduce because it is no longer bad news for the value of the currency when an opportunity for increased usage is suddenly removed. Furthermore, in the long term the economy can be expected to grow, so the zero-supply-growth floor may not even ever be reached in practice. The problem is that measuring an economy in a secure way is a difficult problem. The most obvious metric that the system has access to is mining difficulty, but mining difficulty also goes up with Moore's law, and there is no known way to estimate the impact of Moore's law alone and so the currency cannot know if its difficulty increased by 10x due to better hardware, a larger user volume or a combination of both. Other metrics, such as transaction count, are potentially gameable by entities that want the supply to change in a particular direction (generally, holders want a lower supply, miners want a higher supply).

Another approach is to attempt to create a currency which tracks a specific asset, using some kind of incentive-compatible scheme likely based on the game-theoretic concept of Schelling points, to feed price information about the asset into the system in a decentralized way. This could then be combined with a supply function mechanism as above, or it can be incorporated into a zero-total-supply currency system which uses debts collateralized with other cryptographic assets to offset its positive supply and thus gain the ability to grow and shrink with changes to usage in either direction. The problem here is constructing the scheme in such a way that there is no incentive for entities to feed in false price information in order to increase or decrease the supply of the asset in their favor.

**Problem**: construct a cryptographic asset with a stable price.

**Assumptions**

* No coalitions above 25% are possible
* Over 75% of the agents (weighted by resources) are rational and interested in maximizing profits
* At least one honest and altruistic party exists
* Financial markets offering 10x leverage on any asset exist, but no higher

**Requirements: Level 1**

* The expected root-mean-square daily change in the logarithm of the price of the asset should be less than 50% of that of Bitcoin under similar conditions
* The expectation analysis should take into account black swan risks (ie. systems where the variance is 0% 99% of the time but 10x in a day the other 1% of the time are unacceptable)
* The submitters are expected to come up with a model, including parameters such as short-term-consumption purchases, medium-term purchases, speculative purchases, positive and negative media, adoption and regulatory events, irrational actors and actors with political motives, show that their model well fits the history of Bitcoin and potentially major altcoins without overfitting, and show that under the model the other two requirements hold

**Requirements: Level 2**

* The asset should be guaranteed to maintain a value within 10% of an arbitrary cryptographic or real-world asset for which price information is easily accessible given the economic assumptions.

**5. Create an incentive-compatible proof-of-stake currency**

Another approach to solving the mining centralization problem is to abolish mining entirely, and move to some other mechanism for counting the weight of each node in the consensus. The most popular alternative under discussion to date is "proof of stake" - that is to say, instead of treating the consensus model as "one unit of CPU power, one vote" it becomes "one currency unit, one vote". A very simple proof of stake algorithm requires the miner mining the block to sign it with the private key to the address holding their coins, where the block is valid if `sha256(PREVHASH + ADDRESS + TIMESTAMP) <= 2^256 * BALANCE / DIFFICULTY` where `PREVHASH` is the hash of the previous block, `ADDRESS` is the signer's address with balance `BALANCE`, `TIMESTAMP` is the current Unix time in seconds and `DIFFICULTY` is an adjustable parameter to regulate the frequency of successful signatures. At first glance, this algorithm has the basic required properties: every miner has some random chance per second of succeeding, and if your address has twice as much money in it then you have double the chance of success.

However, this algorithm has two important flaws:

1. There is no trust-free way to verify the timestamp. In Bitcoin, this problem is not significant because each miner only gets a very small benefit, if any benefit at all, from manipulating the timestamp and the two-hour timescale (of the rule that miners are to reject blocks that are more than two hours into the future according to their own clocks) ensures that there is very little risk of a consensus failure. Here, however, the timestamp is of critical importance, and with the protocol as given plus Bitcoin's two-hour rule miners have the incentive to immediately try all 7200 timestamps right up to the limit. Thus, network time is expected to constantly be slightly less than two hours into the future, and the prime criterion for determining whether or not a block is valid becomes the timestamp, which is subjective to each miner. This carries a very large risk of consensus failures.  
2. In the event of a fork, whether the fork is accidental or a malicious attempt to rewrite history and reverse a transaction, the optimal strategy for any miner is to mine on every chain, so that the miner gets their reward no matter which fork wins. Thus, assuming a large number of economically interested miners, an attacker may be able to send a transaction in exchange for some digital good (usually another cryptocurrency), receive the good, then start a fork of the blockchain from one block behind the transaction and send the money to themselves instead, and even with 1% of the total stake the attacker's fork would win because everyone else is mining on both.

The Slasher algorithm, described [here <TODO: LINK>](), represents my own attempt at fixing the second problem. The crux of the algorithm is that (1) the miners for a specific block are determined ahead of time, so in the event of a fork a miner will either have an opportunity to mine a given block on all chains or no chains, and (2) if a miner is caught signing two distinct blocks with the same block number they can be punished and forced to surrender their block reward in the 1000 blocks before it becomes available to spend. The algorithm is viable and effective, but it does suffer from one flaw of unknown significance: if all of the miners for a given block learn each other's identities beforehand, they can meet up and collude to either perform a transaction reversal (or "double-spend") attack or extort the community for money with the threat of not signing the block and forcing the network to grind to a halt.

**Problem**: create a proof-of-stake algorithm that solves the two problems described above while either partially or completely mitigating the flaw in Slasher.

**Assumptions**

* No coalitions above 25% are possible
* At least 100 agents exist with at least 0.1% total resources
* Over 75% and potentially up to 100% of agents (weighted by resources) are rational and interested in maximizing profits

**Requirements: Level 1**

* The expected return from mining should be bounded by `k` times the miner's stake for some `k`, and assuming $1 billion total participating stake a stake of $100 should provide at least a return of 50k.
* The algorithm should address the timestamping issue defined above, allowing proof of work equal to at most 1% that used by Bitcoin. Economic justification is required as to why the equilibrium has miners only performing 1% as much proof of work.
* The algorithm should be incentive-compatible, addressing the double-voting issue defined above and the collusion issue defined above.

**Requirements: Level 2**

* Coalitions are now allowed up to 45%
* No proof of work is allowed; the timestamping issue must still be solved.

**6. Create a proof-of-human-work distribution/mining mechanism that is expected to be human-dominated and reasonably egalitarian**

A major concern with cryptocurrencies is the problem of distribution. Originally, Bitcoin was intended to be widely distributed, giving everyone with a CPU the ability to acquire bitcoins. Of course, even that approach was imperfect; the poorest people in the world rely on low-power electronics such as smartphones or OLPC laptops, which simply have no chance at competing with high-power laptops and desktops even in a CPU mining economy. Furthermore, the development of professional mining rigs and more recently specialized hardware devices now ensures that only individuals with a substantial amount of economic resources can participate. Essentially, one's ability to earn bitcoins in the modern economy is linearly proportional to the amount of wealth that one already has at best, and superlinear in one's wealth at worst. Ideally, a cryptocurrency would have a more egalitarian way of distributing itself, and preferably one which is resistant against potential attacks in the long term so that the currency can be fairly distributed to future generations as well as the present.

One potential solution is to come up with a proof-of-work algorithm which is dominated by human labor, and not computers. This is not as difficult as it may seem; although computers get more and more powerful every year, there are a number of problems that have remained out of computers' reach for decades, and it may even be possible to identify a class of problems that are the artificial-intelligence-theoretic equivalent of "NP-complete" - problems such that, if they can be solved, it with high probability implies that AI can essentially replicate human activity in its entirety, in which case we are essentially in a post-scarcity utopia and money may not even be necessary. The harder challenge is identifying problems that are computer-friendly to administer, but computer-hard to solve, and particularly identifying problems which work in this way even if the entire code is open-source. The closest that we currently have is strategy games like Go, where the number of potential states is astronomical and so human-like pattern-matching approaches, rather than the preferred computational strategy of brute force augmented by heavy use of pruning heuristics, if the dominant approach. However, even Go is not perfect, since the number of skilled Go players, just like players of any game, is highly concentrated. The question is, can we do better?

**Problem**: come up with a blockchain algorithm where the distribution model, and ideally the work, is dominated by human labor.

**Requirements: Level 1**

* Given a well-justified extrapolation of the global levels of human and computer competence at the underlying problem, over 75% of the voting shares in the system should be provided by human labor, either unaided or aided only by commodity hardware and open-source software.

**Requirements: Level 2**

* Given a well-justified extrapolation of the global levels of human and compuer competence at the underlying problem, over 90% of the voting shares in the system should be provided by human labor, either unaided or aided only by commodity hardware and open-source software.
* The top human laborer should not be more than 100x as effective as the bottom 10th percentile human laborer.

**7. Create a proof-of-excellence distribution/mining mechanism that rewards human-directed research and is reasonably egalitarian**

One major economic issue, often pointed out by detractors of Bitcoin, is that the proof of work done in the Bitcoin network is essentially wasted effort. Miners spend 24 hours a day cranking out SHA256 (or in more advanced implementations Scrypt) computations with the hopes of producing a block that has a very low hash value, and ultimately all of this work has no value to society. Traditional centralized networks, like Paypal and the credit card network, manage to get by without performing any proof of work computations at all, whereas in the Bitcoin ecosystem about a million US dollars of electricity and manufacturing effort is spent every day to prop up the network.

One interesting, and largely unexplored, solution to this problem is creating a proof of work that is dominated by highly intellectual human labor involving solving problems that are useful to sicety. For example, one can come up with a "proof of proof" currency that rewards players for coming up with mathematical proofs of certain theorems. There is no generic algorithm, aside from brute force, for proving theorems, and yet proofs of theorems are theoretically computationally easy to verify: one simply needs to write every step of the proof in a formal language, allowing the use of only one inference rule (eg. `a + b = b + a` or `a * (b + c) = a * b + a * c` but not `a * (b + c) = a * c + b * a`) between each step, and having a program verify the correctness of the inferences at each step.

For example, a proof of a common olympiad algebraic factorization problem appears as follows:

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

**Problem**: create a proof-of-excellence distribution/mining mechanism that rewards solving important research problems.

**Requirements: Level 1**

* Given a well-justified extrapolation of the global levels of human and computer competence at the underlying problem, over 75% of the voting shares in the system should be provided by human labor, either unaided or aided only by commodity hardware and open-source software.
* The algorithm must reward progress made toward a research problem that has definite direct or indirect positive impact in an area of research that has benefit to humanity

**Requirements: Level 2**

* Given a well-justified extrapolation of the global levels of human and computer competence at the underlying problem, over 75% of the voting shares in the system should be provided by human labor, either unaided or aided only by commodity hardware and open-source software.
* The algorithm must reward progress made toward a research problem that has definite direct or indirect positive impact in an area of research that has benefit to humanity
* The algorithm must be future-proof; that is to say, it must continue rewarding value production in the long term and should not be an area that will eventually be "solved" completely
* The research area must be sufficiently wide that it can be expected to continue to have significant benefit to humanity in the long term; further research should not become useless due to either rapid diminishing returns or changes in instrumental or terminal values up to a reasonable point.

**8. Create a high-accuracy distributed timestamp consensus system**

An important property that Bitcoin needs to keep is that there should be roughly one block generated every ten minutes; if a block is generated every day, the payment system becomes too slow, and if a block is generated every second there are serious centralization and network efficiency concerns that would make the consensus system essentially nonviable even assuming the absence of any attackers. To ensure this, the Bitcoin network adjusts difficulty so that if blocks are produced too quickly it becomes harder to mine a new block, and if blocks are produced too slowly it becomes easier. However, this solution requires an important ingredient: the blockchain must be aware of time. In order to solve this problem, Bitcoin requires miners to submit a timestamp in each block, and nodes reject a block if the block's timestamp is either (i) behind the median timestamp of the previous eleven blocks, or (ii) more than 2 hours into the future, from the point of view of the node's own internal clock. This algorithm is good enough for Bitcoin, because time serves only the very limited function of regulating the block creation rate over the long term, but there are potential vulnerabilities in this approach, issues which may compound in blockchains where time plays a more important role.

**Problem**: create a distributed incentive-compatible system, whether it is an overlay on top of a blockchain or its own blockchain, which maintains the current time to high accuracy.

**Assumptions**

* All legitimate users have clocks in a normal distribution around some "real" time with standard deviation 20 seconds.
* No two nodes are more than 20 seconds apart in terms of the amount of time it takes for a message originating from one node to reach any other node.
* No coalitions above 25% are allowed.

**Requirements: Level 1**

* 120 second accuracy required.
* Standard non-superlinearity mining requirements apply

**Requirements: Level 2**

* 40 second accuracy required.
* Standard non-superlinearity mining requirements apply

**9. Create a method of constructing an arbitrary proof-of-computation that is semi-succinctly verifiable**

Perhaps the holy grail of the study zero-knowledge proofs is the concept of an arbitrary proof of computation: given a program P with input I, the challenge is to create a zero-knowledge proof that you ran P with input I and received output O, such that the proof can be verified quickly (ie. in polylogarithmic or ideally constant time) even if the original computation took a very large number of steps to complete. In an ideal setup, the proof would even hide the value of I, just proving that you ran P with some output with result O, and if I needs to be made public it can be embedded into the program. Such a primitive, if possible, would have massive implications for cryptocurrency:

1. The blockchain scalability problem would essentially be solved. Instead of miners publishing blocks containing a list of transactions, they would be publishing a proof that they ran the blockchain state updater with some list of transactions and produced a certain output; thus, instead of transactions needing to be verified by every node in the network, they could be processed by one miner and then every other miner and user could quickly verify the proof of computation and if the proof turns out correct they would accept the new state.
2. The blockchain privacy problem would essentally be solved. The blockchain scalability solution above would hide the details behind individual transactions; it would only reveal the fact that all of them are legitimate, so transactions would be hidden from everyone but the sender and the receiver.
3. It would become computationally viable to use a Turing-complete consensus network as a generic distributed cloud computing system; if you have any computation you wanted done, you would be able to publish the program for miners and miners would be able to run the program for you and deliver the result alongside a proof of its validity.

There is a large amount of existing research on this topic, including a protocol known as "SCIP" (succinct computational integrity and privacy) or "zkSNARK" that is already in its later stages of completion; use of this prior work by both its original developers and others is encouraged.

**Problem**: create programs `POC_PROVE(P,I) -> (O,Q)` and `POC_VERIFY(P,O,Q) -> { 0, 1 }` such that `POC_PROVE` runs program `P` on input `I` and returns the program output `O` and a proof-of-computation `Q` and `POC_VERIFY` takes `P`, `O` and `Q` and outputs whether or not `Q` and `O` were legitimately produced by the `POC_PROVE` algorithm using `P`.

**Requirements: Level 1**

* The runtime of `POC_PROVE` should be in `O(n*polylog(n))` where `n` is the number of steps required to run the program.
* The runtime of `POC_VERIFY` should be in `O(polylog(n))`
* The proofs created by `POC_PROVE` should take up at most `O(polylog(n))` space (this is obvious from the other requirements but deserves to be emphasized)

**Requirements: Level 2**

* The runtime of `POC_PROVE` should be in `O(n)`
* The runtime of `POC_VERIFY` should be constant
* The proofs should take up constant space

**10. Create an efficient indistinguishability obfuscation algorithm**

For many years now we have known how to encrypt data. Simple, robust and well-tested algorithms exist for both symmetric key encryption, where the same key is needed to encrypt and decrypt, and public key encryption, where the encryption key and decryption key are different and one cannot be derived from the other. However, there is another kind of encryption that can potentially be very useful, but for which we currently have no viable algorithm: the encryption of programs. The holy grail is to create an obfuscator `O`, such that given any program `P` the obfuscator can produce a second program `O(P) = Q` such that `P` and `Q` return the same output if given the same input and, importantly, `Q` reveals no information whatsoever about the internals of `P`. One can hide inside of `Q` a password, a secret encryption key, or one can simply use `Q` to hide the proprietary workings of the algorithm itself.

In 2007, it was proven that perfect "black box" encryption is impossible; essentially, the argument is that there is a difference between having black-box access to a program and having the code to that program, no matter how obfuscated, and one can construct certain classes of programs that resist obfuscation. However, there is also a weaker notion of obfuscation, known as indistinguishability obfuscation, that appears to be quite possible. The definition of an indistinguishability obfuscator `O` is that if you take two equivalent (ie. same inputs -> same outputs) programs `A` and `B` and calculate `O(A) = P` and `O(B) = Q`, then there is no computationally feasible way for an outsider without access to `A` or `B` to tell whether `P` came from `A` or `B`. This type of obfuscation ay seem more limited, but it is nevertheless sufficient for many applications. For a heuristic argument why, consider two programs `F` and `G` where `F` internally contains and simply prints out that 32-byte string which is the hash of "12345", whereas `G` actually computes the hash of "12345" and prints it out. By the indistinguishability obfuscation definition, there is no computationally feasible way to tell `O(F)` from `O(G)` apart. Hence, if one can feasibly recover "12345" from `O(G)`, then for `O(G)` and `O(F)` to be indistinguishable one would also need to be able to feasibly recover "12345" from `O(F)` - a feat which essentially entails breaking the preimage resistance of a cryptographic hash function.

Recently, a discovery was made by Craig Gentry, Amit Sahai et al on an algorithm which uses a construction known as "multilinear jugsaw puzzles" in order to accomplish this. Their algorithm, described [here](http://eprint.iacr.org/2013/451), claims to satisfy the indistinguishability obfuscation property, although at a high cost: the algorithm requires the use of fully homomorphic encryption, a highly inefficient construction that incurs roughly a one-billion-fold computational overhead.

If this construction can be made better, the potential benefits are massive. The most interesting possibility in the world of cryptocurrency is the idea of an on-blockchain contract containing private information. This basically allows for the scripting properties of Turing-complete blockchain technologies, such as Ethereum, to be exported into any other financial or non-financial system on the internet; for example, one can imagine an Ethereum contract which contains a user's online banking password, and if certain conditions of the contract are satisfied the contract would initiate an HTTPS session with the bank, using some node as an intermediary, and log into the bank account with the user's password and make a specified withdrawal. Because the contract would be obfuscated, there would be no way for the intermediary node, or any other player in the blockchain, to modify the request in-transit or determine the user's password. The same trick can be done with any other website, or much more easily with a "dumb" blockchain such as Bitcoin.

**Problem**: create a reasonably efficient indistinguishability obfuscation algorithm.

**Requirements: Level 1**

* Successful attacks must have an expected runtime above 2^80
* At most a 65536x slowdown is allowed.

**Requirements: Level 2**

* Successful attacks must have an expected runtime above 2^128
* At most a 256x slowdown is allowed.
