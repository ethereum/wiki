# Purpose

DPA stores small pieces of information (preimage objects, arbitrary strings of bytes of limited length) retrievable by their (cryptographic) hash value. Thus, preimage objects stored in DPA have implicit integrity protection. The hash function used for key assignment is assumed to be collision-free, meaning that colliding keys for different preimage objects are assumed to be practically impossible.

DPA serves as a fast, redundant store optimized for speedy retrieval and long-term reliability. Its most frequent use within Ethereum is to cache objects that can be retrieved and/or re-constructed by other means at significant cost.

# High-level design

DPA is organized as a DHT: each participating node has an address (resolved into a network address by the p2p layer) coming from the same value set as the range of the hash function.

There is a distance measure defined over this value set that is a proper metric satisfying the triangle inequality. It is always possible to tell how far another node or another preimage object is from a given address or hash value. The distance from self is zero.

Each node is interested in being able to find preimages to hash values as fast as possible and therefore stores as many preimages as it can itself. Whenever it must decide which preimage object to store and which to discard, preference is given to objects with an address that is closer. In practical terms, it is always the farthest preimage object that is discarded when a node runs short of storage capacity. Thus, each node ends up storing preimage objects within a given radius limited by available storage capacity. The cryptographic hash function takes care of randomization and fair load balancing.

Nodes provide the following services through a public network API:

1. Inserting new preimages into DPA
1. Retrieving preimages from their own storage, if they have it.
1. Sharing routing information to a given node address
1. Responding to pings

Locally, in addition to the above, nodes also provide the service of retrieving preimages from the entire DPA.

## Queries

### Insert

When receiving a preimage that is not already present in its local storage, the node stores it locally, unless its storage is full AND the preimage is farther than the most distant preimage object in its storage. If it is full, but there are stored preimage objects that are farther away than the received one, the farthest stored object is discarded before a repeated attempt at inserting the object.

Irrespectively whether or not the new preimage was stored locally, it is forwarded two good nodes in the routing table that are closest to its hash value, if they are (strictly) closer than the forwarding node itself.

### Retrieve

This section describes the local query. The networked query is a simple local retrieval with no further action.

If a node does not have the preimage of a queried hash value, it queries the good nodes it recursively searches for the node with the closest address, querying each node along the way. If even the closest node does not have the preimage, it is assumed not to be present in DPA. Whenever a node is found that has the preimage (after hashing and verification), the search is aborted and the preimage is returned.

Successfully found pre-images are automatically re-inserted into DPA.

## Routing

_to be continued..._