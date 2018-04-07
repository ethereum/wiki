---
name: Lib P2P Whitepaper
category: 
---

**NOTE:** *This is a work-in-progress*

For Ethereum to succeed, and for the ultimate goals of Ethereum to be achieved, Ethereum needs to employ a number of secure decentralised data systems. The generalised Turing-complete, extensible-state blockchain is one component in this, but for it to be leveraged to its full potential for building decentralised applications (ÐApps), a suite of additional data systems are necessary. Each decentralised-datasystem solves specific needs; in general it is difficult to predict what data systems will ultimately be required since the decentralised paradigm is not immediately comparable, like-for-like with traditional centralised architected systems.

Three types of data systems in particular are required for many current massively multi-user applications (MMAs, aka generally Web-based applications but also mobile phone apps and e.g. MMORPGs). In addition to a transactional database ("Ethereum"), a publication and download system would be required in addition to a generalised low-level "bulletin-board" system for posting messages.

We can, however, imagine many more such types of decentralised communications systems in the future including, e.g., those for identity-based RTC. Each of these components have a number of shared prerequisites, such as peer-discovery and recording; network well-formedness; transport-level buffering, scheduling and framing; and authentication and security. Any computer scientist worth their salt would instantly scream out "abstraction opportunity!".

### What it is

libp2p (aka ÐΞVp2p) aims to provide a lightweight abstraction layer that provides these low-level algorithms, protocols and services in a transparent framework without predetermining the eventual transmission-use-cases of the protocols.

Its specific aims are to provide a language-agnostic API and specification which is:
- *Universal:* Pairwise addressing (ala Telehash), broadcast (ala Bitcoin), groupwise (some DHT designs & filesharing) are all reasonable. There may be others. It should provide only the network structure, not dictate usage over it.
- *Ubiquitous:* The same peer-set/-network should be able to be used for all protocols.
- *Secure:* Encryption between physical peers is a given. Peer introduction can provide a good level of defence against systematic MitM attacks.
- *Efficient:* Framing and prioritisation to guarantee QoS over each protocol. Multiplexing allows access to limited resources to be easily controlled between p2p protocols. Kademlia-style network well-formedness guarantees a low maximum hope distance to any peer in the network and its group.
- *Simple:* A minimal, developer-driven API gives source-level future-proofing.

Ultimately, additional secondary features will also be explored:
- *DPI security:* Framing and bandwidth control can be used to control traffic shape.
- *Transport-layer Agnostic:* TCP/IP v4 and v6 are provided with initial protocol specifications. Others should be able to be added on at later dates without altering the API. Mesh networking devices could even ultimately implement this natively.

### Basic Design

- Peers can each be identified by a node-ID.
- Provides ability *only* to communicate with peers.
- Everything happens in typed, ordered datagrams.
- Any number of datagram types can be registered - these are automatically negotiated at the handshake.
- Peers can be requested via a libp2p physical endpoint.
- Peers can be rated per-protocol using a local metric.
- Peer set is dynamic and "steered" by libp2p internally, going from ratings.
- First packets are either DH key exchange or, if node known from peer introduction, PKI-encrypted session key, or if node known from previous session, new session key encrypted by old. Retry can be made after failed negotiation using prior knowledge, with the corresponding removal of any trust.
- Peer-set is made up from multiple sub-protocol slots.
- Each slot is given over to maximise rating for that particular sub-protocol.

There is no preordained inter-node message routing system (this is left to a higher-level), and no preordained high-level identity system (again, higher level).
