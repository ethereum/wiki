---
name: Extend GetBlockHashes With Target Hash Proposal
category: 
---

# Problem

The current protocol specification does not provide the means to mitigate an infinite chain feeding attack. A malicious peer can make up arbitrary blocks on the fly, and feed their hashes indefinitely to a target, without ever linking to the existing chain. This will result in an eventual memory exhaustion and crash. The root cause is that the protocol provides no means to validate any blocks, without first pulling the entire hash chain.

# Solution

Peers should be able to request hashes not only backwards towards the genesis block, but forward too to some specific target hash. This would allow the downloader to start pulling actual blocks and verify them concurrently with fetching the hashes. If no peers (including the hash provider) has the blocks to back up the hashes, the chain is rejected.

This forward synchronization strategy requires the capacity to locate a common ancestor block to our blockchain and that of a remote peer in order to know the joining point from which to request the hashes. This can be facilitated with the same mechanism by doing a binary search on our blockchain, requesting 1xhashes from the remote (towards its advertised head) side to see if it knows about it or not, honing in on the common ancestor.

# Specification

1.  Extend `GetBlockHashes` packet with an additional field:

    **GetBlockHashes** [`+0x03`: `P`, `srcHash`: `B_32`, `targetHash`: `B_32`, `maxBlocks`: `P`] 
       * `targetHash`: The endpoint in a remote blockchain towards which to retrieve hashes

2.  Modify `BlockHashes`'s returned hash order:

    * Adhere to that requested by `GetBlockHashes`, opposed to the current "hard coded" young -> old ordering

The benefit of this proposal is that beside providing the means to do forward syncing, it retains the capacity to implement the currently specified reverse fetch behavior by passing the genesis block's hash to `targetHash`. This way Ethereum implementations don't have to immediately devise a new downloader strategy to cope with the update, but can function as they are until ready to evolve.

# Supersedes

[[Proposal: BlockHashesFromNumbers]]

The main issue the `BlockHashesFromNumbers` set out to resolve was the slow download of hashes (which was stalling the synchronization), by introducing parallel fetches based on height/block-number based hash retrievals. It also inherently provided a mechanism to limit the number of potentially invalid hashes downloaded to that of the current chain's height.

The current proposal however approaches the challenge from the opposite direction. By downloading hashes towards the end of a chain - after the first hash delivery - blocks can be immediately retrieved in parallel (compared to which hash downloads are insignificant in size and hence latency), so there is no need for the added complexity and potential issues with parallelizing hash downloads. Additionally, by limiting the number of pending hashes, we can also prevent any infinite chain attacks.

Given that the current proposal addresses all of the issues `BlockHashesFromNumbers` aimed to fix, while being much less invasive (requires the addition of single packet field); is gracefully compatible with previous protocol implementations (they can continue to function without an algorithm update); and implementation wise is straightforward and easy to reason about; we believe it is a superior proposal.

### Critique

- Closer to the present protocol than `BlockHashesFromNumbers`.
- Less general and expansive than `BlockHashesFromNumbers`: it is designed for a very particular strategy and is less likely to be adaptable to future, unknown strategies.
- Does not support parallel hash-chain downloading alongside forward hash-chain downloading (`BlockHashesFromNumbers` supports both).
- Somewhat more complex in terms of semantics:
  - The notion of a series of hash going from a source hash to a destination hash is found nowhere else in the protocol or codebase. Exchanging number to/from hash is already found in the API.
  - Not immediately clear with edge cases (e.g. if from is genesis and to is leaf, or if from doesn't exist in the chain but leaf does, or vice-versa). `BlockHashesFromNumbers` is well-defined under all circumstances.
- Somewhat more complex in terms of implementation; how to efficiently determine the fork point or common ancestor? `BlockHashesFromNumbers` would make it trivial with a binary chop.
