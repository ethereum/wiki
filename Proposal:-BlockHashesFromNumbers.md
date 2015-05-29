# Problem

During initial synchronization to a large blockchain downloading block hashes can take significant time. Current protocol does not allow parallel hashes downloading. Hashes can be requested sequentially up the chain, making it impossible to start blocks downloading until all of the hashes up to the known hash or genesis are received. A malicious peer can feed an infinite hash chain. Currently it is not straightforward to protect against such an attack as there is no additional information on the number of hashes. The only strategy is to esitimate a reasonable number of hashes using current date, genesis date and average block time, and then reject the peer after downloading that many hashes.

# Solution

Peers should exchange their latest block number in the Status packet. Also, there should be a way to request a list of hashes by the block number. This will allow parallel downloading, starting block downloading and validation immediately after receiving first hashes, rejecting malicious peers right away. 

# Specification

1.  Extend `Status` packet with an additional field:

    **Status** [`+0x00`: `P`, `protocolVersion`: `P`, `networkId`: `P`, `td`: `P`, `bestHash`: `B_32`, `genesisHash`: `B_32`, `number`: `P`] 
       * `number`: Number of the last block

2.  Introduce a new packet: `BlockHashesFromNumber`, into the slot `+0x08` (new).

**BlockHashesFromNumber**
[`+0x08`: `P`, `number`: `P`, `maxBlocks`: `P`]
Requests a BlockHashes message detailing a number of the first block hash and a total of hashes to be sent. Returned hash list must be ordered by block number in ascending order.
