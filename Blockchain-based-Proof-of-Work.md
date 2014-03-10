One of the key ingredients in a standard cryptocurrency is the idea of proof of work. A proof of work, in general, if a function which is hard to compute, but easy to verify, allowing it to serve as a probabilistic cryptographic proof of the quantity of computational resources controlled by a given node. In Bitcoin, and Ethereum, this mechanism is intimately tied in with the blockchain: every block requires a proof of work of some prespecified difficulty level in order to be valid, and in the event of multiple competing blockchains the chain with the largest total quantity of proof of work is considered to be valid. Thus, in order to reverse a transaction, an attacker needs to start a new fork of the blockchain from before the block the transaction was confirmed in, and then apply more computational power than the rest of the network combined in order to overtake the legitimate fork.

However, one of the key requirements for a proof of work algorithm to, well, work is decentralization. If the proof of work algorithm is designed in such a way that the proof of work computation can only be efficiently done by entities with the millions of dollars of capital required to develop specialized hardware, then the number of participants in the process will be small enough that it will be possible for the majority of miners to conspire and reverse transactions. Alternatively, if the proof of work algorithm encourages miners to outsource their block verification work to centralized entities, then that is another way that centralization can creep in, and it would be the mining pools that have the potential to form conspiracies. Ideally, proof of work algorithms should solve both of those problems.

### Blockchain Based PoW Specification

This mining algorithm is based on Adam Back's Hashcash, also used by Bitcoin. In Hashcash, we take a hash function `H` (assume 256-bit length) which takes as input data and a nonce and a difficulty parameter, and say that a valid nonce is one where `H(data,nonce) < 2^256 / difficulty`. Completing the proof of work essentially entails trying different nonces until one works, and verifying means applying the hash function to the data and nonce provided and making sure that the result is indeed below the target. In Bitcoin, `H` is a simple computation of `sha256(block_header + nonce)`. Here, however, `H` is much more complex, taking in as data not just the block header but also the state data and transactions from the last 16 blocks.

`H` is defined as follows:

1. Let `h[i] = sha3(sha3(header) ++ nonce ++ i)` for `0 <= i <= 15`
2. Let `ind[i] = h[i]` modulo the number of transactions in block `N-1-i` where `N` is the current block number.
3. Let `t[i] = txs[ind[i]]` where `txs` is the list of transactions in block `N-1-i`
4. Let `S` be the state at the beginning of block `N-16`. Sequentially apply `t[0]`, `t[1]` ... `t[15]` to `S`, and let `S'` be the resulting state.
5. Return `sha3(S'.root)`

Three attributes:

0. Transactions should be chosen with probability weighted according to the difficulty of the contract (i.e. fees changed), with a minimum number of contract transactions used (e.g. 50%).

1. Senders & receivers of the transactions should change, probably by rotation through the merkle (state) tree addresses - receivers, if a contract, should rotate to another contract. Maybe don't rotate if the contract is of over-average difficulty.

2. Contracts should be slightly corrupted. This can happen either by random spewing of data in the lower part of contract memory, exchanging arbitrary non-zero locations, or rotation of known instructions through their sets (e.g. arithmetic operations).

### Properties

1. The algorithm is memory-hard; mining requires the miner to store the full state of the last 16 blocks in order to be able to query the blockchain. However, the algorithm is not sequentially memory-hard and is vulnerable to shared memory optimizations.
2. The algorithm requires every node to store the entire blockchain state and be able to process transactions. Furthermore, as new blocks are created, it will likely be more efficient to generate the new blockchain state by computing it rather than by asking for and downloading the missing Patricia tree nodes. Hence, there is no reason why a miner would not want to be a full node.
3. The EVM code is Turing-complete. Hence, an ASIC that performs transaction processing vastly more efficiently than existing CPUs would necessarily be a general-purpose computing device vastly more efficient  than existing CPUs. Thus, if you can make an Ethereum ASIC, you can push the entire computing industry forward by about 5 years.
4. Because every miner must have the full blockchain, there is no equivalent to the Bitcoin mining strategy of only downloading headers from a centralized source. Hence, centralized mining pools offer no benefits over p2pools, and so miners are more likely to use p2pools.
5. The algorithm is relatively computationally quick to verify, although there is no "nice" verification formula that can be run inside EVM code.

(3) and (4) combined basically mean that this proof of work algorithm provides both forms of decentralization, and (2) helps prevent centralization due to blockchain bloat since it helps ensure that at least miners will store the chain.