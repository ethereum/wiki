The purpose of the light client protocol is to allow users in low-capacity environments (embedded smart property environments, smartphones, browser extensions, some desktops, etc) to maintain a high-security assurance about the current state of some particular part of the Ethereum state or verify the execution of a transaction. Although full security is only possible for a full node, the light client protocol allows light nodes processing about 1KB of data per 2 minutes to receive data from the network about the parts of the state that are of concern to them, and be sure that the data is correct  provided that the majority of miners are correctly following the protocol, and perhaps even only provided that at least one honest verifying full node exists.

### Background: Patricia Merkle Trees

All substantial quantities of data in Ethereum are stored in a data structure known as the [Patricia Merkle tree](https://github.com/ethereum/wiki/wiki/%5BEnglish%5D-Patricia-Tree), a tree structure where each node in the tree is the hash of its children. Each set of key/value pairs maps to a unique root hash, and only a small subset of nodes is needed to prove that a particular key/value combination is in the tree corresponding to a particular root hash.

![](http://vitalik.ca/files/spv.png)

The size complexity of a Merkle proof scales linearly with the height of a tree; because each child in a tree has a particular number of children (in our case, up to 17), this means that the size complexity of a Merkle proof is logarithmic in the quantity of data stored. This means that, even if the entire state tree is a few gigabytes in size, if a node receives a state root from a trusted source that node has the ability to know with full certainty the validity of any information with the tree by only downloading a few kilobytes of data in a proof.

An SPV proof in a Patricia tree simply consists of the complete subset of tree nodes that were processed in order to access it (or, more specifically, the tree nodes that needed to be looked up in a reverse-hash-lookup database). In a simple implementation of a Patricia tree, retrieving the value associated with a particular key requires descending the hash tree, constantly looking up nodes in the database by their hashes, until you eventually reach the final leaf node; a simple algorithm for producing an SPV proof is to simply run this naive algorithm, and record all of the database lookups that were made. SPV verification consists of running the naive lookup algorithm but pointing it to a custom database populated only with the nodes in the SPV proof; if there is a "value not found" error, then the proof is invalid. 

#### Specifications

Request for proof of account data (all data structures in RLP):

    [ 30, address ]

Request for proof of account state:

    [ 31, address, index ]

Request for proof of transaction:

    [ 32, blknum, index ]

    [ 33, txhash ]

To all of these requests, a parameter `blk_coarseness` can be appended (eg. `[31, address, index, blk_coarseness]`); this requests a proof including block headers up to the next block N such that `N mod blk_coarseness = 0`

A simple proof:

    [ 40, [ node1, node2, node3 ... ]]

A proof with block headers:

    [ 40, [node1, node2, node3 ... ], [header1, header2 ... ]]

The purpose of the `blk_coarseness` parameter is to allow for "extra-light" nodes, which process all block headers but store only a few (eg. one block header per 16). Using exact powers of two for `blk_coarseness` is encouraged.

### Miscellaneous Block Invalidity

A proof that a block's transaction tree contains an 

### Transaction Execution Challenge-Response

Another function of the light client protocol is to verify the validity 