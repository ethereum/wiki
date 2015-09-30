---
name: Parallel Block Downloads
category: 
---

New network protocol & strategy.

### Goals
- Achieve parallel downloads of chain
- Introduce framework that can form basis of Swarm
- Minimise unnecessary block transfers

### Basic Overview

- Two peers connect & say Hello. Hello includes the TD & hash of their best block.
- The client with the worst TD asks peer for full chain of just block hashes.
- Chain of hashes is stored in space shared by all peer connections, and used as a "work pool".
- While there are hashes in the chain of hashes that we don't have in our chain:
  - Ask for N blocks from our peer using the hashes. Mark them as on their way so we don't get them from another peer.

### Required Changes

Network protocol: 33

Additional parameters to Hello:
- `TD`: Total Difficulty of the best chain. Integer.
- `BestHash`: The hash of the best (i.e. highest TD) known block.
- `GenesisHash`: The hash of the Genesis block.

Additional Message types:
- `0x17`: `GetBlockHashes` [ `hash` : `B_32`, `maxBlocks`: `P` ]: Requests a `BlockHashes` message of at most `maxBlocks` entries, of block hashes from the blockchain, starting at the parent of block `hash`. Does not _require_ the peer to give `maxBlocks` hashes - they could give somewhat fewer.
- `0x18`:`BlockHashes` [ `hash_0`: `B_32`, `hash_1`: `B_32`, .... ]: Gives a series of hashes of blocks (each the child of the next). This implies that the blocks are ordered from youngest to oldest.
- `0x19`:`GetBlocks` [ `hash_0`: `B_32`, `hash_1`: `B_32`, .... ]: Requests a `Blocks` message detailing a number of blocks to be sent, each referred to by a hash. Note: Don't expect that the peer necessarily give you all these blocks in a single message - you might have to re-request them.

Remove Message types:
- `GetChain`
- `NotInChain`