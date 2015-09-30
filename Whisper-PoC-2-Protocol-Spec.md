---
name: Whisper PoC-2 Protocol Spec
category: 
---

This page, in addition to the Whisper Wire Specification, details the full Whisper protocol for the first proof-of-concept and sets the vision for the final design. It evolve alongside the Whisper protocol as the prototype is refined. From it you should be able to develop compliant Whisper implementations. This document is intended to give only the base specification. Many of the aspects leading to an implementation of Whisper are game theoretic and best not prescribed in the specification, but rather left to individual implementation teams to determine for themselves.

### What Whisper Is (and Is Not)

Whisper combines aspects of both DHTs and datagram messaging systems (e.g. UDP). As such it may be likened and compared to both, not dissimilar to the matter/energy duality (apologies to physicists for the blatant abuse of a fundamental and beautiful natural principle).

Whisper is a pure identity-based messaging system. Whisper provides a low-level (non-application-specific) but easily-accessible API without being based upon or prejudiced by the low-level hardware attributes and characteristics, particularly the notion of singular endpoints.

Alternatively, Whisper may be likened to a DHT with a per-entry configurable TTL and conventions for the signing and encryption of values. In this sense, Whisper provides the ability to have multiply-indexable, non-unique entries (i.e. the same entry having multiple keys, some or all of which may be the same as other entries).

As such, Whisper is not a typical communications system. It is not designed to replace or substitute TCP/IP, UDP, HTTP or any other traditional protocols; it is not designed to provide a connection oriented system, nor for simply delivering data betwixt a pair of specific network endpoints; it does not have a primary goal of maximising bandwidth or minimising latency (though as with any transmission system, these are concerns).

Whisper is a new protocol designed expressly for a new paradigm of application development. It is designed from the ground up for easy and efficient multi-casting and broadcasting. Similarly, low-level partially-asynchronous communications is an important goal. Low-value traffic reduction or retardation is another goal (which might also be likened to the quest for QoS). It is designed to be a building block in next generation ÐApps which require large-scale many-to-many data-discovery, signal negotiation and modest transmissions with an absolute minimum of fuss and the expectation that one has a very reasonable assurance of complete privacy.

### Pitch-Black Darkness

Whisper operates around the notion of being user-configurable with regard to how much information it leaks concerning the ÐApp content and ultimately, the user activities. To understand information leakage, it is important to distinguish between mere encryption, and *darkness*. Many protocols, both those designed around p2p and more traditional client/server models provide a level of encryption. For some, encryption forms an intrinsic part of the protocol and, applied alone, delivers its primary requirement. While decentralising and encrypting is a great start on building a legitimately "post-Snowden" Web, it is not the end.

Even with encrypted communications, well-funded attackers are still able to compromise ones privacy, often quite easily. Bulk metadata collection becomes the new battleground, and is at once dismissed as a privacy concern by authorities yet trumpeted as the Next Big Thing by big-data outfits. In the case of a simple client/server model, metadata betrays with which hosts one communicates - this is often plenty enough to compromise privacy given that content is, in many cases, largely determinable from the host.

With decentralised communications systems, e.g. a basic non-routed but encrypted VoIP call or Telehash communication, a network packet-sniffing attacker may not be able to determine the specific content of a transmission, but with the help of ISP IP address logs they would be able to determine to whom one communicated, when and how often. For certain types of applications in various jurisdictions, this is enough to be a concerning lack of privacy.

Even with encryption and packet forwarding through a third relay node, there is still ample room for a determined bulk transmissions-collector to execute statistical attacks on timing and bandwidth, effectively using their knowledge of certain network invariants and the fact that only a finite amount of actors are involved (i.e. that nodes tend to forward messages between the same two peers with minimal latency and that there aren't all that many pairs of nodes that are trying to communicate via the same relay). There are ways to mitigate this attack vector, such as using multiple third-party relays and switching between them randomly or to use very strict framing, however both are imperfect and can lead to substantial inefficiencies.

A truly dark system is one that is utterly uncompromising in information leakage from metadata. At its most secure mode of operation, Whisper can (at a considerable cost of bandwidth and latency) deliver 100% dark operation. Even better, this applies not only for metadata collection from inter-peer conduits (i.e. backbone dragnet devices), but even against a much more arduous "100% - 2" attack; i.e. where every node in the network were compromised (though functional) save a pair running ÐApps for people that wanted to communicate without anybody else knowing.

### Routing and Lack Thereof

Fundamentally, at least with the present state of computer science, all systems present a trade off between the efficiency of deterministic (and thus supposedly optimal) routing and darkness (or, put another way, routing privacy). One of Whisper's differences is in providing a user-configurable trade-off between ones routing privacy and ones routing efficiency.

At its most dark, Whisper nodes are entirely reactive - they receive and record pieces of data and forward them trying to maximise the utility of information transmission to the peers. These pieces of information include what is known as a *topic*, which may be viewed both as a secure-probabilistic routing endpoint and/or a DHT multi-key.

However, Whisper is also designed to be able to route probabilistically using two methods, both giving away minimal routing information and both being exceptionally resilient to statistical attacks from large-scale metadata collection.

The first builds on the functionality of the ÐΞV-p2p backend. This backend provides the ability of Whisper to rate peers and, over time, probabilistically alter (or *steer*) its set of peers to those which tend to deliver useful (on-topic, timely, required for ones ÐApps to function) information. Ultimately, as the network evolves and the peer-set is steered, the number of hops between this peer and any others that tend to be good conduits of useful information (be they the emitters or simply the well-positioned hubs) will tend to 0.

Peer steering also provides the incentive for nodes to provide useful information to peers. The fear of being identified as an under-performing peer and thus being rotated out in favour of other nodes gives all nodes incentive to cooperate and share the most useful information. Useful in this case means provably difficult to author (use of a proof-of-work function helps here); a low time-to-live; and well-corresponding to any provided information on what might be useful (read on for more).

The second is more dynamic. Nodes are informed by their ÐApps over what sort of topics are useful. Nodes are then allowed to advertise to each peer describing these topics. Topics may be described, using masks or Bloom filters, and thus in an incomplete manner, to reduce the amount of information leaked and make passive statistical attacks arbitrarily difficult to execute. The determination of the amount of information to give to peers is both made through an explicit setting from the user and through topic/traffic modelling: "Given the information coming from this peer and my models of information distribution made from total traffic, am I receiving the amount of useful valuable information that I would expect to receive? If so, narrowing it down with additional description information to the peer may be warranted."

These settings can even be configured per-peer (more trusted/longer-lasting peers may be afforded greater amounts of information), and per-ÐApp (those ÐApps that may be more sensitive could be afforded less advertising than others). We can also make use of proof-of-work algorithms to minimise the changes of both DoS attacks and 'everything-but' attacks (where a peer is flooded with almost-useful information prompting it to give away more about its true requirements than it would do otherwise).

Through combining and reducing the Blooms/masks, weaker Nth-level information can be provided to peers about their peers' interests, forming a probabilistic topic-reception vortex around nodes, the "topic-space" gravity-well getting weaker and less certain the farther away with the network hop distance from any interested peers.

## Basic Protocol Elements

### Envelopes

There are two key concepts in Whisper: *Envelopes* and *Messages*. Envelopes may be considered items should Whisper be considered a DHT. Should Whisper be considered a datagram messaging system then envelopes become the packet format which house the potentially encrypted datagrams. Envelopes are necessarily comprehensible by any node (i.e. they themselves are unencrypted).

They contain information to the entry/datagram such as the original time to live (*TTL*), the absolute time for *expiry* and, importantly, the *topics*. Topics are a set of indexes to this item, recorded in order to help an interested party ("recipient") find it or have it routed to them. They might be likened to binary tags or keywords. Envelopes additionally contain a nonce allowing the original insertion node ("sender") to prove that some arbitrary amount of work was done in its composition. Finally, envelopes contain a message-*data* field; this stores the actual payload, together with some information and potentially a signature; together this forms a message. This field may be encrypted.

Envelopes are transmitted as RLP-encoded structures. The precise definition is given by:

[`expiry`: `P`, `ttl`: `P`, [`topic0`: `B_4`, `topic1`: `B_4`, ...], `data`: `B`, `nonce`: `P`]

Here, `ttl` is given in seconds, `expiry` is the Unix time of the intended expiry date/time for the envelope. Following this point in time the envelope should no longer be transmitted (or stored, unless there is some extenuating circumstance). Prior to this point in time less the `ttl` (i.e. the implied insertion time), the envelope is considered utterly invalid and should be dropped immediately and the transmitting peer punished.

`nonce` is an arbitrary value. We say the work proved through the value of the SHA3 of the concatenation of the nonce and the SHA3 of the RLP of the packet save the nonce (i.e. just a 4-item RLP list), each of the components as a fixed-length 256-bit hash. When this final hash is interpreted as a BE-encoded value, the smaller it is, the higher the work proved. This is used later to judge a peer.

### Topics

Topics are cryptographically secure, probabilistic partial-classifications of the message. Each topic in the set (order is unimportant) is determined as the first (left) 4 bytes of the SHA3-256 hash of some arbitrary data given by the original author of the message. These might e.g. correspond to "twitter" hash-tags or an intended recipient's public key hashed with some session nonce or application-identity.

Four bytes was chosen to minimise space should a large number of topics be mentioned while still keeping a sufficiently large space to avoid large-scale topic-collision (though it may yet be reviewed and possibly made dynamic in later revisions of the protocol).

### Messages

A message is formed as the concatenation of a single byte for flags (at present only a single flag is used), followed by any additional data (as stipulated by the flags) and finally the actual payload. This series of bytes is what forms the `data` item of the envelope and is always encrypted.

In the present protocol version, no explicit authentication token is given to indicate that the data field is encrypted; any would-be readers of the message must know ahead of time, through the choice of topic that they have specifically filtered for, that the message is encrypted with a particular key. This is likely to be altered in a further PoC to include a MAC.

Any determination that the message is indeed from a particular sender is left for a higher-level to address. This is noted through the Javascript API allowing the `to` parameter to be passed only at the point of specifying the filter. Since the signature is a part of the message and not outside in the envelope, those unable to decrypt the message data are also unable to access any signature.

- `flags`: 1 byte
- (`signature`: 65 bytes)
- `payload`: not fixed

Bit 0 of the flags determines whether the signature exists. All other bits are not yet given a purpose and should be set randomly. A message is invalid if bit 0 is set but the total data is less than 66 bytes (since this wouldn't allow it to contain a signature).

Payloads are encrypted in one of two ways. If the message has a specific recipient, then by using ECIES with the specific recipient's SECP-256k1 public key. If the message has no recipient, then by AES-256 with a randomly generated key. This key is then XORed with each of the *full* topics to form a salted topic. Each salted topic is stored prior to the encrypted data *in the same order as the corresponding topics are in the envelope header*.

As a recipient, payloads are decrypted in one of two ways. Through use of topics, it should be known whether the envelope is encrypted to a specific recipient (in which case use the private key to decrypt) or to a general multicast audience. In the latter case, we assume that at least one topic is known (since otherwise, the envelope could not be properly "identified"). In this case, we match the known *full* topic to one of the abridged topics in the envelope, determine the index and de-salt the according salted-key at the beginning of the data segment in order to retrieve the final key.

Encryption using the full topic with "routing" using the abridged topic ensures that nodes which are merely transiently storing the message and have no interest in the contents (thus have access only to routing information via the abridged topics) have no intrinsic ability to read the content of the message.

The signature, if provided, is the SHA3-256 hash of the unencrypted payload signed using ECDSA with the insertion-identity's secret key.

The signature portion is formed as the concatenation of the *r*, *s* and *v* parameters of the SECP-256k1 ECDSA signature, in that order. `v` is non-normalised and should be either 27 or 28. `r` and `s` are both big-endian encoded, fixed-width 32-byte unsigned.

The payload is otherwise unformatted binary data.

In the Javascript API, the distinction between envelopes and messages is blurred. This is because DApps should know nothing about envelopes whose message cannot be inspected; the fact that nodes pass envelopes around regardless of their ability to decode the message (or indeed their interest in it at all) is an important component in Whisper's dark communications strategy.

### Basic Operation

Nodes are expected to receive and send envelopes continuously, as per the [protocol specification](https://github.com/ethereum/wiki/wiki/Whisper-Wire-Protocol). They should maintain a map of envelopes, indexed by expiry time, and prune accordingly. They should also efficiently deliver messages to the front-end API through maintaining mappings between topics and envelopes.

When a node's envelope memory becomes exhausted, a node may drop envelopes it considers unimportant or unlikely to please its peers. Nodes should consider peers good that pass them envelopes with low TTLs and high proofs-of-work. Nodes should consider peers bad that pass then expired envelopes or, worse, those that have an implied insertion time prior to the present.

Nodes should always keep messages that its ÐApps have created. Though not in PoC-1, later editions of this protocol may allow ÐApps to mark messages as being "archived" and these should be stored and made available for additional time.

Nodes should retain a set of per-ÐApp topics it is interested in.

### Inserting (Authoring) Messages

To insert a message, little more is needed than to place the envelope containing it in the node's envelope set that it maintains; the node should, according to its normal heuristics retransmit the envelope in due course. Composing an envelope from a basic payload, possible identities for authoring and access, a number of topics, a time-to-live and some parameters concerning work-proving targets is done though a few steps:

- Compose `data` through concatenating the relevant flag byte, a signature of the payload if the user specified a valid author identity, and the user-given payload.
- Encrypt the data if an access ("destination") identity's public key is given by the user.
- Compose `topics` from the first 4 bytes of the SHA3 of each topic.
- Set user-given attribute `ttl`.
- Set the `expiry` as the present Unix time plus the time-to-live.
- Set the `nonce` as that which provides the most work proved as per the previous definition, after some fixed amount of time of cycling through candidates or after a candidate surpasses some boundary; either should be given by the user.

### Topic Masking and Advertising

Nodes can advertise their topics of interest to each other. For that purpose they use a special type of Whisper message (TopicFilterPacket). The size of Bloom Filter they send to each other must be 64 bytes. Subsequently the rating system will be introduced -- peers sending useful messages will be rated higher then those sending random messages. 

A message matches the bloom filter, if any one of the topics in this message, converted to the Whisper bloom hash, will match the bloom filter. 

Whisper bloom function accepts AbridgedTopic as a parameter (size: 4 bytes), and produces a 64-byte hash, where three bits (at the most) are set to one, and the rest are set to zeros, according to the following algorithm:

0. Set all the bits in the resulting 64-byte hash to zero.

1. We take 9 bits form the AbridgedTopic, and convert to integer value (range: 0 - 511).

2. This value defines the index of the bit in the resulting 512-bit hash, which should be set to one.

3. Repeat steps 1 & 2 for the second and third bit to be set in the resulting hash.

Thus, in order to produce the bloom, we use 27 bits out of 32 in the AbridgedTopic. For more details, please see the implementation of the function: TopicBloomFilterBase<N>::bloom() [libwhisper/BloomFilter.h].

### Coming changes

Also being considered for is support for plausible deniability through the use of session keys and a formalisation of the multicast mechanism.