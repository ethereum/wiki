---
name: Adaptive Peer Time
category: 
---

### Goals

At present, a single mining peer with its clock substantially (> 25% of the expected block time) ahead of the other peers will cause problems on the network. Forks are common as peers are inconsistently forced to ignore the future blocks.

A system with multiple peers should be robust to a few peers having substantially fast clocks.

### Basic Overview

Each peer does a ping-sequence, each message timestamped with the hardware clock (H) directly after handshake to determine both the network traversal distance ("ping time") and an estimate of the peer's hardware clock (Hp).

The clock-offset (D) of the peer then becomes its hardware clock (H) corrected to become the median of the peer hardware clocks (median({H1, H2, ...}) = M).

The clock offset (D) is dynamically evaluated as the peer set changes.

### Required Changes

Protocol changes:
* Ping & Pong packets include a timestamp.

Rest to be implemented lazily.