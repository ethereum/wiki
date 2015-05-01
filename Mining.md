## So what is mining anyway?

Ethereum Frontier like all blockchain technologies uses an incentive-driven model of security. Consensus is based on choosing the block with the highest total difficulty. 
Miners produce blocks which the others check for validity. Among other well-formedness criteria, a block is only valid if it contains **proof of work** (PoW) of a given **difficulty**. 
Note that in Ethereum 1.1, this is likely gonna be replaced by a **proof of stake** model.

[The proof of work algorithm used is called [Ethash](https://github.com/ethereum/wiki/wiki/Ethash) (a modified version of [Dagger-Hashimoto](https://github.com/ethereum/wiki/wiki/Dagger-Hashimoto) involves finding a nonce input to the algorithm so that the result is below a certain threshold depending on the difficulty. The point in PoW algorithms is that there is no better strategy to find such a nonce than enumerating the possibilities while verification of a solution is trivial and cheap. If outputs have a uniform distribution, then we can guarantee that on average the time needed to find a nonce depends on the difficulty threshold, making it possible to control the time of finding a new block just by manipulating difficulty.

The difficulty dynamically adjusts so that on average one block is produced by the entire network every 12 seconds (ie., 12 s block time). This heartbeat basically punctuates the synchronisation of system state and guarantees that maintaining a fork (to allow double spend) or rewriting history is impossible unless the attacker possesses more than half of the network mining power (so called 51% attack).

Any node participating in the network can be a miner and their expected revenue from mining will be directly proportional to their (relative) mining power or **hashrate**, ie., number of nonces tried per second normalised by the total hashrate of the network.

Ethash PoW is memory hard, making it basically ASIC resistant. This basically means that calculating the PoW requires choosing subsets of a fixed resource dependent on the nonce and block header. This resource (a few gigabyte size data) is called a **DAG**. The [DAG](https://github.com/ethereum/wiki/wiki/Ethash-DAG) is totally different every 30000 blocks (a 100 hour window, called an **epoch**) and takes a while to generate. Since the DAG only depends on block height, it can be pregenerated but if its not, the client need to wait the end of this process to produce a block. Until clients actually precache dags ahead of time the network may experience a massive block delay on each epoch transition. Note that the DAG does not need to be generated for verifying the PoW essentially allowing for verifiction with both low CPU and small memory.

As a special case, when you start up your node from scratch, mining will only start once the DAG is built for the current epoch. 

## The Algorithm

Our algorithm, [Ethash](https://github.com/ethereum/wiki/wiki/Ethash) (previously known as Dagger-Hashimoto), is based around the provision of a large, transient, randomly generated dataset which forms a DAG (the Dagger-part), and attempting to solve a particular constraint on it, partly determined through a block's header-hash.

It is designed to hash a fast verifiability time within a slow CPU-only environment, yet provide vast speed-ups for mining when provided with a large amount of memory with high-bandwidth. The large memory requirements mean that large-scale miners get comparatively little super-linear benefit. The high bandwidth requirement means that a speed-up from piling on many super-fast processing units sharing the same memory gives little benefit over a single unit.

### Formal Requirements

TODO: Content from formal requirements doc.

### Design Decisions Taken

TODO: Content from design decisions doc.

## Infrastructure Overview

Mining will be accomplished in one of two ways: either on CPU (and possibly the GPU, to be confirmed) with the Mist client or on the GPU though a combination of the Ethereum daemon and [sgminer](https://github.com/sgminer-dev/sgminer).

An sgminer module for Ethash is expected to be released at some point during, but not necessarily before the Frontier Genesis.

### JSON-RPC

Communication between the external mining application and the Ethereum daemon for work provision and submission happens through the JSON-RPC API. Two RPC functions are provided; `eth_getWork` and `eth_submitWork`.

These are formally documented on the [JSON-RPC API](https://github.com/ethereum/wiki/wiki/JSON-RPC) wiki article.

