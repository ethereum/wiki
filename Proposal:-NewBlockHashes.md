# Problem

Block propagation is slow. This is partly due to the problematic strategy decision of either sending out a new block to all peers (costly in terms of bandwidth, especially when considered at the network level where most nodes will receive a block from most of their peers) or send a new block to a randomly selected subset of peers (making worst-case propagation times substantial).

# Solution

Much better would be a low-bandwidth full propagation of the hash. All nodes would receive a hash for each new block from most of their peers (peers who have demonstrated knowledge of the hash would not be notified). However, the block itself would need only be transferred once per node.

# Specification

Introduce a new packet: `NewBlockHashesPacket`, into slot `+1` (original taken by `GetTransactions`, which is not defunct).


