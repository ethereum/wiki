### What is Proof of Stake

Proof of stake is a consensus algorithm for public blockchains which is intended to serve as an alternative to proof of work. Proof of work is the mechanism that underpins the security behind Bitcoin, the current version of Ethereum and many other blockchains, however it has been criticized for centralization issues, game-theoretic weaknesses, requirement of a large amount of ongoing currency issuance, and the environmental damage and electricity cost (along with other implied externalities due to government subsidies) associated with the "mining" process. Bitcoin's proof of work has been calculated to consume [as much electricity as all of Ireland](https://karlodwyer.github.io/publications/pdf/bitcoin_KJOD_2014.pdf), but the result is a network that is highly centralized, with a very small number of mining pools (the largest of which directly operate most of their hardware) controlling the bulk of the network. Proof of stake attempts to resolve these issues by removing the concept of "mining" entirely, and replacing it with a different mechanism.

The mechanism in proof of stake can be described as a form of "virtual mining". Whereas proof of work relies on computer hardware as the primary form of scarcity to prevent [Sybil attacks](https://en.wikipedia.org/wiki/Sybil_attack), proof of stake relies on coins inside of the blockchain itself. In proof of work, a user might take $1000, use it to buy a mining computer, plug it in and start participating in the network and producing blocks and getting rewards, in proof of stake, one could take $1000, convert it into $1000 worth of coins, then deposit the coins into the proof of stake mechanism, which would (pseudo-)randomly assign the owner the right to produce blocks and get rewards. Much like in proof of work, where spending $2000 would get you a miner that is twice as powerful and hence can produce blocks and receive rewards twice as often, in proof of stake a deposit that is twice as large is twice as likely to be assigned the right to produce a block in a given round.

In general, a proof of stake algorithm looks as follows. There exists some set of coin holders that place their coins into a proof of stake mechanism and thereby become validators. Given a particular blockchain "head" (ie. the latest block in a blockchain), the algorithm randomly selects one of these validators (the randomness being weighted by deposit size, so a validator with 10000 coins has 10x the chance of a validator with 1000 coins) and assigns to them the right to create the next block. If that validator does not create a block within some period of time, then a secondary validator is selected that can create the block instead. Just like in proof of work, the "longest chain" is considered to be the canonical one.

Note that there are many deviations from this model. In earlier Peercoin-style algorithms, a different validator was assigned block creation rights every second. Sometimes, there is no explicit mechanism for "becoming a validator"; _every_ coin holder is a potential validator, though if a coin holder is offline or uninterested in validating then they may well "skip their turn". In some algorithms, there is no notion of validator selection; instead, a traditional [Byzantine-fault-tolerant consensus algorithm](https://en.wikipedia.org/wiki/Byzantine_fault_tolerance) is used to get _all_ validators to agree on the next block. The seed for the pseudorandom algorithm that chooses the next validator can also be chosen in different ways. However, the principle of using coins, deposited or otherwise, as a substitute for miners is invariably there.

### What are the benefits?

In short:

* No need to consume large quantities of electricity in order to secure a blockchain.
* Because of the lack of high electricity consumption, there is not as much need to issue as many new coins in order to motivate participants to keep participating in the network. It may theoretically even be possible to have _negative_ net issuance, where a portion of transaction fees is "burned" and so the supply goes down over time.
* Possibly reduced vulnerability to selfish-mining attacks through "co-operative game theory", though proof of work can also do this to some extent.
* Reduced centralization risks, as economies of scale are much less of an issue. $10 million of coins will get you exactly 10 times higher returns than $1 million of coins, without any additional disproportionate gains because at the higher level you can afford better mass-production equipment.
* Ability to use economic penalties to make various forms of 51% attacks vastly more expensive to carry out than proof of work - to paraphrase Vlad Zamfir, "it's as though your ASIC farm burned down if you participated in a 51% attack".

### How does proof of stake fit into traditional Byzantine fault tolerance research?

There are several fundamental results from Byzantine fault tolerance research that apply to all consensus algorithms, including traditional consensus algorithms like PBFT but also any proof of stake algorithm and even, contrary to popular opinion, to some extent proof of work.

The key results include:

* [**CAP theorem**](https://en.wikipedia.org/wiki/CAP_theorem) - "in the cases that a network partition takes place, you have to choose either consistency or availability, you cannot have both". The intuitive argument is simple: if the network splits in half, and in one half I send a transaction "send my 10 coins to A" and in the other I send a transaction "send my 10 coins to B", then either the system is unavailable, as one or both transactions will not be processed, or it becomes inconsistent, as one half of the network will see the first transaction completed and the other half will see the second transaction completed.
* [**FLP impossibility**](http://the-paper-trail.org/blog/a-brief-tour-of-flp-impossibility/) - in an asynchronous setting (ie. there are no guaranteed bounds on network latency even between correctly functioning nodes), it is not possible to create an algorithm which is guaranteed to reach consensus in any specific finite amount of time if even a single faulty/dishonest node is present. Note that this does NOT rule out ["Las Vegas" algorithms](https://en.wikipedia.org/wiki/Las_Vegas_algorithm) that have some probability each round of achieving consensus and thus will achieve consensus within T second with probability exponentially approaching 1 as T grows; this is in fact the "escape hatch" that many successful consensus algorithms use.
* **Bounds on fault tolerance** - from [the DLS paper](http://groups.csail.mit.edu/tds/papers/Lynch/jacm88.pdf) we have: (i) protocols running in a partially synchronous network model (ie. there is a bound on network latency but we do not know ahead of time what it is) can tolerate up to 1/3 arbitrary (ie. "Byzantine") faults, (ii) deterministic protocols in an asynchronous model (ie. no bounds on network latency) cannot tolerate faults (although their paper fails to mention that [randomized algorithms can](http://link.springer.com/chapter/10.1007%2F978-3-540-77444-0_7) with up to 1/3 fault tolerance, (iii) protocols in a synchronous model (ie. network latency is guaranteed to be less than a known `d`) can, surprisingly, tolerate up to 100% fault tolerance, although there are restrictions on what can happen when more than or equal to 1/2 of nodes are faulty. Note that the "authenticated Byzantine" model is the one worth considering, not the "Byzantine" one; the "authenticated" part essentially means that we can use public key cryptography in our algorithms, which is in modern times very well-researched and very cheap.

Proof of work has been [rigorously analyzed by Andrew Miller and others](https://socrates1024.s3.amazonaws.com/consensus.pdf) and fits into the picture as an algorithm reliant on a synchronous network model. Bitcoin, for example, has 50% fault tolerance assuming zero network latency, ~49.5% fault tolerance under actually observed conditions, but goes down to 33% if network latency is equal to the block time, and reduces to zero as network latency approaches infinity. We often do not realize this because the Bitcoin block time is so long (10 minutes) and even with Ethereum's 14-second block time fault tolerance is still around 46%, but no fundamental violation of established Byzantine fault tolerance theory is made. It is worth noting that proof of work does not have a concept of there being N nodes, with the network being able to tolerate up to 0.495 * N faults; different nodes may have different computing power, however, abstractions have been made that map computing power onto a Byzantine-fault-tolerance-theoretic framework.

Proof of stake consensus is more clearly a Byzantine fault tolerant consensus problem; just like proof of work, most algorithms achieve up to 50% fault tolerance by relying on a synchronous network model. There has also been some research, particularly inside the [Tendermint project](http://tendermint.com/docs/tendermint.pdf) in making a consensus algorithm that relies on either the partially synchronous or asynchronous network models. Both proof of work and proof of stake choose availability over consistency, although Casper's finality mechanism chooses consistency

Note that Ittay Eyal and Emin Gun Sirer's [selfish mining](https://bitcoinmagazine.com/articles/selfish-mining-a-25-attack-against-the-bitcoin-network-1383578440) discovery, which places 25% and 33% bounds on the incentive compatibility of Bitcoin mining depending on the network model (ie. mining is only incentive compatible if collusions larger than 25% or 33% are impossible) has NOTHING to do with results from traditional consensus algorithm research, which does not touch incentive compatibility.

### What is "nothing at stake"?

### What is "weak subjectivity"?

### What is "economic finality"?

### Can one economically penalize censorship in proof of stake?

### What is "stake grinding"?

### Doesn't MC => MR mean that all consensus algorithms with a given security level are equally efficient (or in other words, equally wasteful)?

### What about capital lockup costs?

### How centralized is proof of stake compared to proof of work?

### Will exchanges in proof of stake have a similar role to pools in proof of work?