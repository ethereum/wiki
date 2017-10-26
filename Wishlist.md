This document outlines known flaws and missing features in current versions of Ethereum that we would like to see fixed in future versions.

### Sharding

See https://github.com/ethereum/wiki/wiki/Sharding-FAQ

### Proof of Stake

See https://github.com/ethereum/wiki/wiki/Proof-of-Stake-FAQ

### Trie

The current trie has the following known problems:

* Design more complicated than it needs to be.
* RLP is complex (see below) and slows down serialization/deserialization time, especially in slow interpreted language implementations (eg. python).
* Because the trie is hexary and not binary, the size of a Merkle branch of any value is ~3.75x longer than needed. Because our Merkle branch encoding is suboptimal, this increases to 4x. This is because in a binary tree with 2^n key/value pairs, the size of a node is 64 bytes, and there are n nodes in a branch, so a naive Merkle branch will be 64*n bytes long. If we make the optimization of not storing the part of each node that is simply the hash of the next node, this decreases to 32 * n. In a hexary tree, each node is 512 bytes, and there are n/4 nodes in a branch, so a naive Merkle branch is 512 * n/4 = 128*n bytes long. The de-duplication optimization reduces this to 120*n. Practically speaking, this means that in a trie with 1 billion key/value pairs, an optimal Merkle branch would be 960 bytes long, but ours is 3840 bytes (plus more due to overhead). The trie was originally done in a hexary format to reduce the number of required database lookups to fetch a value, but these gains can be achieved in a different way by clumping together nearby trie nodes when they get stored in the database.

A possible solution is to switch to a binary trie. See an implementation [here](https://github.com/ethereum/research/tree/master/trie_research).

### More expressive Merkle trees

Certain kinds of mechanisms, to be developed on Ethereum, require a richer set of data structures then just arrays, key/value maps, etc; the most commonly asked-for one is a heap (or an equivalent data structure that supports push/pop/top in logtime), highly useful in front-runner-proof markets among other applications. Currently, implementing a heap in Ethereum will lead to log^3(n) overhead, as we have a tree (the heap) on top of a tree (the account storage Patricia tree) on top of a tree (leveldb). Making a custom DB for the Patricia tree will likely need to be done at some point anyway, and will remove one level of overhead. However, the larger gain will come from the ability to have a Merklized heap-like structure, essentially making the heap operations native to the protocol. This reduces the total overhead all the way to the optimum of log(n).

See also: Andrew Miller's [Lambda Auth](http://amiller.github.io/lambda-auth/), potentially useful as a starting point for a generalized design.

### RLP

The RLP format is overcomplicated. There are three ways to store a value: one byte (only for values in the range `[0x00 ... 0x7f]`), (0x80 plus length) then value (eg. `cow -> \x83cow`), and (0xb7 plus length of length) then length then value for values longer than 55 bytes. The latter two encodings also exist for lists, but with starting prefixes 0xc0 and 0xf7. This makes deserialization inside the EVM very tricky, and generally adds higher than needed consensus complexity.

One alternative is SimpleSerialize prototyped in python [here](https://github.com/ethereum/research/tree/master/py_ssz), which uses a simple "three bytes representing length, then value" mechanism. This increases byte count slightly (though the losses from this can be heavily mitigated with wire-protocol-level compression), but it adds substantial simplicity gains.

### Parallelization

Because every transaction can theoretically read data that was modified by a previous transaction, guaranteed parallelization is impossible. One can do _optimistic parallelization_ via an algorithm like "fetch the next N transactions, execute them all in parallel, keep track of what they read and wrote, and discard everything including and after the first transaction that reads something a previous transaction wrote", but even though that improves the average case it does not improve the worst case, and protocol limits need to be set based on worst case efficiency to ensure safety.

This can be improved by allowing transactions to statically declare what portions of the state are off-limits to them, enforcing this in protocol, and providing gas incentives for doing this.

See:

* EIP 648: https://github.com/ethereum/EIPs/issues/648
* Sharding account redesign EIP: https://github.com/ethereum/sharding/blob/master/account_redesign_eip.md

### Pre-fetchability and Stateless Client Support

If we adopt a strong version of static declaration, where each transaction must specify a very small portion of the state that it is allowed to access, then we can get two further benefits. First, we gain **pre-fetchability** - clients that receive a transaction that states "I can read or write to accounts A, B, C, D and E" can pre-load those five accounts, with their Merkle tree branches, into the cache, so that when the transaction gets included into a block it can be processed much more quickly. Second, we gain the option to have **stateless clients** - a client can theoretically hold just the global root hash of the state, and accept transactions that come with Merkle branches for those portions of the state that they edit, and in this way validate all consensus rules but require no disk reads or writes (except for one per block to update the state root) and need to store nothing.

Note that there is an entire spectrum between stateless clients and fully stateful clients. For example, many sharding designs require clients to switch to a new shard every N-hour period. One can create a network where clients are expected to keep track of any Merkle tree nodes _that have already been accessed_ during some period, and transactions are required to contain Merkle tree nodes that they need to access that have not yet been accessed during that period.

For this to work, the amount of space that a transaction needs to be able to access needs to be very small - O(1)-sized. This means that a _range_ of accounts from 0x...0000 to 0x...ffff cannot work; it must be a _list_ of accounts. Also, there must be a cap on the amount of data in an account, so storage tries as they currently exist are out of the question.

### Gas weaknesses

* The SSTORE and SLOAD opcodes cost the same amount regardless of the size of the storage tree. However, there are large differences between the cost of accessing data from a 5-item storage tree and a 5 million-item storage tree.
* Filling storage in general is too cheap (see "rent")
* The gas cost of calling another contract is independent of the size of the contract code. This means the calling is too expensive if the target contract code is small, and too cheap if it is large.
* There are no discounts for calling contracts that are frequently called, even though such contracts are more likely to be part of the cache.

### Transaction fee payments

* It would be nice if it were possible for a transaction to be sent with a fee that ramps up automatically up to some limie, eg. if the transaction was sent during block 4202030, then the gasprice might be `min((5 * 10**8) * 1.25 ** (block.number - 4202030), 10**11)`, ramping up exponentially from a min of 0.5 gwei to a max of 100 gwei. This would reduce the load on transaction fee estimation software and generally increase fee efficiency.
* Currently it's very difficult to detect in-protocol what a reasonable gas price for a transaction would be, and mechanisms for doing this can be easily gamed especially by miners. Can we improve on this?

### Rent

There is a mismatch between storage filling gas cost and the externality the storage imposes on the network: you create an account or a new SSTORE key once, but everyone must bear the load forever. One possible fix is to require accounts to pay some rent per unit time.

### Events

There should ideally be some mechanism by which one can create events that trigger automatically at certain times in the future, without any overlay protocols (as one simple application of this, consider a dice game that depends on future block data as a source of randomness). For this to work effectively, one must introduce an "event tree" into the Ethereum state alongside the state tree, and add specialized opcodes for creating events (events can be seen as one-way calls that get "frozen" and then executed in the future). A mechanism for gas costs for events, particularly recurring events, should be determined.

See "what are guaranteed cross-shard calls" for some discussion in the context of sharding here: https://github.com/ethereum/wiki/wiki/Sharding-FAQ

### Verification

Currently, secp256k1 ECDSA + SHA3 exists as a privileged signature verification algorithm in Ethereum. Ideally, since we are a generalized platform, it would be nice to be able to support any signature verification algorithm. A proposed design for this is:

1. When a transaction is sent, it does not require a sender, only a "destination" address. Gas price, start gas, value, and data are still required.
2. The top-level message execution by default has a limit of 25000 gas, and within that time must either exit with an error, or run an `ACCEPT` opcode, paying for the full amount of gas from that contract's account. If the message exits with an error before ACCEPTing, the transaction is invalid. If it ACCEPTs, then it has the full amount of gas to run any other computation, send sub-messages, etc. The intent is for the first 25000 gas to be spent verifying a signature placed inside of the transaction data, and exiting with an error if the signature is invalid.

Ideally, the initial limit would be large enough to support many kinds of signatures, but still small enough to be DDoS-proof. An alternative would be to require PoW on transactions, and scale the PoW difficulty with a verification gas limit set in the transaction; ASIC-resistant PoW can be used for this, though it would need to be carefully designed to be more CPU-friendly rather than GPU-friendly as is the case for Ethash.

See also EIP 86: http://github.com/ethereum/EIPs/issues/86

### Compression

Currently, wire and database compression is done with a fairly crude algorithm that run-length-encodes zeroes but otherwise leaves data unchanged. Substantial gains can probably be made by applying either a pre-generated Huffman code or some separate compression algorithm such as [http://lloyd.github.io/easylzma/](http://lloyd.github.io/easylzma/).

### Virtual machine

The Ethereum virtual machine has a number of suboptimalities at present, and so there are plenty of features that can be added or impoved. Particular possibilities include:

1. The addition of opcodes specialized for 64-bit arithmetic, which can be done on machines much more quickly.
2. The addition of `MCOPY` as an opcode and not a contract.
3. Replacement of DUP1...DUP16 and SWAP1...SWAP16 with `DUP <n>` (where `<n>` is stored similarly to pushdata). This allows for unlimited depth in stack variables.
4. The addition of a `DEPTH` opcode to determine the current call stack depth, useful or determining whether or not there is enough stack space to make a particular sub-call with a compile-time-known depth.
5. A more radical rearchitecture where the VM deals with 64-bit values only, and long-arithmetic is done directly over memory slices. This removes the 256-bit limit and allows easy crypto calculations at arbitrary sizes.

See:

* EVM 1.5: https://github.com/ethereum/cpp-ethereum/issues/3404
* E-WASM: https://github.com/ewasm

### Sending funds

Currently, there is no way to send _all_ of one's funds to a contract from an externally owned account, unless one can exactly estimate the amount of gas that will be consumed. This adds some inconvenience to the process of emptying accounts. A possible solution is a "keep the change" opcode, by which a contract can (i) absorb all remaining gas in a message, (ii) claim `gas * gasprice` as ether for itself, and (iii) have that gas NOT count toward the miner's revenue or the block's gas limit.