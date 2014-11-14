This page, in addition to the Whisper Wire Specification, details the full Whisper protocol. From it you should be able to develop compliant Whisper implementations. This document is intended to give only the base specification. Many of the aspects leading to an implementation of Whisper are game theoretic and best not prescribed in the specification, but rather left to individual implementation teams to determine for themselves.

### What it is

Whisper combines aspects of both DHTs and datagram messaging systems (e.g. UDP). As such it may be likened and compared to both, not dissimilar to the matter/energy duality (apologies to physicists for the blatant abuse of a fundamental and beautiful natural principle).

Whisper is a pure identity-based messaging system. Whisper provides a low-level (non-application-specific) but easily-accessible API without being based upon or prejudiced by the low-level hardware attributes and characteristics, particularly the notion of singular endpoints.

Alternatively, Whisper may be likened to a DHT with a per-entry configurable TTL and conventions for the signing and encryption of values. In this sense, Whisper provides the ability to have multiply-indexable, non-unique entries (i.e. the same entry having multiple keys, some or all of which may be the same as other entries).

As such, Whisper is not a typical communications system. It is not designed to replace or substitute TCP/IP, UDP, HTTP or any other traditional protocols. Whisper is a new protocol designed expressly for a new paradigm of application development. It is designed from the ground up for easy and efficient multi-casting and broadcasting. Similarly, low-level partially-asynchronous communications is an important goal. Spam-reduction or retarding is another goal. It is designed to be a building block in next generation ÐApps which requires large-scale many-to-many discovery and transmission with an absolute minimum of fuss and the expectation that one has a very reasonable assurance of complete privacy.

### Pitch-Black Darkness

Whisper operates around the notion of being user-configurable with regard to how much information it leaks concerning the ÐApp content and ultimately, the user activities. To understand information leakage, it is important to distinguish between mere encryption and darkness. Many protocols, both those designed around p2p and more traditional client/server models provide a level of encryption. For some, like Tor, encryption forms an intrinsic part of the protocol and delivers its primary requirement. While decentralising and encrypting is a great start on building a truly egalitarian Web Three, it is not the end.

Even with encrypted communications, well-funded attackers are still able to compromise ones privacy, often quite easily. Bulk metadata collection becomes the new battleground, and is at once dismissed as a privacy concern by authorities yet trumpeted as the Next Big Thing by big data outfits. In the case of a simple client/server model, metadata betrays with which hosts one communicates - this is often plenty enough to compromise privacy given that content is, in many cases, largely determinable from the host.

With decentralised communications systems, e.g. a basic non-routed but encrypted Skype call or Telehash communication, a network packet-sniffing attacker may not be able tell precise what one were saying on a call, but they would be able to determine to whom one communicated, when and how often. Even with encryption and packet forwarding through a third relay node, there is still ample room for a determined bulk packet-collector to execute statistical attacks, effectively using their knowledge that nodes tend to forward messages between the same two peers with minimal latency. There are ways to mitigate this attack vector, such as using multiple third-party relays and switching between them randomly or to use very strict framing, however both are imperfect and can lead to substantial inefficiencies.

A truly dark system is one utterly uncompromising in information leakage from metadata. At its most secure mode of operation, Whisper can (at a considerable cost of bandwidth and latency) deliver 100% dark operation. Even better, this applies not only for metadata collection from inter-peer conduits, but even against a much more arduous "100% - 2" attack; i.e. where every node in the network were compromised (though functional) save a pair running ÐApps for people that wanted to communicate without anybody else knowing.

### Routing and Lack Thereof

Fundamentally, at least with the present state of computer science, all systems present a trade off between the efficiency of a deterministic (and thus supposedly optimal) routing and darkness (or, put another way, routing privacy). One of Whisper's differences is in providing a user-configurable trade-off between ones routing privacy and ones routing efficiency.

At its most dark, Whisper nodes are entirely reactive - they receive and record pieces of data and forward them trying to maximise the utility of information transmission to the peers. These pieces of information include what is known as a *topic*, which may be viewed both as a probcryptographic routing endpoint and/or a DHT multi-key.

However, Whisper is also designed to be able to route probabilistically using two methods, both giving away minimal routing information and both being exceptionally resilient to statistical attacks from large-scale metadata collection.

### Envelopes

There are two key concepts in Whisper: *Envelopes* and *Messages*. Envelopes may be considered items should Whisper be considered a DHT. Should Whisper be considered a datagram messaging system then envelopes become the packet format which house the potentially encrypted datagrams. Envelopes are necessarily comprehensible by any node (i.e. they themselves are unencrypted).

They contain information to the entry/datagram such as the original time to live (*TTL*), the absolute time for *expiry* and, importantly, the *topics*. Topics are a set of indexes to this item, recorded in order to help an interested party ("recipient") find it or have it routed to them. They might be likened to binary tags or keywords. Envelopes additionally contain a nonce allowing the original insertion node ("sender") to prove that some arbitrary amount of work was done in its composition. Finally, envelopes contain a message-*data* field; this stores the actual payload, together with some information and potentially a signature; together this forms a message. This field may be encrypted.

Envelopes are transmitted as RLP-encoded structures. The precise definition is given by:

[`expiry`: `P`, `ttl`: `P`, [`topic0`: `B_4`, `topic1`: `B_4`, ...], `data`: `B`, `nonce`: `P`]

### Messages

A message is formed as the concatenation of a single byte for flags (at present only a single flag is used), followed by any additional data (as stipulated by the flags) and finally the actual payload. This series of bytes is what forms the `data` item of the envelope, however, it may be encrypted. Notably, no explicit indication is given that the data field was encrypted; it is assumed any would-be readers know ahead of time through the choice of topic. Since the signature is a part of the message and not outside in the envelope, those unable to decrypt the message data are also unable to access any signature.

- `flags`: 1 byte
- (`signature`: 65 bytes)
- `payload`: not fixed

Bit 0 of the flags determines whether the signature exists. All other bits are not yet given a purpose and must be set to 0. A message is invalid if bit 0 is set but the total data is less than 66 bytes (since this wouldn't allow it to contain a signature).

The signature is formed as the concatenation of the *r*, *s* and *v* parameters, in that order. `v` is non-normalised and should be either 27 or 28. `r` and `s` are both big-endian encoded, fixed-width 32-byte unsigned.

The payload is otherwise unformatted binary data.

In the Javascript API, the distinction between envelopes and messages is blurred. This is because DApps should know nothing about envelopes whose message cannot be inspected; the fact that nodes pass envelopes around regardless of their ability to decode the message (or indeed their interest in it at all) is an important component in Whisper's dark communications strategy.




