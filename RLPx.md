    Draft specification

# Introduction
RLPx is a network layer which provides a general-purpose transport and interface for applications to communicate via a p2p network. The first version is geared towards building a robust transport, well-formed network, and software interface in order to provide infrastructure which meets the requirements of distributed or decentralized applications such as Ethereum. Encryption is employed to provide better privacy and integrity than would be provided by a cleartext implementation.

RLPx underpins the DEVp2p interface:  
https://github.com/ethereum/wiki/wiki/ÐΞVp2p-Wire-Protocol  
https://github.com/ethereum/wiki/wiki/libp2p-Whitepaper

# Features
* Node Discovery and Network Formation
* Peer Preference Strategies
* Peer Reputation
* Multiple protocols
* Encrypted handshake
* Encrypted transport
* Dynamically framed transport
* Fair queuing

# Security Overview
### Objectives
* nodes have access to a uniform network topology
* peers can uniformly connect to network
* protocols sharing a connection are provided uniform bandwidth
* authenticated connectivity
* authenticated discovery protocol
* encrypted transport
* robust protocol advertisement and versioning

# Network Formation
### Objectives
* nodes can resolve the endpoint information of other nodes via node ids
* new nodes can reliably find nodes to connect to
* nodes have sufficient network topology information to uniformly connect to other nodes
* nodes can resolve other nodes in 6 hops

A kademlia-like protocol is implemented in order to fascilitate a well-formed network. Major differences are that packets are signed, node ids are 512-bit public keys, and DHT features are not implemented.

# Transport
### Objectives
* multiple p2p protocols over a single connection
* encrypted
* flow control, simple quality of service

Authenticated encryption is employed in order to provide confidentiality and protect against network disruption. This is particularly important for a well-formed network where nodes make long-term decisions about other nodes which yield non-local effects.

Throughput guarantees, such that each protocol is allotted the same amount of bandwidth, are achieved by packet framing.

# Implementation Overview
Packets are dynamically framed, prefixed with an RLP encoded header, encrypted, and authenticated. Multiplexing is achieved via the frame header which specifies the destination protocol of a packet.

An RLPx implementation is composed of:
* Node Discovery & Peer Preference
* Encrypted Handshake
* Framing
* Multiplexing

# Node Discovery & Peer Preference
**Node**: An entity on the network.  
**Peer**: Node which is currently connected to local node.  
**NodeId**: public key of node

Node discovery and network formation are implemented via a kademlia-like protocol. The major differences are that packets are signed, node ids are the public keys, and DHT-related features are excluded. The FIND_VALUE and STORE packets are not implemented. The parameters necessary to implement the protocol are a bucket size of 16 (denoted k in Kademlia), concurrency of 3 (denoted alpha in Kademlia), and 8 bits per hop (denoted b in Kademlia) for routing. The eviction check interval is 75 milliseconds, request timeouts are 300ms, and the idle bucket-refresh interval is 3600 seconds.

Aside from the previously described exclusions, node discovery closely follows system and protocol described by Maymounkov and Mazieres.

Packets are signed and authenticated. Authentication is performed by recovering the public key from the signature and checking that it matches the expected value. Packet properties are serialized in the order in which they're defined.

RLPx provides 'potential' nodes along with distance information and can maintain connections for well-formedness based on an ideal peer count (default is 5). This strategy is implemented by connecting to 1 random node for every 'close' node which is connected.

Other connection strategies which can be manually implemented by a protocol; a protocol can use it's own metadata and strategies for making connectivity decisions.

**Note:** It is intended that the final version of this protocol be framed and encrypted using the same or similar structures as-is used for TCP packets. This change is expected following the first working implemention of the node discovery protocol, framing, and encryption.

Packet Encapsulation:

    mac || signature || packet-type || packet-data
		mac: sha3(pubkey || packet-type || packet-data)
		signature: sign(privkey, mac)
		packet-type: single byte < 2**7
    	packet-data: RLP encoded list. Packet properties are serialized in the order in which they're defined. See packet-data below.

DRAFT Encrypted Packet Encapsulation:

	mac || header-data || frame
	
    frame-size: 3-byte integer size of frame, big endian encoded
    header-data:
        normal: rlp.list(protocol-type[, sequence-id])
        chunked-0: rlp.list(protocol-type, sequence-id, total-packet-size)
		chunked-n: rlp.list(protocol-type, sequence-id)
        values:
            protocol-type: < 2**16
            sequence-id: < 2**16 (this value is optional for normal frames)
            total-packet-size: < 2**32

Packet Data (packet-data):

	PingNode packet-type: 0x01
    struct PingNode
    {
    	std::string ipAddress; // our IP
    	unsigned port; // our port
    	unsigned expiration;
    };
	
	Pong packet-type: 0x02
    struct Pong  // response to PingNode
    {
    	h256 replyTo; // hash of rlp of PingNode
    	unsigned expiration;
    };
	
	FindNode packet-type: 0x03
    struct FindNode
    {
    	NodeId target; // Id of a node. The responding node will send back nodes closest to the target.
    	unsigned expiration;
    };
	
	Neighbors packet-type: 0x04
    struct Neighbors  // reponse to FindNode
    {
    	struct Node
    	{
    		unsigned version = 0x01;
    		std::string ipAddress;
    		unsigned port;
    		NodeId node;
    	};
		
    	std::list<Node> nodes;
    	unsigned expiration;
    };
	
    NodeId is the node's public key.

# Encrypted Handshake
Connections are established via a handshake and, once established, packets are encapsulated as frames which are encrypted using AES-256 in CTR mode. Key material for the session is derived via a KDF with ECDHE-derived keying material. ECC uses secp256k1 curve (ECP). Note: "remote" is host which receives the connection.

The handshake is carried out in two phases. The first phase is authentication and key exchange and the second phase is to negotiate supported protocols. The authentication and key exchange is an ECIES encrypted message which includes ephemeral keys for Perfect Forward Secrecy (PFS). The second phase of the handshake is a part of DEVp2p and is an exchange of capabilities that each node supports. It's up to the implementation how to handle the outcome of the second phase of the handshake.

There are several variants of ECIES, of which, some modes are malleable and must not be used. This specification relies on the implementation of ECIES as defined by Shoup. Thus, decryption will not occur if ciphertext authentication fails. http://en.wikipedia.org/wiki/Integrated_Encryption_Scheme

There are two kinds of connections which can be established. A node can connect to a known peer, or a node can connect to a new peer. A known peer is one which has previously been connected to and from which a corresponding session token is available for authenticating the requested connection.

If the handshake fails, if and only if initiating a connection TO a known peer, then the nodes information should be removed from the node table and the connection MUST NOT be reattempted. Due to the limited IPv4 space and common ISP practices, this is likely a common and normal occurrence, therefore, no other action should occur. If a handshake fails for a connection which is received, no action pertaining to the node table should occur.

Handshake:

    New: authInitiator -> E(remote-pubk, S(ecdhe-random, ecdh-shared-secret^nonce) || H(ecdhe-random-pubk) || pubk || nonce || 0x0)
	     authRecipient -> E(remote-pubk, ecdhe-random-pubk || nonce || 0x0)
	
    Known: authInitiator = E(remote-pubk, S(ecdhe-random, token^nonce) || H(ecdhe-random-pubk) || pubk || nonce || 0x1)
           authRecipient = E(remote-pubk, ecdhe-random-pubk || nonce || 0x1) // token found
		   authRecipient = E(remote-pubk, ecdhe-random-pubk || nonce || 0x0) // token not found

Values generated following the handshake (see below for steps):

    ecdhe-shared-secret = ecdh.agree(ecdhe-random, remote-ecdhe-random-pubk)
    shared-secret = sha3(ecdhe-shared-secret || sha3(nonce || initiator-nonce))
    token = sha3(shared-secret)
    aes-secret = sha3(ecdhe-shared-secret || shared-secret)
    # destroy shared-secret
    mac-secret = sha3(ecdhe-shared-secret || aes-secret)
    # destroy ecdhe-shared-secret
    egress-mac = sha3(mac-secret^nonce || auth)
    # destroy nonce
    ingress-mac = sha3(mac-secret^initiator-nonce || auth)
    # destroy remote-nonce

Creating a connection (needs update!):

    1. initiator generates ecdhe-random and nonce and creates auth
    2. initiator connects to remote and sends auth

    3. remote generates ecdhe-random and nonce and creates auth
    4. remote receives auth and decrypts (ECIES performs authentication before decryption)

    5. remote sends auth
    6. remote derives shared-secret, aes-secret, mac-secret, ingress-mac, egress-mac
    7. remote sends protocol-handshake

    10. initiator receives auth
    11. initiator derives shared-secret, aes-secret, mac-secret, ingress-mac, egress-mac
    12. initiator sends protocol-handshake

    13. cryptographic handshake is complete if mac of protocol-handshake is valid; permanent-token is replaced with token
	14. begin sending/receiving data

Either side may disconnect if authentication of the first framed packet fails or if the protocol handshake isn't appropriate (ex: version is too old). All packets following auth, including protocol negotiation handshake, are framed.

# Framing
The primary purpose behind framing packets is in order to robustly support multiplexing multiple protocols over a single connection. Secondarily, as framed packets yield reasonable demarcation points for message authentication codes, supporting an encrypted stream becomes straight-forward. Accordingly, frames are authenticated via key material which is generated during the handshake.

When sending a packet over RLPx, the packet is framed. The frame header provides information about the size of the packet and the packet's source protocol. There are three slightly different frames, depending on whether or not the frame is delivering a multi-frame packet. A multi-frame packet is a packet which is split (aka chunked) into multiple frames because it's size is larger than the protocol window size (pws; see Multiplexing). When a packet is chunked into multiple frames, there is an implicit difference between the first frame and all subsequent frames. Thus, the three frame types are normal, chunked-0 (first frame of a multi-frame packet), and chunked-n (subsequent frames of a multi-frame packet).

      normal = not chunked
      chunked-0 = First frame of a multi-frame packet
      chunked-n = Subsequent frames for multi-frame packet
      || is concatenate
      ^ is xor

    Single-frame packet:
    header || header-mac || frame || mac

    Multi-frame packet:
    header || header-mac || frame-0 ||
    [ header || header-mac || frame-n || ... || ]
    header || header-mac || frame-last || mac

    header: frame-size || header-data || padding
    frame-size: 3-byte integer size of frame, big endian encoded
    header-data:
        normal: rlp.list(protocol-type[, sequence-id])
        chunked-0: rlp.list(protocol-type, sequence-id, total-packet-size)
		chunked-n: rlp.list(protocol-type, sequence-id)
        values:
            protocol-type: < 2**16
            sequence-id: < 2**16 (this value is optional for normal frames)
            total-packet-size: < 2**32
    padding: zero-fill to 16-byte boundary

    header-mac: right128 of egress-mac.update(aes(mac-secret,egress-mac)^header-ciphertext)

    frame:
        normal: rlp(packet-type) [|| rlp(packet-data)] || padding
		chunked-0: rlp(packet-type) || rlp(packet-data...)
        chunked-n: rlp(...packet-data) || padding
    padding: zero-fill to 16-byte boundary

    packet-mac: output of egress-mac.update(packet)

    egress-mac: h256, continuously updated with egress-bytes*
    ingress-mac: h256, continuously updated with ingress-bytes*

* \* Message authentication is achieved by continuously updating egress-mac or ingress-mac with the ciphertext of bytes sent (egress) or received (ingress); for headers the update is performed by xoring the header with the encrypted output of it's corresponding mac (see header-mac above for example). This is done to ensure uniform operations are performed for both plaintext mac and ciphertext.
* All macs are sent CLEARTEXT.
* Padding is used to prevent buffer starvation, such that frame components are byte-aligned to block size of cipher.
* Sequence-ids are local to the protocol-type and not the connection; two protocols may reuse the same sequence-ids.

Notes on Terminology:  
"Packet" is used because not all packets are messages; RLPx doesn't yet have a notation for destination address. "Frame" is used to refer to a packet which is to be transported over RLPx. Although somewhat confusing, the term "message" is used in the case of text (plain or cipher) which is authenticated via a message authentication code (MAC or mac).

# References
Petar Maymounkov and David Mazieres. Kademlia: A Peer-to-peer Information System Based on the XOR Metric. 2002. URL {http://www.cs.rice.edu/Conferences/IPTPS02/109.pdf}
Victor Shoup. A proposal for an ISO standard for public key encryption, Version 2.1. 2001. URL {http://www.shoup.net/papers/iso-2_1.pdf}
Gavin Wood. libp2p Whitepaper. 2014. URL {https://github.com/ethereum/wiki/wiki/libp2p-Whitepaper}
Vitalik Buterin. Ethereum: Merkle Patricia Tree Specification. 2014. URL {http://vitalik.ca/ethereum/patricia.html}

# Contributors
(Contributors: Please create PR w/name or alias to reference)

- protocol-type into frame header from packet-type offset
- considering unlimited channels
- full review, bug fixes
- review of cryptography
- intersection of nodes
- devp2p protocol and paper

RLPx was inspired by circuit-switched networks, BitTorrent, TLS, the Whisper protocol, and the PMT used by Ethereum.