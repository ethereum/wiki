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

Irrespectively whether or not the new preimage was stored locally, it is forwarded to at least two good nodes in the routing table that are closest to its hash value, if they are (strictly) closer than the forwarding node itself.

### Retrieve

Retrieval requests have the following parameters:

1. The hash of the queried pre-image
1. Timeout value for routed retrieval, zero if not required

If the node has the pre-image, it is returned. Otherwise, the following happens:

1. The entire row in the Kademlia table corresponding to the queried hash is returned.
1. If routing is deemed not worth the effort (timeout is too short), this fact is also communicated.
1. Otherwise, the same query is recursively done and if it succeeds within the specified timeout, the result is sent to the querying node.

Successfully found pre-images are automatically re-inserted into DPA.

## Routing

It is based on Kademlia's routing. [details](https://github.com/ethereum/wiki/wiki/Cademlia-Peer-Selection)

_to be continued..._