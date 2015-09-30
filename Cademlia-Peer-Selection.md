---
name: Cademlia Peer Selection
category: 
---

# Peer addresses

Nodes in the P2P network are identified by 256-bit cryptographic hashes of the nodes' public keys.

The distance between two addresses is the MSB first numerical value of their XOR.

# Peer table format

The peer table consists of rows, initially only one, at most 255 (typically much less). Each row contains at most _k_ peers (data structures containing information about said peer such as their peer address, network address, a timestamp, signature by the peer and possibly various other meta-data), where _k_ is a parameter (not necessarily global) with typical values betwen 5 and 20.

This parameter, _k_, determines the redundancy of the network: the peer selection algorithm described in this page aims to maintain exactly _k_ peers in each row. If several different protocols requiring routing are used on the network, each protocol _p_ can specify its own redundancy requirement _k_<sub>_p_</sub>, in which case the corresponding _k_<sub>_p_</sub> number of peers supporting that protocol are maintained. Participants must specify what protocols they support.

Row numbering starts with 0. Each row number _i_ contains peers whose address matches the first _i_ bits of this node's address. The _i_+1st bit of the address must differ from this nodes address in all rows except the last one. 

As a matter of implementation, it might be worth internally representing all 255 rows from the outset (requiring that the _i_+1st bit be different from our node in all rows); but then considering all of the rows at the end as if they were one row. That is, we look at non empty rows at the end and treat the elements in them as if they belonged to row _i_ where _i_ is the lowest index such that the total number of all elements in row _i_ and in all higher rows, together is at most _k_.

Note: there is a difference here to the original Kedemlia paper http://pdos.csail.mit.edu/~petar/papers/maymounkov-kademlia-lncs.pdf The rows with a high _i_ for us here are the rows with a low _i_ in the paper. For us, high _i_ means high number of bits agreeing, for them high _i_ mean high xor distance. 

# Adding a new peer

A peer is added to the row to which it belongs according to the length of address prefix in common with this node. If that would increase the length of the row in question beyond _k_, the **worst** peer (according to some, not necessarily global, peer quality metric) is dropped from the row, except if it is the last row, in which case a new row _i_ + 1 is added and the elements in the old last row are split between the two rows according to the bit at the corresponding position. Note: If we followed the implementation suggestion in the previous section, then we do not need to make an exception for the last row, nor do we have to worry about splitting the last row on the implementation level.

One sufficient condition for adding a peer is a signed "_add_me_" message containing the sender's peer address and network address, the addressee's peer address and a recent timestamp.

# Lookup

A lookup request contains a peer address (which might or might not be valid, it does not matter). The response is the peer list from the full row corresponding to the requested address (information about at most _k_ peers, or _k_<sub>_p_</sub> peers for the protocol _p_ in use).

For brevity, it might be worth treating _add_me_ requests for nodes that are not already in the peer table as self-lookups and respond accordingly.

The new peers are added to the peer table after which the ones in the row corresponding to the address being searched are sent the lookup request recursively, until no peers are returned that are closer to the searched address.

# Joining the network

Requires only one bootstrap peer, to which the new node sends an _add_me_ message and subsequently adds every node from the response to its own peer table.

Thereafter, it performs a lookup of a synthetic random address from the address range corresponding to rows with indices that are smaller than the row in which the bootstrap node ended up.

# Backwards compatibility mode and DoS safety

Nodes can still safely dump their full peer table and accept connections from naive nodes. Overwriting the entire peer table of a node requires significant computational effort even with relatively low _k_. DoS attacks against non-naive nodes (as described in this page) require generating addresses with corresponding key pairs for each row, requiring quite a bit of hashing power.