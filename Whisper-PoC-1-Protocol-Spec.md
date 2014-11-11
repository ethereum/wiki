This page, in addition to the Whisper Wire Specification, details the full Whisper protocol. From it you should be able to develop compliant Whisper implementations. This document is intended to give only the base specification. Many of the aspects leading to an implementation of Whisper are game theoretic and best not prescribed in the specification, but rather left to individual implementation teams to determine for themselves.

### What it is

Whisper combines aspects of both DHTs and datagram messaging systems (e.g. UDP). As such it may be likened and compared to either.

Whisper is a pure identity-based messaging system. Whisper provides a low-level (non-application-specific) but easily-accessible API without being based upon or prejudiced by the low-level hardware attributes and characteristics, particularly the notion of singular endpoints.

Alternatively, Whisper may be likened to a DHT with a per-entry configurable TTL and conventions for the signing and encryption of values. In this sense, Whisper provides the ability to have multiply-indexable, non-unique entries (i.e. the same entry having multiple keys, some or all of which may be the same as other entries).

### Envelopes and Messages

There are two key concepts in Whisper: *Envelopes* and *Messages*. Envelopes may be considered items should Whisper be considered a DHT. Should Whisper be considered a datagram messaging system then envelopes become the packet format which house the potentially encrypted datagrams. Envelopes are necessarily comprehensible by any node (i.e. they themselves are unencrypted).

They contain information to the entry/datagram such as the original time to live (*TTL*), the absolute time for *expiry* and, importantly, the *topics*. Topics are a set of indexes to this item, recorded in order to help an interested party ("recipient") find it or have it routed to them. They might be likened to binary tags or keywords. Envelopes additionally contain a nonce allowing the original insertion node ("sender") to prove that some arbitrary amount of work was done in its composition. Finally, envelopes contain a message-*data* field; this stores the actual payload, together with some information and potentially a signature; together this forms a message. This field may be encrypted.

Envelopes are transmitted as RLP-encoded structures. The precise definition is given by:

[`expiry`: `P`, `ttl`: `P`, [`topic0`: `B_4`, `topic1`: `B_4`, ...], `data`: `B`, `nonce`: `P`]

A message is formed as the concatenation of a single byte for flags (at present only a single flag is used), followed by any additional data (as stipulated by the flags) and finally the actual payload. This series of bytes is what forms the `data` item of the envelope, however, it may be encrypted. Notably, no explicit indication is given that the data field was encrypted; it is assumed any would-be readers know ahead of time through the choice of topic. Since the signature is a part of the message and not outside in the envelope, those unable to decrypt the message data are also unable to access any signature.



