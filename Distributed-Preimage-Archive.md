---
name: Distributed Preimage Archive
category: 
---

# Purpose

DPA stores small pieces of information (preimage objects, arbitrary strings of bytes of limited length) retrievable by their (cryptographic) hash value. Thus, preimage objects stored in DPA have implicit integrity protection. The hash function used for key assignment is assumed to be collision-free, meaning that colliding keys for different preimage objects are assumed to be practically impossible.

DPA serves as a fast, redundant store optimized for speedy retrieval and long-term reliability. Its most frequent use within Ethereum is to cache objects that can be retrieved and/or re-constructed by other means at significant cost. Since the key is derived from the preimage, there is no sense in which we can talk about multiple or alternative values for keys, the store is immutable.

# High-level design

DPA is organized as a DHT (Distributed Hash Table): each participating node has an address (resolved into a network address by the p2p layer) coming from the same value set as the range of the hash function. In particular it is the hash of the public key (NodeID field of the DEVP2P handshake).

There is a distance measure defined over this value set that is a proper metric satisfying the triangle inequality. It is always possible to tell how far another node or another preimage object is from a given address or hash value. The distance from self is zero.

Each node is interested in being able to find preimages to hash values as fast as possible and therefore stores as many preimages as it can itself. Whenever it must decide which preimage object to store and which to discard, preference is given to objects with an address that is closer. In practical terms, it is always the farthest preimage object that is discarded when a node runs short of storage capacity. Thus, each node ends up storing preimage objects within a given radius limited by available storage capacity. The cryptographic hash function takes care of randomization and fair load balancing.

Nodes provide the following services through a public network API:

1. Inserting new preimages into DPA
1. Retrieving preimages from their own storage, if they have it.
1. Sharing routing information to a given node address

Locally, in addition to the above, nodes also provide the service of storing and retrieving large chunks of data. When storing, the data is disassembled into a tree of blocks according to a scheme resulting in the key corresponding to the root block. Given the key of the root block the data can then can be reassembled with the help of recursively retrieving the blocks in the tree. 

## Queries

### Insert

When receiving a preimage that is not already present in its local storage, the node stores it locally. If the storage allocated by the node for the archive is full, the object accessed the longest time ago is discarded. Note that this policy implicitly results in storing the objects closer to the node's address, as - all else being equal - those are the ones which are most likely to get queried from this particular node, due to the lookup strategy detailed below.

After storing the preimage, the insert request is also forwarded to all the nodes in the corresponding row of the routing table. Note that the kademlia routing makes sure that the row in the close proximity of a node actually contains nodes further out than self thereby taking care of storage redundancy.
However, in order to mitigate against node drop out preimages, especially those with hashes in the node's proximity, need to be re-broadcast, albeit with a very low frequency.

When a node received a store request, it remembers it for a while and does not forward the same request. This is needed to avoid redundant network traffic. 

### Retrieve

Retrieval requests have the following parameters:

1. The hash of the queried pre-image
1. Timeout value for routed retrieval, zero if routing is not required

If the node has the pre-image, it is returned, called a delivery. Otherwise, the following happens:

1. The entire row in the Kademlia table corresponding to the queried hash is returned.
1. If routing is deemed not worth the effort (timeout is too short), this fact is also communicated.
1. Otherwise, the same query is recursively done and if it succeeds within the specified timeout, the result is sent to the querying node.

Successfully found pre-images are automatically re-inserted into DPA.

A default time estimate for retrieval is calculated in proportion to the expected hop-distance from the node closest to the queried preimage. If this time is outside of the timeout parameter in the request, the request is not routed.

Each node in the row corresponding to the queried preimage is sequentially queried in order of increasing distance from the target hash. The query is forwarded with a timeout value set to the maximum of the above estimate and the total timeout divided by the number of nodes in the row. If the preimage is found or the time elapsed is in excess of the received timeout value, processing of the query is aborted with timeout. 

From the set up of the first forward onwards, all retrieval requests of the same target are remembered in a request pool. If the data results in successful retrieval and the preimage is stored, the requests are answered by returning the preimage object to each requesting node whether forwarding or originator. The pool of requesting nodes then can be forgotten, since all further queries can be responded with the object delivery.

## BZZ protocol spec

**Handshake**

`[0x01, protocolVersion: B_32, strategy: B_32, capacity: B_64, peers: B_8]`

**Storing**

`[+0x02, key: B_256, metadata: [], data: B_4k]`: the data chunk to be stored, preceded by its key. 

**Retrieving**

`[0x03, key: B_256, timeout: B_64, metadata: []]`: key of the data chunk to be retrieved, timeout in milliseconds. Note that zero timeout retrievals serve also as messages to retrieve peers.

**Peers**

`[0x04, key: B_256, timeout: B_64, peers: [[peer], [peer], .... ]]` the encoding of a peer is identical to that in the devp2p base protocol peers messages: `[IP, Port, NodeID]` note that a node's DPA address is not the NodeID but the hash of the NodeID. Timeout serves to indicate whether the responder is forwarding the query within the timeout or not.

**Delivery**

`[0x05, key: B_256, metadata: [], data: B_4k]`: the delivery response to retrieval queries. 

## Routing

It is based on Kademlia's routing. [details](https://github.com/ethereum/wiki/wiki/Cademlia-Peer-Selection)

_to be continued..._