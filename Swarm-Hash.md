---
name: Swarm Hash
category: 
---

# Introduction

Swarm Hash (a.k.a. [`bzzhash`](https://github.com/ethersphere/go-ethereum/tree/bzz/bzz/bzzhash)) is a Merkle tree hash designed for the purpose of efficient storage and retrieval in content-addressed storage, both local and networked. While it is used in [Swarm](https://github.com/ethereum/go-ethereum/wiki/Swarm---distributed-preimage-archive), there is nothing Swarm-specific in it and the authors recommend it as a drop-in substitute of sequential-iterative hash functions (like SHA3) whenever one is used for referencing integrity-sensitive content, as it constitutes an improvement in terms of performance and usability without compromising security. For instance in the [URL Hint Protocol](https://github.com/ethereum/wiki/wiki/URL-Hint-Protocol).

In particular, it can take advantage of parallelisms (including SMP and massively-parallel architectures such as GPU's) for faster calculation and verification, can be used to verify the integrity of partial content without having to transmit all of it. Proofs of security to the underlying hash function carry over to Swarm Hash.

# Description

Swarm Hash is constructed using a regular hash function (in our case, SHA3) with a generalization of Merkle's tree hash scheme. The basic unit of hashing is a _chunk_, that can be either a _leaf chunk_ containing a section of the content to be hashed or an _inner chunk_ containing hashes of its children, which can be of either variety.

Hashes of leaf chunks are defined as the hashes of the concatenation of the 64-bit length (in LSB-first order) of the content and the content itself. Because of the inclusion of the length, it is resistant to length extension attacks, even if the underlying hash function is not. Note that this "safety belt" measure is extensively used in the latest edition of OpenPGP standard. It is, however, important to emphasize that Swarm Hash is obviously vulnerable to length extension attacks, but can be easily protected against them, when necessary, using similar measures in a higher layer. A possibly very profitable performance optimization (not currently implemented) is to initialize the hash calculation with the length of the standard chunk size (e.g. 4096 bytes), thus saving the repeated hashing thereof.

Hashes of inner chunks are defined as the hashes of the concatenation of the 64-bit length (in LSB-first order) of the content hashed by the entire (sub-) tree rooted on this chunk and the hashes of its children.

To distinguish between the two, one should compare the length of the chunk to the 64-bit number with which every chunk begins. If the chunk is exactly 8 bytes longer than this number, it is a leaf chunk. If it is shorter than that, it is an inner chunk. Otherwise, it is not a valid Swarm Hash chunk.

# Strict interpretation

A strict Swarm Hash is one where every chunk with the possible exception of those on the rightmost branch is of a specified length, i.e. 4 kilobytes. Those on the rightmost branch are no longer, but possibly shorter than this length. The hash tree must be balanced, meaning that all root-to-leaf branches are of the same length.

The strict interpretation is unique in that only one hash value matches a particular content. The strict interpretation is only vulnerable to length extension attacks if the length of the content is a multiple of the chunk size, and the number of leaf chunks is an integer power of branching size (the fix maximum chunk size divided by hash length).

A [parallelized implementation in Go](https://github.com/ethersphere/go-ethereum/tree/bzz/bzz) is available as well as [a command-line tool](https://github.com/ethersphere/go-ethereum/tree/bzz/bzz/bzzhash) for hashing files on the local filesystem using the strict interpretation.

# Loose interpretations

Swarm Hash interpreted less strictly may allow for different tree structures, imposing fewer restrictions or none at all. In this way, different hash values can resolve to the same content, which might have some adverse security implications.

However, it might open the door for different applications where this does not constitute a vulnerability. For example, accepting single-leaf hashes in addition to strict Swarm hashes allows for referencing files without having to implement the whole thing.

# References

- [Merkle tree](http://en.wikipedia.org/wiki/Merkle_tree)
- [Length extension attack on wikipedia](http://en.wikipedia.org/wiki/Length_extension_attack)
- [IETF RFC4880](https://tools.ietf.org/html/rfc4880)
- [bzzhash code](https://github.com/ethersphere/go-ethereum/tree/bzz/bzz/bzzhash)
- [Swarm documentation and draft specs](https://github.com/ethereum/go-ethereum/wiki/Swarm---distributed-preimage-archive)