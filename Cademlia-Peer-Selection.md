# Peer addresses

Nodes in the P2P network are identified by 256-bit cryptographic hashes of the nodes' public keys.

The distance between two addresses is the MSB first numerical value of their XOR.

# Peer table format

The peer table consists of rows, initially only one, at most 255 (typically much less). Each row contains at most _k_ peeers (data structures containing information about said peer such as their peer address, network address, a timestamp, signature by the peer and possibly various other meta-data), where _k_ is a parameter (not necessarily global) with typical values betwen 5 and 20.

This parameter, _k_, determines the redundancy of the network: the peer selection algorithm described in this page aims to maintain exactly _k_ peers in each row. If several different protocols requiring routing are used on the network, each protocol _p_ can specify its own redundancy requirement _kp_, in which case the corresponding _kp_ number of peers supporting that protocol are maintained. Participants must specify what protocols they support.

Row numbering starts with 0. Each row number _i_ contains peers whose address matches the first _i_ bits of this node's address. With the exception of the last row, the address bit following the first _i_ bits must be different.

As a matter of implementation, it might be worth internally representing all 255 rows from the outset, but considering the (at most _k_) elements from the last non-empty rows as belonging to the same row. In particular to the one with the lowest index such that the number of all elements in that row and those with a higher _i_ value number at most _k_.

# Adding a new peer

A peer is added to the row to which it belongs according to the length of address prefix in common with this node. If that would increase the length of the row in question beyond _k_, the **worst** peer (according to some, not necessarily global, peer quality metric) is dropped from the row, except if it is the last row, in which case a new row _i_ + 1 is added and the elements in the old last row are split between the two rows according to the bit at the corresponding position. Note that the implementation suggestion in the previous section makes this distinction unnecessary on the implementation level.

One sufficient condition for adding a peer is a signed "_add_me_" message containing the sender's peer address and network address, the addressee's peer address and a recent timestamp.

# Lookup

A lookup request contains a peer address (which might or might not be valid, it does not matter). The response is the peer list from the full row corresponding to the requested address (information about at most _k_ peers, or _kp_ peers for the protocol _p_ in use).

For brevity, it might be worth treating _add_me_ requests for nodes that are not already in the peer table as self-lookups and respond accordingly.

The new peers are added to the peer table after which the ones in the row corresponding to the address being searched are sent the lookup request recursively, until no peers are returned that are closer to the searched address.

# Joining the network

Requires only one bootstrap peer, to which the new node sends an _add_me_ message and subsequently adds every node from the response to its own peer table.

Thereafter, it performs a lookup of a synthetic random address from the address range corresponding to rows with indices that are smaller than the row in which the bootstrap node ended up.

# Backwards compatibility mode and DoS safety

Nodes can still safely dump their full peer table and accept connections from naive nodes. Overwriting the entire peer table of a node requires significant computational effort even with relatively low _k_. DoS attacks against non-naive nodes (as described in this page) require generating addresses with corresponding key pairs for each row, requiring quite a bit of hashing power.