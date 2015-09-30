---
name: Serenity Wishlist
category: 
---

This document outlines known flaws and missing features in Ethereum Frontier and Homestead that are non-serious but inconvenient, and which we decided to leave in in order to be able to launch in any reasonable timeframe. We intend to thoroughly investigate solving all of these issues for Serenity (aka. Ethereum 1.1).

### Big Issues

1. Because every user must process every transaction (ie. the per-user required quantity of storage is linear in the number of users or level of usage), Ethereum is currently fundamentally unscalable, as are all other blockchain technologies to date. We believe this to be the single largest problem in blockchain development, and it will be our primary focus for either Serenity or a version soon after.
2. Proof of work is an extremely expensive means of maintaining consensus and produces substantial negative externalities due to environmental issues and to a lesser degree principal-agent issues, and its security guarantees are uncomfortably incomplete (eg. it is vulnerable to [P + epsilon attacks](https://blog.ethereum.org/2015/01/28/p-epsilon-attack/) or even outright bribes of fairly low size). We plan to resolve this via proof of stake, which resolves these issues via a combination of proof of stake and subjective resolution-of-last-resort. A small amount of proof of work may remain as an anti-DDoS measure.

### Trie

In the process of designing Ethereum 1.0 we have done a thorough job of optimizing the Ethereum virtual machine's computational power, but a much less thorough job of optimizing the database. There exists the possibility for substantial space savings, for both full nodes and light clients, by reorganizing Merkle tree structures and accounts. One example model for a more optimized state tree is the following:

1. Assign account numbers sequentially, not via `address = sha3(pubkey) % 2**160`. This means that the set of accounts is contiguous, allowing standard binary Merkle trees to be used in place of Patricia trees, saving substantial space in state, history and LOAD/SSTORE time due to the resulting tight-packing. 
2. Where Patricia trees are required, switch from hex to binary (est. 10-50% savings)
3. Where Patricia trees are required, note that currently a proof stores redundant info by storing a hash of a tree node in one node and then the other node separately. Create a special-purpose compression algorithm, either LZV style or placing nodes into each other directly when they refer to each other, to deduplicate (est. 35% savings).
4. Charge rent for accounts. This entails expanding accounts by adding a `last_accessed` parameter, and then when an account is accessed we update the parameter to the current block timestamp and simultaneously subtract `rent * (block.timestamp - acct.last_accessed)` from the account balance, where `rent` is somehow dynamically adjusted. If the new balance is below zero, then the account is destroyed and a large gas refund is provided. We theorize that large miners and account holders will want to create transactions that destroy accounts that are lagging on rent in order to reduce storage bloat; alternatively we can incentivize it with an additional security deposit.
5. Require transactions to include a timestamp, so that they can only be valid in `[timestamp, timestamp + 3600]`. This prevents replay attacks in the special case that accounts go down to zero balance, get deleted and then regain balance but with a nonce reset to zero.

### More expressive Merkle trees

Certain kinds of mechanisms, to be developed on Ethereum, require a richer set of data structures then just arrays, key/value maps, etc; the most commonly asked-for one is a heap (or an equivalent data structure that supports push/pop/top in logtime), highly useful in front-runner-proof markets among other applications. Currently, implementing a heap in Ethereum will lead to log^3(n) overhead, as we have a tree (the heap) on top of a tree (the account storage Patricia tree) on top of a tree (leveldb). Making a custom DB for the Patricia tree will likely need to be done at some point anyway, and will remove one level of overhead. However, the larger gain will come from the ability to have a Merklized heap-like structure, essentially making the heap operations native to the protocol. This reduces the total overhead all the way to the optimum of log(n).

See also: Andrew Miller's [Lambda Auth](http://amiller.github.io/lambda-auth/), potentially useful as a starting point for a generalized design.

### Events

There should ideally be some mechanism by which one can create events that trigger automatically at certain times in the future, without any overlay protocols (as one simple application of this, consider a dice game that depends on future block data as a source of randomness). For this to work effectively, one must introduce an "event tree" into the Ethereum state alongside the state tree, and add specialized opcodes for creating events (events can be seen as one-way calls that get "frozen" and then executed in the future). A mechanism for gas costs for events, particularly recurring events, should be determined.

### Verification

Currently, secp256k1 ECDSA + SHA3 exists as a privileged signature verification algorithm in Ethereum. Ideally, since we are a generalized platform, it would be nice to be able to support any signature verification algorithm. A proposed design for this is:

1. When a transaction is sent, it does not require a sender, only a "destination" address. Gas price, start gas, value, and data are still required.
2. The top-level message execution by default has a limit of 25000 gas, and within that time must either exit with an error, or run an `ACCEPT` opcode, paying for the full amount of gas from that contract's account. If the message exits with an error before ACCEPTing, the transaction is invalid. If it ACCEPTs, then it has the full amount of gas to run any other computation, send sub-messages, etc. The intent is for the first 25000 gas to be spent verifying a signature placed inside of the transaction data, and exiting with an error if the signature is invalid.

Ideally, the initial limit would be large enough to support many kinds of signatures, but still small enough to be DDoS-proof. An alternative would be to require PoW on transactions, and scale the PoW difficulty with a verification gas limit set in the transaction; ASIC-resistant PoW can be used for this, though it would need to be carefully designed to be more CPU-friendly rather than GPU-friendly as is the case for Ethash.

### Compression

Currently, wire and database compression is done with a fairly crude algorithm that run-length-encodes zeroes but otherwise leaves data unchanged. Substantial gains can probably be made by applying either a pre-generated Huffman code or some separate compression algorithm such as [http://lloyd.github.io/easylzma/](http://lloyd.github.io/easylzma/).

### Virtual machine

The Ethereum virtual machine has a number of suboptimalities at present, and so there are plenty of features that can be added or impoved. Particular possibilities include:

1. The addition of opcodes specialized for 64-bit arithmetic, which can be done on machines much more quickly.
2. The addition of `MCOPY` as an opcode and not a contract.
3. Replacement of DUP1...DUP16 and SWAP1...SWAP16 with `DUP <n>` (where `<n>` is stored similarly to pushdata). This allows for unlimited depth in stack variables.
4. The addition of a `DEPTH` opcode to determine the current call stack depth, useful or determining whether or not there is enough stack space to make a particular sub-call with a compile-time-known depth.
5. A more radical rearchitecture where the VM deals with 64-bit values only, and long-arithmetic is done directly over memory slices. This removes the 256-bit limit and allows easy crypto calculations at arbitrary sizes.

### Sending funds

Currently, there is no way to send _all_ of one's funds to a contract from an externally owned account, unless one can exactly estimate the amount of gas that will be consumed. This adds some inconvenience to the process of emptying accounts. A possible solution is a "keep the change" opcode, by which a contract can (i) absorb all remaining gas in a message, (ii) claim `gas * gasprice` as ether for itself, and (iii) have that gas NOT count toward the miner's revenue or the block's gas limit.

### Currency Neutrality

Allow any currency to be used for purposes of security deposits, paying rent, etc. One possible framework in which this could happen is as follows:

1. Create a base protocol-level concept of "currency". An eligible currency should give the protocol the ability to print more units of it and should support the standard send/receive/allow withdrawal interface.
2. Have (and subsidize liquidity on LMSR-style?) an on-protocol decentralized exchange to determine the price of each token relative to each other token.
3. Anyone can set a security deposit of any size in any currency. The protocol keeps track of a minimum security deposit in every currency (adjusted to target some number of validators), and you cannot add a deposit unless you provide at least this quantity. A deposit ceases to be usable if it falls below 50% of the minimum.

Alternatives include being more liberal with regard to the assets involved, but using some kind of preference-revelation scheme to determine the value of a maximally arbitrary asset (note: this is hard when you do not have a single "favored currency" which you are assuming will have higher liquidity than everything else).

A third option is to have a demurrage-heavy (or otherwise ephemeral) pseudo-currency, similar to gas, which is in some way assigned to validators.