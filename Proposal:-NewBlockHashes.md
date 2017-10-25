---
name: NewBlockHashes Proposal
category: 
---

# Problem

Block propagation is slow. This is partly due to the problematic strategy decision of either sending out a new block to all peers (costly in terms of bandwidth, especially when considered at the network level where most nodes will receive a block from most of their peers) or send a new block to a randomly selected subset of peers (making worst-case propagation times substantial).

# Solution

Much better would be a low-bandwidth full propagation of the hash. All nodes would receive a hash for each new block from most of their peers (peers who have demonstrated knowledge of the hash would not be notified). However, the block itself would need only be transferred once per node.

# Specification

Introduce a new packet: `NewBlockHashes`, into the slot `+0x01` (original taken by `GetTransactions`, which is not defunct).

**NewBlockHashes**
[`+0x01`: `P`, `hash1`: `B_32`, `hash2`: `B_32`, `...`] Specify one or more new blocks which have appeared on the network. Including hashes that the sending peer could reasonable be considered to know that the receiving node is aware of is considered Bad Form, and may reduce the reputation of the sending node. Including hashes that the sending node later refuses to honour with a proceeding `GetBlocks` message is considered Bad Form, and may reduce the reputation of the sending node.


