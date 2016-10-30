### Choosing a codebase

There are currently 8 implementations of the Ethereum protocol:

* Go [http://github.org/ethereum/go-ethereum](http://github.org/ethereum/go-ethereum)
* C++ [http://github.org/ethereum/cpp-ethereum](http://github.org/ethereum/cpp-ethereum)
* Python [http://github.org/ethereum/pyethereum](http://github.org/ethereum/pyethereum)
* Javascript [http://github.org/ethereum/ethereumjs-lib](http://github.org/ethereum/ethereumjs-lib)
* Java [https://github.com/ethereum/ethereumj](https://github.com/ethereum/ethereumj)
* Haskell [https://github.com/jamshidh/ethereumH](https://github.com/jamshidh/ethereumH)
* Rust (Parity) [https://github.com/ethcore/parity](https://github.com/ethcore/parity)
* Ruby [https://github.com/janx/ruby-ethereum](https://github.com/janx/ruby-ethereum)

These implementations differ in:

* License (GPL, LGPL, MIT, Apache)
* Performance
* Feature-completeness
* Modularity
* Ease of modification
* Ease of integration into other systems

For enterprise use cases, performance is likely to be highly important. There have been some efforts at benchmarking Ethereum clients, see:

[https://github.com/ethereum/wiki/wiki/Benchmarks](https://github.com/ethereum/wiki/wiki/Benchmarks)

However, a comprehensive set of benchmarks on all clients likely to be performant (C++, Go, Haskell, Java, Parity) would be a very useful task. Regarding licensing, Go is LGPL licensed, C++ is GPL licensed but there is an effort (with outcome still uncertain) to relicense it to Apache, Java is MIT, and Parity is GPL.

### The config file

There is an informal standard for a configuration file that Ethereum nodes must support, which describes the network parameters. The goal is to allow nodes to easily connect to test networks, private networks, and in the long term specify alternative networks with different properties, including consensus algorithms, P2P networking protocols, initial state, and protocol rules. The standard is described here:

[https://github.com/ethereum/wiki/wiki/Ethereum-Chain-Spec-Format](https://github.com/ethereum/wiki/wiki/Ethereum-Chain-Spec-Format)

An enterprise ethereum version should respect a superset of the standard, so that a node started with the right config settings would act as an Ethereum public chain node (but with more APIs and other features useful for enterprise purposes), but a node started with different settings would participate in a given consortium-chain network using PBFT, or a test network that uses a different virtual machine, or a network that incorporates new scalability features sooner than the public chain, etc.

### Consensus

Currently, the Ethereum clients support proof of work consensus. There is a desire to modularize the consensus algorithm in such a way that it will be possible to also use proof of stake (Casper), as well as private chain-specific consensus algorithms, with likely initial targets being PBFT and DPOS (essentially a round robin consensus algorithm). The first step is to determine exactly what the "interface" for a consensus algorithm "class" should look like.

A possible sketch of the interface is as follows:

* `submitTransaction(tx)`: submit a transaction to a participating node. The node will attempt to include it in a block, or vote for it, or perform the equivalent operation in the consensus algorithm.
* `sendMessage(msg, node_id)`: send a message to another node.
* `broadcastMessage(msg)`: broadcast a message.
* `getMostRecent(conf_level)`: get the most recent block that satisfies a given confirmation level. In proof of work or DPOS, `conf_level` would represent the number of confirmations, so it would return the nth last block in the chain; in PBFT confirmation is binary (ie. final confirmation is instant) so it always returns the most recent confirmed block.

Note that public chain consensus also requires an incentivization model, ie. a way to reward consensus participants who perform well and possibly penalize participants who perform badly. We two abstract this by providing two options:

1. A function `finalize` that gets automatically called after processing every transaction in a block, which makes modifications to the state and may use the block as input.
2. A function `initialize` that gets automatically called before processing every transaction in a block, which makes modifications to the state and may use the block as input.

Note that either `finalize` or `initialize` may simply consist of a call to some standard contract with the block header as data; the Ethereum public chain plans to move to such an approach in the long term. In consortium chains, there is no need to use these functions for incentivization; however, they may be useful for other purposes (eg. to automatically run scheduled operations). Hence, because the utility of these operations is not strictly related to consensus incentivization, they should not be viewed as part of the consensus interface; rather, they should be viewed as transaction processing rules.

In a private chain context, there are three consensus algorithms that make the most sense:

* **Proof of authority** - essentially, one client with one particular private key makes all of the blocks
* **PBFT** (or some other traditional byzantine-fault-tolerant consensus algorithm)
* **DPOS** (or some other chain-based limited-validator consensus algorithm)

PBFT and DPOS (taken purely as a consensus algorithm, not including the ability of some class of token holders to vote on delegates) each have their own advantages and disadvantages. Specifically:

* PBFT has academically robust formal proofs for various properties (safety, liveness, etc) under a partially sychronous network model assuming at least 67% of nodes are online and honest
* PBFT has instant finalty - once a transaction is confirmed once, reverts are impossible. DPOS consensus is probabilistic much like proof of work (in fact, slightly worse, as attackers can see if they have the ability to revert short-range forks ahead of time), and so while a notion of "de-facto finality" is possible, it takes several minutes to reach
* DPOS can support an unlimited number of nodes, whereas PBFT becomes very inefficient above about 30 nodes because every node has to send messages to and receive messages from every other node during every round. DPOS does this because each round consists only of one randomly selected validator making a block
* DPOS is conceptually simpler to understand and implement and intuitively see why it converges
* In a synchronous network model, DPOS can survive up to 49% Byzantine faults, higher than the 33% of PBFT; additionally, DPOS can survive even more faults if they consist of nodes going offline rather than trying to attack the network

Proof of authority may be required in some cases where particular safety properties are required and Ethereum's technologies are used more to provide deterministic and verifiable execution and auditing properties rather than decentralization in the traditional sense. Hence, it makes sense to implement all three and give each user the choice of which one to use for any given application.

**Note**: there is a common misconception that different algorithms have widely different transaction processing capacities, eg. DPOS can process 100 tx/sec while PBFT can process 1000 tx/sec, etc. Whereas in the case of future sharded blockchains this may be true, in the context of blockchains where every node processes every transaction this is not the case; the same amount of computational effort is required to process each transaction regardless of what consensus algorithm is used, although there may be small differences because some algorithms require recomputing transactions in the case of short-range forks, some can more safely allow transaction processing to be run a larger percentage of the time, etc. Differences in performance between chains are usually almost entirely due to differences in the protocols and the implementations, not the consensus algorithm (with the important exception that public-chain capacities tend to be much lower due to their additional economic restrictions, smaller node sizes and centralization concerns).

### Abstraction

In Ethereum protocol development, one of the major overriding philosophies is the notion of abstraction: that the protocol itself should be as simple as possible, and as much as possible should be implemented in contract code instead of through hard protocol rules. Targets for abstraction include:

* Account security (see [https://github.com/ethereum/EIPs/issues/86](https://github.com/ethereum/EIPs/issues/86))
* The transaction state transition function (ie. the rules by which a transaction is processed)
* Ether (ie. make ether into a [ERC 20 token](https://github.com/ethereum/EIPs/issues/20) just like the others)
* Logs / events

The purpose of abstraction is the following:

* Lower attack surface and lower core code complexity
* The ability to more easily hardfork the public chain protocol to different protocol rules, as it can be done simply by swapping out contract code
* The ability to more easily swap out different rulesets on a test network or private implementation
* The ability to switch between different rules on a per-application or per-user level (eg. some users want their accounts secured by ECDSA, some prefer Lamport for quantum-proofness, and in some private chain use cases industry or national standards require the use of specific forms of cryptography; the goal of EIP 86 is to support all of this)

EES may want to implement some of these abstraction features ahead of schedule.

### P2P networking

Ethereum's current P2P network is a Kademlia architecture, and details are described here:

* [https://github.com/ethereum/wiki/wiki/%C3%90%CE%9EVp2p-Wire-Protocol](https://github.com/ethereum/wiki/wiki/%C3%90%CE%9EVp2p-Wire-Protocol)
* [https://github.com/ethereum/go-ethereum/wiki/Peer-to-Peer](https://github.com/ethereum/go-ethereum/wiki/Peer-to-Peer)

A private chain may want to either use the same networking code (but with a different network ID set in the config file), or use an alternative type of network; the most likely alternative is a design where every node connects directly to every other node (quite feasible and likely optimal in networks with under ~20 nodes). Additionally there is the choice of customizing the connection that is made at the network or even lower level (eg. should all nodes be in the same subnet? If they are physically close to each other would fiber optic connections be optimal?); many of the details of this are largely outside the scope of the EES codebase, but the codebase should make sure that it works well under many common expected configurations.

### Privacy-preserving protocols

See [https://blog.ethereum.org/2016/01/15/privacy-on-the-blockchain/](https://blog.ethereum.org/2016/01/15/privacy-on-the-blockchain/) for a much more detailed description of the available approaches.

### APIs

Currently, the Ethereum codebase supports a basic set of APIs for querying the blockchain, querying contract state, filtering logs, sending transactions, etc. The primary way of making more complex queries (eg. returning the balance of a particular account in a particular token issued on the blockchain, returning the ask price of an offer on a blockchain-based order book, etc) is through the concept of "virtual transactions". Essentially, the idea is that a client can pretend to execute a transaction locally, which would call a function of a contract that returns a value. The API would then (possibly synchronously) return the value.

However, there may be more high-level APIs that are required, particularly around use cases that require not just accessing the current state but also accessing previous state, getting information about transactions, and more advanced forms of blockchain analytics; additionally, there is room for APIs that are compatible with API formats that users are already comfortable with and expect.

A particular class of APIs that is likely to be very useful is the ability to make SQL and similar queries to the blockchain. There are a few existing projects in this direction, see:

* [https://www.reddit.com/r/ethereum/comments/4gcsn2/introducing_etherquery_perform_sql_queries/](https://www.reddit.com/r/ethereum/comments/4gcsn2/introducing_etherquery_perform_sql_queries/)
* [https://github.com/jamshidh/ethereum-data-sql](https://github.com/jamshidh/ethereum-data-sql)

However, much more work likely remains in making these systems maximally efficient and broadly applicable to many use cases.

### User interfaces

User interfaces in Ethereum are a broad category, including:

* The Mist browser, as well as applications written in HTML/Javascript that can be used either inside of Mist or through a user's regular browser
* Block explorers such as [etherchain](http://etherchain.org), [etherscan](http://etherscan.io) and [ether.camp](http://live.ether.camp). Note that unlike similar tools for many other blockchains, which focus purely on currency balances and transactions, Ethereum block explorers tend to be highly advanced and general, offering views into contract state, functions, internal transactions, etc; for example, here is a ether.camp's view of the DAO:
* The geth command line interface (the same as the web3 Javascript API).

### Efficiency improvements

The process of verifying a block in Ethereum currently contains the following particularly computationally intensive operations:

* Verifying the transaction signatures
* Running the EVM instance(s) triggered by transaction execution
* Updating the Merkle tree, including computing the new intermediate hashes
* Updating the database

Some benchmarks have been made that show that a single ethereum node is capable of around 1000-2000 transactions per second, and a small consortium network is capable of a few hundred transactions per second, given current software. These limits arise due to a combination of (1) bandwidth limitations, and (2) computational limitations.

In a single transaction, we can expect:

* 1 elliptic curve signature verification (or more precisely, public key recovery)
* 3-10 state updates (minimum 3, corresponding to sender ether deduction, recipient ether increase and miner ether fee payment; state-changing contract calls will have more), corresponding to 3-10 updates to the Merkle tree, requiring ~15-100 rounds of serialization, hashing, etc
* A corresponding ~15-100 updates to the underlying database
* In a contract call, at least one round of VM execution, but possibly more. An informal survey of recent Ethereum transactions showed that among gas-consuming transactions the median gas consumption was ~50,000 gas and the average was ~100,000.

### Virtual machine optimization and precompiled contracts

The Ethereum Virtual Machine is a unique architecture that is optimized for the following properties:

* Small attack surface, assumes that all code running inside of it is untrusted
* Small code size (eg. the 4 kb of "boilerplate" that the default gcc creates for C++ code is acceptable on a single user's hard drive, but completely unacceptable when the code needs to sit on a blockchain; EVM code is often under 100 bytes)
* Fully deterministic (note that due to metering with gas, even timeouts are deterministic; most other smart contract frameworks that try to use existing virtual machines completely fail to accomplish this)
* Very fast to start a new VM instance up and shut an instance down, as we expect 1-10 EVM instances to be created and destroyed **per transaction**. Note that most of these instances may well run less than 100 computational steps; hence, being "lightweight" is crucial.
* Reduced complexity - in those cases where users are going to implement an alternative mechanism in contract code regardless (eg. multisig, ring signatures, on-chain tokens), the "privileged" in-protocol mechanism only serves to duplicate effort and get in the way, whereas abstraction would allow you to use the alternative mechanism to *replace* the default mechanism.
* Having multiple fully compatible implementations (there are ~50000 test cases at [https://github.com/ethereum/tests](https://github.com/ethereum/tests) which all 8 VM implementations in the 8 Ethereum clients pass)

<blockquote>
<b>Important note</b>: "gas" and "ether" are NOT the same thing. Gas is a mechanism that allows computation inside the EVM to be deterministically metered, ie. for contracts to deterministically restrict calls to some fixed number of computational steps. Ether is a way of paying transaction fees, which are expected to be proportional to gas consumption. The Bitcoin analog of ether is BTC, the Bitcoin analog of gas is the number of bytes that a transaction takes up in a block; in Ethereum, measuring bytes alone is not enough as you also need to measure computation, hence the concept of gas. On a private chain, you do not need to use ether to pay for gas; you can come up with alternate rulesets, including for example simply requiring every transaction to have a maximum gas limit of 1 million. 
</blockquote>

Note that the original EVM was **not** designed for high-performance computation. When the EVM 1.0 spec was created, most ideas for what can be done inside of blockchain scripting were under 50 lines of code, consisting of "if...then" logic and basic arithmetic, and in this environment one can make a strong case that even 100x inefficiency in EVM execution will not make a significant difference to total transaction processing times because the bulk of transaction processing load appears in the form of verifying elliptic curve signatures (which requires looping through elliptic curve additions over 1000 times, where each addition itself contains many 256-bit operations).

However, more recently we have realized that there is a very large demand for high-performance computation in the EVM particularly in order to implement more complex cryptography (eg. ring signatures, partially homomorphic encryption, other kinds of elliptic curves, lamport signatures); hence, we have realized that our current work on the EVM has been insufficient. We currently have two approaches that we are pursuing in parallel toward solving this:

* A possible new virtual machine based on WebAssembly (see [https://github.com/ethereum/evm2.0-design](https://github.com/ethereum/evm2.0-design))
* EVM improvements
* Precompiled contracts

The WebAssembly plan is to develop a "trans-compiler" that takes WebAssembly code and convert it into WebAssembly code that keeps track of its own "gas consumption" at every branch point, and automatically stops if the remaining gas goes below zero. WebAssembly was chosen because the design requirements are in many cases similar to Ethereum, eg. users will be running untrusted WebAssembly code in their browsers, code sizes also need to be small as code is downloaded in real time, and there are already multiple implementations and a goal of compatibility between them. However, WebAssembly lacks a notion of gas counting, and the trans-compiler will fix that problem while allowing the use of an unmodified WebAssembly implementation to do the actual execution. It will also ban floating point numbers and other sources of potential nondeterminism. Note that it is not guaranteed that WebAsssembly will become the main public chain VM, but it may make sense to develop it further and make it a candidate for private chain use.

The leading EVM improvement proposal is to add a set of 256 64-bit registers, plus opcodes specialized to dealing with those registers. This allows operations that only require 64-bit precision to be processed with near-native speed. If this proposal is implemented, then it could potentially be merged with the WebAssembly route, by making a two-way compiler between the upgraded EVM and WASM and thus making WASM be one of the implementations of this upgraded EVM.

A "precompiled contract" is an operation that is implemented in native code, and which can be accessed by calling a contract at a prespecified address. Current precompiles include:

* Elliptic curve signature public key hash recovery (address `0x000....0001`)
* SHA256 (address `0x000....0002`)
* RIPEMD160 (address `0x000....0002`)

In many industry use cases, there is the goal of either integrating existing libraries for financial computation (eg. [OpenGamma](http://www.opengamma.com/)), or different forms of cryptography in order to comply with industry or national standards, or accelerating complex business logic (eg. order books) that is required to run at very high speed. Applications that do so may wish to specify their own precompiles. We recommend that private-chain precompiles take addresses in the range `0x000....000400` to `0x000....0fffff` (ie. 1024 to 1048575) to avoid collisions with possible future public chain features.

### Denial-of-service attacks and Security

In September and October 2016 there has been a series of denial-of-service attacks against various Ethereum implementations and the Ethereum protocol. A large portion of them exploited a "quadratic memory consumption" vulnerability in the Go implementation, where the client inefficiently copied a potentially large amount of cached state every time a call was made, leading to either very slow processing or an outright halt due to the client running out of memory. Additionally, a large number of attacks arose because opcodes that read the state (eg. EXTCODECOPY, BALANCE, CALL) were incorrectly priced too low, and because a design flaw in the SUICIDE opcode presented a way to very cheaply bloat the state with a large number of empty accounts. These issues were resolved in a hard fork on Oct 17.

As of the time of this writing, the main remaining denial-of-service issue is that the client makes O(log(n)) database reads in order to read a state entry, and when the state is too large to fit into memory (which is the case after the state increased greatly due to previous attacks that are no longer possible) this makes processing state-reading opcodes slow.

There are two ways to resolve this issue. One is to implement a cache that allows state entries to be read in O(1) database reads (likely a single database read in an optimal implementation); this resolves the problem but still leaves a low limit for transaction processing capacity. A single SSD read takes [100 microseconds](http://www.thessdreview.com/featured/ssd-throughput-latency-iopsexplained/); hence a leveldb read may take 200-400 microseconds, creating a cap of ~2500 reads per second, or ~500 transactions per second. The requirement for an offline node to be able to synchronize decreases this further. Writes take up to 10x longer due to the need to update the state tree (which adds O(log(n)) overhead), but this can be done in a background process. Hence, storing state in an SSD makes it difficult to process more than ~100 tx/sec effectively.

The other approach is to implement measures to ensure that the total size of the state remains small enough to fit into memory. In a public chain, this entails either charging "rent" for accounts (see [here](https://github.com/ethereum/EIPs/issues/87), [here](https://github.com/ethereum/EIPs/issues/35), [here](https://github.com/ethereum/EIPs/issues/88) and [here](http://vitalik.ca/files/storage_rent.html) for proposals); in a consortium chain, this simply entails having a good garbage collection strategy and if needed adding more RAM to validator nodes.

### The Merkle Tree

The Merkle tree (also sometimes called "Merkle Patricia tree", "Patricia trie", "Merkle Patricia trie" or "trie") is an important part of the Ethereum protocol and provides a large amount of value particularly in the public chain. The function of the tree (see [here](https://github.com/ethereum/wiki/wiki/Patricia-Tree) for details, and [here](https://blog.ethereum.org/2015/11/15/merkling-in-ethereum/) and [here](https://easythereentropy.wordpress.com/2014/06/04/understanding-the-ethereum-trie/) for more detailed explanations) is to provide a cryptographically authenticated data structure that stores the entire state (ie. account balances, contract storage, nonces, etc); every 32-byte root hash maps uniquely (assuming cryptographic security of SHA3) to a particular state which may be gigabytes in size.

This allows for the following benefits:

* **Secure fast syncing**: new clients joining the network do not need to process every transaction from the genesis block to the present moment in order to download the state. Instead, they can simply download the block headers, verify the proof of work (or potentially proof of stake or signatures from PBFT participants), find a recent finalized block, and from the root hash contained in that block download the entire current state and authenticate it by checking that every hash in the trie matches up.
* **Light client support**: a client that does not have enough computational power, storage or bandwidth to download and process even the entire current state may simply keep track of block headers and use the consensus algorithm to authenticate block headers, and then download and verify "branches" of the Merkle tree just in time when the client needs to get some particular piece of state data.
* **Independent light verifiability**: a block creator can come up with a "proof", consisting of a block together with the portion of the state data that is accessed or modified during that block, that any client even without any other data can use to verify that the block was created and the transactions were executed correctly.

Although no one has yet come up with such a product at the time of this writing, one can imagine a centralized service running its business logic single-node private chain that uses the independent light verifiability feature of the Merkle tree in order to provide its users with very strong auditability and authenticity guarantees. For example, an exchange may commit to providing an API that allows any user to download the Merkle branches corresponding to their account data at any time, and also publish the root hash of its internal state after every operation. If a user sees their balance unexpectedly decreased and wants to be sure that there was no malfeasance, they can use a binary search algorithm to determine after which operation their balance was reduced, and then download a proof-of-execution for that operation and verify that the operation was legitimate. Hence, Merkle trees have benefits in many consortium and private chain applications as well as the public chain.

However, the Merkle tree also comes with its efficiency cost; hence, in those applications that do not require it, an optimal solution may be to simply get rid of the Merkle tree, and instead store the state in the database directly, so three state changes would correspond to three database operations. That said, it is important not to jump to this conclusion too quickly, as there are several ways to optimize the Merkle tree without removing it entirely. This includes:

* Allowing the storage of unlimited-size values in the state, instead of just 32-byte values (see [EIP 97](https://github.com/ethereum/EIPs/issues/97)). In cases where a contract very often needs to store many values at the same time (eg. a blockchain-based order book storing buy currency, sell currency, price, quantity, seller), this may improve efficiency by as much as 2x.
* Removing the storage of intermediate root hashes after every transaction, instead doing it after every block. This (1) allows for the cache to be used more, so if many transactions in a block modify a single account only a single Merkle tree update needs to be done for that account for the entire block, (2) allows the Merkle tree to be updated using a more efficient algorithm (eg. see in [the benchmarks](https://github.com/ethereum/wiki/wiki/Benchmarks) the Go client, which has this optimization, needs 28.5 milliseconds instead of 45.7 to update when the number of intermediate root hash calculations is reduced by 40%), (3) allows the Merkle tree to be updated using a more parallelizable algorithm, and (4) potentially opens the door to some heuristic parallel transaction execution optimizations.
* Changing the serialization algorithm or the trie algorithm (eg. making it a binary trie instead of hexary as it is now)
* Software-level improvements

The optimal strategy is to pursue three paths (namely, adding an option to instantiate an ethereum chain without a Merkle tree, exploring protocol hardforking options to make the Merkle tree more efficient, and exploring software-level improvements to the code) in parallel.

### Transaction Parallelizability

One common criticism (see [http://www.multichain.com/blog/2015/11/smart-contracts-slow-blockchains/](http://www.multichain.com/blog/2015/11/smart-contracts-slow-blockchains/)) of the current Ethereum model is that because it is so open-ended in the effects that transactions can have, verifying transactions in parallel is difficult. Once intermediate state root computation is removed, one heuristic strategy that can be employed works as follows:

1. Select the next `k` transactions, `t[1] ... t[k]`, that need to be processed. Process them all in parallel threads on top of the current state (giving each one a separate cache if it reads data that it already wrote), and during execution keep track of the set of addresses that each thread read and the set of addresses that each thread wrote to.
2. Let `(i, j)` be the earliest collision (ranked by `j`) where (i) `i < j`, (ii) `t[j]` read something that transaction `t[i]` wrote.
3. Apply the state updates from `t[1] ... t[j-1]` inclusive. Go back to step 1, starting from `t[j]` as the first transaction.

This may achieve substantial speedups in the general case, but that is not guaranteed; additionally, relying on the scheme may make your application vulnerable to denial-of-service attacks where the attacker sends a large number of deliberately unparallelizable transactions in order to prevent any parallel execution from taking place - in the worst case, performance will be no better than the single-threaded case. The Merkle tree changes detailed above will dditionally allow for the parallelization of Merkle tree calculations, and elliptic curve signature verifications are precomputed in existing clients and can be parallelized already, but this is currently as far as we can go.

The long-term roadmap for Ethereum 2.0 and 3.0 is to implement the concept of "sharding", where the state is broken up into many pieces and each piece is stored by a portion of nodes, and transactions affecting different parts of the state are processed by different nodes in parallel. Part of this roadmap is allowing transactions to statically declare an address range within which they are able to make synchronous operations, so that processing these transactions without holding the entire state is actually feasible. Calls going outside the stated address range would return an `OUT_OF_RANGE` error, similar to `OUT_OF_GAS` errors in effect.

Public blockchain sharding is a complicated technical challenge, as on top of the usual problems involving database sharding there are also cryptoeconomic challenges, as the particular form of distributed computing that a public blockchain is has the unique property that each node does not trust any other node, and so another node simply _saying_ that it processed and verified a given set of transactions is not sufficient. However, in the case of private and consortium chains, going this far is not required; instead, it suffices only to implement the part of the sharding roadmap that has to do with transaction parallelizability (ie. transactions specifying address ranges), and then achieve scalability by requiring each node in the network to simply add more CPU cores and more bandwidth.

One challenge with this kind of sharding is that there will be many operations in many applications that need to be cross-shard, ie. have an effect that is dispersed across the state such that a single transaction with a narrow address range cannot cover it. The solution for these cases is an asynchronous programming language, allowing operations to be split up into multiple phases. For example, a coin transfer from shard A to shard B would consist of the following steps:

1. Destroy X coins on shard A, in a transaction that also specifies the destination shard and address; the consensus mechanism generates a receipt proving that this operation was confirmed.
2. Submit this receipt into a contract in shard B, which then generates X new coins on shard B. It then saves a record into the storage of shard B stating that the receipt was consumed, preventing it from being used to generate new coins a second time.

See [this presentation](https://docs.google.com/presentation/d/1CjD0W4l4-CwHKUvfF5Vlps76fKLEC6pIwu1a_kC_YRQ/edit?ts=575e81ff#slide=id.p) ([video](https://www.youtube.com/watch?v=-QIt3mKLIYU)) for more details on how this would be done.

An important higher-level task is to actually create the programming language that would make it easy to implement these kinds of asynchronous cross-shard operations. Note that a lot of this work would simultaneously lay the groundwork for a programming language that compiles down into applications that exist across multiple blockchains.

### Smart Contract Safety

An important aspect of smart contract programming that touches public and consortium chain smart contracts alike is safety, including both protection against benign developer mistakes, and against malicious exploits included by the author of a contract in order to attempt to cheat users. A compiled list of contract programming mistakes on the public Ethereum blockchain can be found here:
  
[https://blog.ethereum.org/2016/06/19/thinking-smart-contract-security/](https://blog.ethereum.org/2016/06/19/thinking-smart-contract-security/)  

An older paper from security researcher Andrew Miller can be found here:
  
[https://eprint.iacr.org/2015/460.pdf](https://eprint.iacr.org/2015/460.pdf)    
  
The Ethereum wiki doc on safe contract programming techniques can be found here:    
  
[https://github.com/ethereum/wiki/wiki/Safety](https://github.com/ethereum/wiki/wiki/Safety)    
  
The first link also includes possible incremental improvements that can be taken to the EVM as well as Ethereum development environments in order to minimize the risk of such attacks. Additionally, since the DAO incident in June 2016 there has been a number of proposed EIPs in order to facilitate safer contract development: [EIP 114](https://github.com/ethereum/EIPs/issues/114), [116](https://github.com/ethereum/EIPs/issues/116), [117](https://github.com/ethereum/EIPs/issues/117), [118](https://github.com/ethereum/EIPs/issues/118) and [119](https://github.com/ethereum/EIPs/issues/119). It is likely not wise for the consortium chain development community to implement safety-focused EIPs ahead of the public chain ecosystem, as we do not want to confuse developers by having one set of safe contract programming practices for the consortium chain ecosystem and one for the public chain ecosystem (except where absolutely unavoidable, eg. on the public chain, game-theoretic issues are much more inescapable).

Aside from low-level improvements, there are also higher-level tools that can be built in order to improve the safety of the contract programming ecosystem. These fall into two major categories:

1. New programming languages (using Solidity, Serpent, LLL or EVM as a compile target)
2. Formal verification, symbolic execution or other static analysis tools for either new programming languages or existing programming languages

Note that one must be careful about how these tools are integrated with the underlying environment; for example, pure functional languages are often cited as a solution to Ethereum contract programming challenges, but if we look at the specific case of the DAO theft we can see that it arose due to a "re-entrancy" hack where a contract called an untrusted contract which then called the original contract, and even if you try to implement a purely functional language over the current EVM, you will run into the challenge that if you need to call another contract it is extremely difficult to be sure that the child contract will not make some "impure" state-changing operations unless an EVM-level opcode such as `STATIC_CALL` is implemented.

In general, it is not likely that there will be a single magic technology that solves all smart contract safety issues; rather, we will see a combination of incremental approaches including new languages, better analysis tools, better development tools, better standards and best practices and changes to the underlying EVM. Better standards and best practices should be integrated into development tools in order to make them easier for programmers to adapt to; a large part of the value of blockchain technology is in reducing barriers to entry in building high-trust systems, and if the previous barriers to entry are only replaced with a requirement of years of specialized developer education then the potential of the technology is arguably significantly reduced.

### State channels

State channels are a popular short-term scalability, privacy and latency solution for blockchain technologies. Some information on state channels can be found here:

* [http://www.arcturnus.com/ethereum-lightning-network-and-beyond/](http://www.arcturnus.com/ethereum-lightning-network-and-beyond/)
* [http://www.jeffcoleman.ca/state-channels/](http://www.jeffcoleman.ca/state-channels/)
* [http://ethereum.stackexchange.com/questions/342/what-are-payment-channels-can-they-be-implemented-on-ethereum/1648](http://ethereum.stackexchange.com/questions/342/what-are-payment-channels-can-they-be-implemented-on-ethereum/1648)

Some implementations include:

* [https://github.com/AnnaIsAWang/LedgerLabsCoops2016](https://github.com/AnnaIsAWang/LedgerLabsCoops2016)
* [http://raiden.network](http://raiden.network)

### Privileged Accounts

Many consortium chain applications require some node (eg. a regulator) to have special privileges. Possible privileges include:

* The ability to freeze balances
* The ability to stop execution of a smart contract
* The ability to change the code of a smart contract
* The ability to see plaintext state information that is only seen in encrypted form by most participants (eg. balances, contract terms)

In some cases, these privileges are best implemented in layers on top of the base protocol; for example, in a chain where the issuing of new smart contracts is heavily restricted (or even where there is only one contract for the entire chain), one can implement such features into the code of the contracts themselves. In the case of privacy-preserving solutions, giving regulators the ability to view plaintext state can only be done in layers on top because the privacy-preserving solutions themselves will be built through layers on top.

In other cases, these privileges are best implemented via changes to the underlying protocol. The simplest way to do this is to introduce some new opcodes, eg. `0xed = SSTORE_EXT` (set storage of external account), `0xee = WITHDRAW` (drain ETH from another account), `0xef = SET_CODE` (set code of another account), and give only one address the priviledge of using these opcodes (eg. address 254). Such a "privileged address" is a feature that is likely to be implemented in Ethereum in the future as part of the abstraction roadmap (in the public chain, only system-level contracts such as a hypothetical contract that would implement the contract creation function would have access to this priviledged address, so it will not be usable as a "backdoor" by any organizations or individuals, but in private and consortium chains it can be dual-purposed as a priviledge mechanism).

### Changing the Consensus Set

Once a consortium chain is launched, there will inevitably be reasons to change the set of consensus participants after the fact. This includes:

* Induction of new members into the consortium
* Removal of new members of the consortium
* Change of the private keys used by one or more members of the consortium

There are several approaches to handling this:

* Have the consensus set be fixed in the config file, and make every consensus set change a hard fork
* Store the consensus set in a contract, where the contract itself encodes rules for updating the consensus set
* Use a secure external mechanism to store the consensus set (eg. the Ethereum public blockchain)

A consensus abstraction should be able to cover all three modes if needed. If either (i) the consensus set is stored in an in-chain contract and the consensus algorithm does not provide instant finality (eg. DPOS), or (ii) the consensus set is stored in an external mechanism, then it is prudent to have a delay between when a consensus set change is made and when the change becomes active. The approach currently taken by the Casper proof of stake protocol planned for the public chain is to split blockchain time into 12-hour "epochs", and use the validator set defined at the start of the previous epoch (ie. a time which is always between 12 and 24 hours in the past) as the consensus set for the current block.

The reason why you may want to use an external mechanism is to provide greater security assurances for frequently-offline nodes. Specifically, especially if the consensus set is small there is a risk that, over some period of time, a majority of historical keys will be compromised, and if an attacker obtains these keys they will be able to create an alternate chain and trick long-offline nodes that this chain is legitimate. Using an external mechanism to store consensus participants makes it much more difficult for this to happen; though this approach may not be appropriate for all applications and in many cases the consortium itself may be sufficiently large that a 50% historical key compromise is very unlikely.

Note that a well-designed setup could even tolerate short-term failures of the external mechanism by using a consensus algorithm that can tolerate small divergences in perceived consensus mechanism; this would also require restricting the rate at which consensus participants could change and having a rule that if the mechanism is unavailable then the most recent available consensus set should be used (though this is highly theoretical).