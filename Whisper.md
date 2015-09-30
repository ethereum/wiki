---
name: Whisper
category: 
---

### Use case

* DApps that need to publish small amounts of information to each other and have the publication last some substantial amount of time. For example, a currency exchange DApp may use it to record an offer to sell some currency at a particular rate on an exchange. In this case, it may last anything between tens of minutes and days. The offer wouldn't be binding, merely a hint to get a potential deal started.

* DApps that need to signal to each other in order to ultimately collaborate on a transaction. For example, a currency exchange DApp may use it to coordinate an offer prior to creating one (or two, depending on how the exchange is structured) transactions on the exchange.

* DApps that need to provide non-real-time hinting or general communications between each other. E.g. a small chat-room app.

* DApps that need to provide dark (plausible denial over perfect network traffic analysis) comms to two correspondents that know nothing of each other but a hash. This could be a DApp for a whistleblower to communicate to a known journalist exchange some small amount of verifiable material and arrange between themselves for some other protocol (Swarm, perhaps) to handle the bulk transfer.

In general, think transactions, but without the eventual archival, any necessity of being bound to what is said or automated execution & state change.

### Specs

* **Low-level** API only exposed to DApps, never to users.
* **Low-bandwidth** Not designed for large data transfers.
* **Uncertain-latency** Not designed for RTC.
* **Dark** No reliable methods for tracing packets or 
* Typical usage:
  * Low-latency, 1-1 or 1-N signalling messages.
  * High latency, high TTL 1-* publication messages.

Messages less than 64K bytes, typically around 256 bytes.

### Existing solutions

* UDP: Similar in API-level, native multicasting. No TTL, security or privacy safeguards.
* [0MQ](http://zeromq.org/): A distributed messaging system, no inherant privacy safeguards.
* [Bitmessage](https://bitmessage.org/wiki/Main_Page): Similar in the basic approach of P2P network exchanging messages with baseline PKI for dark comms. Higher-level (e-mail replacement, only "several thousand/day", larger mails), fixed TTL and no hinting to optimise for throughput. Unclear incentivisation.
* [TeleHash](https://github.com/telehash/telehash.org/blob/master/network.md#paths): Secure connection-orientated RTC comms. Similar in approach to BitTorrent (uses modified Kademila tech), but rather than discovering peers for a given hash, it routes to the recipient given its hash. Uses DHT to do deterministic routing therefore insecure against simple statistical packet-analysis attacks against a large-scale attacker. Connection oriented, so no TTL and not designed for asynchronous data publication.
* [Tox](https://github.com/irungentoo/toxcore/blob/master/docs/updates/DHT.md): Higher-level (IM & AV chat) replacement.

### Basic Design

Uses the `"shh"` protocol string of ÐΞVP2P.

Rest coming soon, once I've finished prototyping. Gav.

### Considerations for Defeating Traffic Analysis
(from lokiverloren) All existing protocols for location obscured instant messaging have complicated problems to do with routing. 

The Bitmessage protocol propagates messages blindly across the network, and the proper recipient knows how to decrypt it and receives it (just like everyone else) but then stores it and lets its' user know it's got a new message. The problem with this of course is that it greatly increases the exposure of the whole network to a body of encrypted material that ideally should not be easily accessed at all.

None of the others listed above particularly have any means to hide the source and destination of messages. I believe it is one of the core objectives of the Whisper protocol to hide location of sender and receiver and in transit, make it difficult if not impossible to establish one, the other or both.

The Tor system has a protocol for enabling connections between two nodes without either knowing where the other is, it is the Rendezvous protocol used for hidden services. Most readers of this will already understand how it works, but what happens is that a hidden service (the equivalent of a server listening on a port in the TCP/IP protocol) selects at random a number (I think, usually 6) 'introducer' nodes. In order to do this it establishes the standard 3-hop chain to each of the introducers, and when a user wants to establish a connection with a hidden service, they propagate a request to connect with hidden service that is associated with a particular public key. I'm not sure about how this request is propagated, obviously it would also have to be done through a circuit or the rendezvous introducer node knows who might be about to establish a circuit. Once one's client knows a valid rendezvous node it then establishes a connection to it and requests to have traffic relayed through its' circuit to the hidden service.

I think there really isn't a better way to implement 'dark' parts of the connection protocol, because there has to be a different identity for a relay node as a client node, the same principle applies in Ethereum. However, using the rendezvous, it again makes possible something for connections between nodes that know each other (though they don't know the account or identity of the initiator) something other than the uniform 3 hop circuit. For generating a circuit to an exit node in Tor, it is of no danger that your client knows the identity of the relays in each hop along the way, but to do that *within* the network compromises location obfuscation security, tying your ethereum identity with your router identity (I think it may need to be explicitly pointed out that routing function is something that Ethereum nodes will perform).  When connecting to a rendezvous, you do not thereby reveal your location to the rendezvous, and the rendezvous does not know the location of the hidden service either, and establishing these connections only requires a public directory to be created for routers. It is not consequential information for an attacker and can be revealed directly by probing for a relevant server at your IP addresses anyway.

The contribution I would like to make to the discussion is this: 

1. It is implied that the whisper protocol has to work through a system of location obfuscation relays like Tor. Not only this, but it must therefore also be using some form of the rendezvous protocol used in Tor for hidden services.

2. However, as Tor was originally designed first to be a way to stop webservers logging your IP address in association with a session cookie and record, then secondarily added the ability to implement the equivalent of TCP/IP routing (listening ports) within the context of location obfuscation in the form of the rendezvous protocol, it is an artifact, a legacy of the first purpose that leads to the uniform utilisation of 3 hop nodes and connections that remain open rather than a session (connectionless) protocol like used in HTTP. What I suggest is that, depending on the needs of a particular connection, there can be occasion to mix up the obfuscation process so that it is further obscured from traffic analysis in the case of malicious nodes performing data gathering for an attacker. 

Instead of a connection like is normal between a browser and web server, for example, which is usually left open because of the latency of reestablishing such a connection, to make further requests, instead there can be a session cookie, and then you can alter the way your client sends data through the 'connection' to a hidden node. The connection, between two routing nodes, could be direct, 1 proxy intermediary, 2, or 3, it could use shared secrets instead and route fragments of datagrams across multiple heteregenous circuits, as well, and the receiver would then wait for sufficient fragments to assemble the original packets. Indeed, it could be possible in the case of (not quite related to whisper, but to the distributed data storage protocol) larger streams, break the stream up into parts and alter the obfuscation method through the process, further confusing the traffic analysis data.

The same considerations apply, fundamentally, the only differences have to do with the size of the data being transmitted, whether it's for a messaging system or distributed filesystem. The other criteria for deciding how to scramble the routing is latency. For some purposes one wants lower latency, and other purposes, greater security is vitally important. When in the process streams are fragmented into parts, it can also increase security to apply an All Or Nothing Transform to the entire package, then if part is intercepted but not the complete message, it is impossible to assemble the data, not even for cryptanalysis purposes.