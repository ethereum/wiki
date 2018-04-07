---
name: NewBlock Message
category: 
---

### Goals

At present `Blocks` messages may be sent either as a response to a `GetBlocks` message receipt or due to a new block being mined or discovered. This causes some issues for the state transition mechanisms, which are to be avoided.

### Basic Design

Provide a second response type explicitly used for distributing a new block.

### Needed Changes

New packet for the Ethereum sub-protocol, `NewBlock`:

**Blocks**
[`+0x06`, [`blockHeader`, `transactionList`, `uncleList`], `...`] Specify (a) block(s) as an answer to `GetBlocks`. The items in the list (following the message ID) are blocks in the format described in the main Ethereum specification. This may validly contain no blocks if no blocks were able to be returned for the `GetBlocks` query.

**NewBlock**
[`+0x07`, [`blockHeader`, `transactionList`, `uncleList`], `totalDifficulty`] Specify a single block that the peer should know about. The composite item in the list (following the message ID) is a block in the format described in the main Ethereum specification.
- `totalDifficulty` is the total difficulty of the block (aka score).
