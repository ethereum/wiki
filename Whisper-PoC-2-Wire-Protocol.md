---
name: Whisper PoC-2 Wire Protocol
category: 
---

Peer-to-peer communications between nodes running Whisper clients run using the underlying [ÐΞVp2p Wire Protocol](https://github.com/ethereum/wiki/wiki/%C3%90%CE%9EVp2p-Wire-Protocol).

This is a preliminary wire protocol for the Whisper subsystem. It will change.

### Whisper Sub-protocol

**Status**
[`+0x00`: `P`, `protocolVersion`: `P`] Inform a peer of the **whisper** status. This message should be send _after_ the initial handshake and _prior_ to any **whisper** related messages.
* `protocolVersion` is one of:
    * `0x02` for PoC-7.

**Messages**
[`+0x01`: `P`, [`expiry1`: `P`, `ttl1`: `P`, [`topic1x1`: `B_4`, `topic1x2`: `B_4`, ...], `data1`: `B`, `nonce1`: `P`], [`expiry2`: `P`, ...], ...] Specify one or more messages. Nodes should not resend the same message to a peer in the same session, nor send a message back to a peer from which it received. This packet may be empty. The packet must be sent at least once per second, and only after receiving a **Messages** message from the peer.

**TopicFilter**
[`+0x02`: `P`, `bloom`: `B_64`] Specify the bloom filter of the topics that the sender is interested in. The bloom method is defined below.

### Session Management

For the Whisper sub-protocol, upon an active session, a `Status` message must be sent. Following the reception of the peer's `Status` message, the Whisper session is active. The peer with the greatest Node Id should send a **Messages** message to begin the message rally. From that point, peers take it in turns to send (possibly empty) Messages packets.

### Topics and Abridged Topics

*Topics* are 32-byte hashes, typically generated from ÐApp-specified data. The *abridged topic* is the first 4 bytes of the topic. Abridged topics allow the identification (within the bounds of an acceptably high probability) of a given topic, useful for determining the utility of a topic for a given peer, yet do not give away the full topic information itself, allowing it to be used as a strong encryption key whose secret is not inherently available.

### Bloomed Topics

The Bloom filter used in the `TopicFilter` message type is a means a identifying a number of topics to a peer without compromising (too much) privacy over precisely what topics are of interest. Precise control over the information content (and thus efficiency of the filter) may be maintained through the addition of bits.

Blooms are formed by the bitwise OR operation on a number of bloomed topics. The bloom function takes the abridged topic (the first four bytes of the SHA3 of the ÐApp/user level topic description) and projects them onto a 512-bit slice; in total, three bits are marked for each bloomed topic.

The projection function is defined as a mapping from a a 4-byte slice `S` to a 512-bit slice `D`; for ease of explanation, `S` will dereference to bytes, whereas `D` will dereference to bits.

```
LET D[*] = 0
FOREACH i IN { 0, 1, 2 } DO
LET n = S[i]
IF S[3] & (2 ** i) THEN n += 512
D[n] = 1
END FOR
```

In formal notation:
```
D[i] := f(0) == i || f(1) == i || f(2) == i
```
where:
```
f(x) := S[x] + S[3][x] * 512
```
(assuming a byte value `S[i]` may be further dereferenced into a bit value)

