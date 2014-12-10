# Peer addresses

Nodes in the P2P network are identified by 256-bit cryptographic hashes of the nodes' public keys.

The distance between two addresses is the MSB first numerical value of their XOR.

# Peer table format

The peer table consists of rows, initially only one, at most 255. Each row contains at most _k_ peeers (data structures containing information about said peer such as their peer address, network address and various other meta-data), where _k_ is a parameter (not necessarily global) with typical values betwen 5 and 20.

Row numbering starts with 0. Each row number _i_ contains peers whose address matches the first _i_ bits of this node's address. With the exception of the last row, the address bit following the first _i_ bits must be different.

As a matter of implementation, it might be worth internally representing all 255 rows from the outset, but considering the (at most _k_) elements from the last non-empty rows as belonging to the same row. In particular to the one with the lowest index such that the number of all elements in that row and those with a higher _i_ value number at most _k_.

# Adding a new peer

A peer is added to the row to which it belongs according to the length of address prefix in common with this node. If that would increase the length of the row in question beyond _k_, the **worst** peer (according to some, not necessarily global, peer quality metric) is dropped from the row, except if it is the last row, in which case a new row _i_ + 1 is added and the elements in the old last row are split between the two rows according to the bit at the corresponding position. Note that the implementation suggestion in the previous section makes this distinction unnecessary on the implementation level.

One condition for adding a peer is a signed "_add_me_" message containing the sender's peer address and network address, the addressee's peer address and a recent timestamp.