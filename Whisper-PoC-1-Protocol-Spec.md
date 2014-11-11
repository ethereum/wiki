This page, in addition to the Whisper Wire Specification, details the full Whisper protocol. From it you should be able to develop compliant Whisper implementations. This document is intended to give only the base specification. Many of the aspects leading to an implementation of Whisper are game theoretic and best not prescribed in the specification, but rather left to individual implementation teams to determine for themselves.

### What it is

Whisper combines aspects of both DHTs and datagram messaging systems (e.g. UDP). As such it may be likened and compared to either.

Whisper is a pure identity-based messaging system. Whisper provides a low-level (non-application-specific) but easily-accessible API without being based upon or prejudiced by the low-level hardware attributes and characteristics, particularly the notion of singular endpoints.

Alternatively, Whisper may be likened to a DHT with a per-entry configurable TTL and conventions for the signing and encryption of values. In this sense, Whisper provides the ability to have multiply-indexable, non-unique entries (i.e. the same entry having multiple keys, some or all of which may be the same as other entries).

### Envelopes and Messages

There are two key concepts in Whisper: Envelopes and Messages. Envelopes may be considered items should Whisper be considered a DHT. Should Whisper be considered a datagram messaging system then envelopes become the packet format which house the potentially encrypted datagrams. Envelopes are necessarily comprehensible by any node (i.e. they themselves are unencrypted). They contain information to the entry/datagram such as the time to live, the original send date and, importantly, the *topics*. Topics are a set of indexes to this item, recorded in order to help an interested party ("recipient") find it or have it routed to them. They might be likened to binary tags or keywords.

Each envelope contains a message, however messages may be encrypted.





