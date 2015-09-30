---
name: Security Categorization
category: 
---

See also: [https://github.com/ethereum/wiki/wiki/Design-Rationale](https://github.com/ethereum/wiki/wiki/Design-Rationale), for descriptions of potentially counterintuitive design decisions in Ethereum.

The purpose of this document is to attempt to create a taxonomy of key security properties that we are targeting for the Ethereum protocol, implementation and materials to have at launch time, as well as provide supplementary information where needed for each one.

### Yellow Paper

* Paper is free of typos and mathematical and grammatical errors
* The definitions in the paper are clear and unambiguous
  * Pay particular attention to external dependencies, eg. the definition of secp256k1
* In places where the formal definition deviates drastically from all commonly used intuitive definitions, clear explanations are provided

### Proof of Work

The working spec description of Dagger Hashimoto is available at  [https://github.com/ethereum/wiki/wiki/Ethash](https://github.com/ethereum/wiki/wiki/Ethash)

The algorithm is intended to have the following key properties:

* ASIC resistance (ie. lowest possible ASIC speedup coefficient)
* Light-client verifiability, in <1s on Python or Javascript on a desktop or C++ on a Raspberry Pi

And should not have any of the following flaws:

* High centralization risk (ie. unboundedly superlinear return)
* Possibility of superior "complex" software implementations (eg. as in SAT solving)
* Cheap evasion of memory hardness (due to light-client verifiability considerations, a low-memory implementation must exist, but this should not be a practical way of mining, and hybrid reduced-memory implementations should also be uneconomical)
* Outright trivial attack (eg. the underlying function is actually polytime invertible)
* Pool resistance
* If pools are going to happen, p2pools should be a viable option

### Block algorithm

A simulator of difficulty adjustment is available at https://github.com/ethereum/economic-modeling/blob/master/diffadjust/blkdiff.py .

The modified GHOST algorithm is defined [here](https://github.com/ethereum/wiki/wiki/White-Paper#modified-ghost-implementation).

The following properties are desired:

* The difficulty adjustment should update quickly to increasing or decreasing hashpower and should not be maliciously gameable in any sense.
* The 12 second block time + GHOST should not have perverse incentives (ie. it should be optimal to mine on the head)
* The 12 second block time should not induce an excessively high orphan rate and higher confirmation times than expected.
* Ethereum should remain reasonably light-client friendly, especially for low-power IoT (patch solution if necessary: block skiplists, eg. every block with number % n^k == 0 must refer to the block n^k blocks ago, for say n = 4 and k = 0...3; this basically entails turning prevhash into an array)
* Blockchain bloat: blocksize too big to incentivize new full nodes. Will it be dynamically adjusted? If so,  also rogue miners attack to bloat the blockchain
* It should be expensive to generate incentives not to mine on the best chain using baits inside of smart contracts

### Gas economics

* The floating gas limit should achieve the compatibility objectives described in https://github.com/ethereum/wiki/wiki/White-Paper#fees
* There should not exist a way for a transaction to get itself executed and spend large amounts of computational resources on many nodes without paying for gas
    * The following is an example vulnerability: a miner can create a transaction A that spends a very large amount of gas (eg. ~gaslimit * 0.99) running useless computation. That miner then creates a transaction B with the same nonce that has no effect, and tries to mine a block containing B. If a miner produces a block containing B, it broadcasts A, forcing all other nodes on the network to waste time processing A, even though A actually getting accepted is nearly impossible since B is already published in a block.
* There should not be any kind of of perverse incentive in the way gas costs work on the high level (eg. how gas is passed from parent to child message, how gas is refunded, exception reversions, etc)
* Storage gas refund mechanism should not be exploitable
* The operation gas costs should match the cost of running the operation at least complexity-theoretically (ie. there should be no way of causing O(n^2) computational load with O(n) gas).
    * An n*log(n) vulnerability against `JUMP`s in the JIT VM is known, but is deemed not sufficiently catastrophic to worry about
    * An example prior vulnerability in PoC6 was that since `CALLDATACOPY` always cost 1 gas, you could incur n^2 computational load with n `CALLDATACOPY` instructions on n bytes of memory (ie. O(n) gas)
    * A good implementation of `CALL` and `CREATE` should never actually copy memory to produce call data. Otherwise, it will be vulnerable to a quadratic execution attack where a contract runs CALL n times on n bytes of memory, causing n^2 computational load on O(n) gas
    * The return data of a `CALL` is not a source of a quadratic execution vulnerability because the `RETURN` opcode expands memory in each inner call, thereby costing `n` memory expansion gas `n` times.
* Expanding the amount of data stored in the state should cost at least 5 gas per byte in all cases
* There should not exist some operations are much more expensive to execute per unit gas than others, leading to a high "multiplicative griefing factor". Note that this is not a primary concern for security audits; we will perform profiles of each operation ourselves later on in order to set gas costs optimally.

### VM

* The VM implementation and the formal specifications (so called Yellow Paper) should be equivalent. The VM implementation can be found at https://github.com/ethereum/go-ethereum/tree/master/vm
* The VM should be resistant to the following attack scenarios:
  * Transactions/messages whose execution will somehow escape the VM and either reveal or modify memory or hard drive contents of the client's machine
  * Transactions/messages whose execution will cause a system crash
  * Transactions/messages that could be processed differently by different machines
  * Transactions/messages that have a much higher execution time / gas cost ratio than most others, opening the door to a denial-of-service attack

### Wire protocol 

The wire protocol is described here: https://github.com/ethereum/wiki/wiki/%C3%90%CE%9EVp2p-Wire-Protocol

The wire protocol should be secure against the following issues:

* Node isolation against the block spread algorithm
* Single account transaction paralelism ratio due to nonce validation in peers (vs DoS prevention)
* Sequences of wire messages that would lead to the recipient spending a very large amount of time responding to them
* Sequences of wire messages that would lead to the recipient storing a large amount of data
* Sequences of wire messages that would lead to the recipient storing data that is specifically crafted to make data structures blow up (eg. sending transactions with hashes in sequence in order to make trees very very deep)
  * Any third party software, e.g. leveldb would also fall under this scrutiny - e.g. what kind of tree is leveldb? Is it vulnerable to such DoS attacks?
* Messages that would fingerprint the target

### JS / RPC

* Check that there are no exploits in the Javascript API, JSON-RPC API 
* Define a security model for browser+js bindings. (-or give nathan@leastauthority.com a link to an existing doc.)

### Libraries

* Leveldb
  * Check for hidden limits in leveldb and other libraries that might trigger inconsistent failures outside the VM, similar to http://bitcoinmagazine.com/3668/bitcoin-network-shaken-by-blockchain-fork/
* Crytopgraphic libraries
  * secp256k1 with a Go bridge (https://github.com/obscuren/secp256k1-go)
  * A fork of the official golang sha3 package (https://github.com/obscuren/sha3) (Reason for using a sha3 fork is because the official sha3 has switched to FIPS-202 which Ethereum can't use because genesis addresses have been generated using an earlier version of sha3)
  * Official ripemd package (code.google.com/p/go.crypto/ripemd160)

### Non-Audit security-related product wishlist:

* How-to write secure contracts
* How-to write secure html+js front-ends
