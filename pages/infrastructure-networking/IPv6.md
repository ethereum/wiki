---
name: IPv6
category: 
---

### Goals

Allow IPv6 to be used over the ethereum peer network

### Basic Design

Allow IP addresses to be specified either as a 4-byte array (IPv4) or a 16-byte array (IPv6).

### Needed Changes

**Peers**
[`0x05`, [`ip1`: `B_4` OR `B_16`, `port1`: `P`, `id1`: `B_64`], [`ip2`: `B_4` OR `B_16`, `port2`: `P`, `id2`: `B_64`], ... ] Specifies a number of known peers.
* `ip` is either a 4-byte array 'ABCD' that should be interpreted as the IPv4 address A.B.C.D or a 8-byte array 'ABCDEFGHIJKLMNOP' that should be interpreted as the IPv6 address AB:CD:EF:GH:IJ:KL:MN:OP.
* `port` is a 2-byte array that should be interpreted as a 16-bit big-endian integer.
* `id` is the 512-bit hash that acts as the unique identifier of the node.
