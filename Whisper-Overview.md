```
var appName = "My silly app!";
var myName = "Gav Would";

shh.post({ "from": eth.key, "topic": [ eth.fromAscii(appName) ], "payload": [ myName, eth.fromAscii("What is your name?") ], "ttl": 100, "priority": 1000 });

var replyWatch = shh.watch({ "filter": [ eth.fromAscii(appName), eth.secretToAddress(eth.key) ] });
// could be filter: [eth.fromAscii(appName), null] if we wanted to filter all such messages for this app, but we'd be unable to read the contents.

replyWatch.arrived(function(m)
{
	// new message m
	console.log("Reply from " + eth.toAscii(m.payload) + " whose address is " + m.from;
});

var broadcastWatch = shh.watch({ "filter": [ eth.fromAscii(appName) ] });
broadcastWatch.arrived(function(m)
{
	if (m.from != eth.key)
	{
		// new message m: someone's asking for our name. Let's tell them.
		console.log("Broadcast from " + eth.toAscii(m.payload).substr(0, 32) + "; replying to tell them our name.");
		shh.post({ "from": eth.key, "to": m.from, "topic": [ eth.fromAscii(appName), m.from ], "payload": [ eth.fromAscii(myName) ], "ttl": 2, "priority": 500 });
	}
});
```

### Basic operation

`post` takes a JSON object containing four key parameters: 

```
shh.post({ "topic": t, "payload": p, "ttl": ttl, "priority": pri });
```

- `topic`, provided as either a list of items which is, during the packet construction, ameliorated into a single 256-bit secure hash, or the hash directly using the standard form of a hex string;
- `payload`, provided similarly to topic but left as an unformatted byte array provides the data to be sent.
- `ttl` is a time for the message to live on the network, specified in seconds. This defaults to 60.
- `priority` is the amount of priority you want the packet to have on the network. It is specified in milliseconds of processing time on your machine. This defaults to 50.

Two other parameters optionally specify the addressing: recipient (`to`), sender (`from`). The latter is meaningless unless a recipient has been specified.

### Use cases
- `shh.post({ "topic": t, "payload": p });` No signature, no encryption: Anonymous broadcast; a bit like an anonymous subject-filtered twitter feed.
- `shh.post({ "from": eth.key, "topic": t, "payload": p });` Open signature, no encryption: Clear-signed broadcast; a bit like a normal twitter feed - anyone interested can see a particular identity is sending particular stuff out to no-one in particular.
- `shh.post({ "to": recipient, "topic": t, "payload": p });` No signature, encryption: Encrypted anonymous message; a bit like an anonymous drop-box - message is private to the owner of the dropbox. They can't tell from whom it is.
- `shh.post({ "from": eth.key, "to": recipient, "topic": t, "payload": p });` Secret signature, encryption: Encrypted signed message; like a secure e-mail. One identity tells another something - nobody else can read it. The recipient alone knows it came from the sender.

In addition to the basic use cases, there will also be support for secure multi-casting. For this, you set up a group with `shh.newGroup`:

```
var group = shh.newGroup(eth.key, [ recipient1, recipient2 ]);
```

Then can use this as a recipient as you would normally:

```
shh.post({ "from": eth.key, "to": group, "topic": t, "payload": p });
```

The `newGroup` actually does something like:

```
var keypair = eth.newKeyPair();
shh.post([ "from": eth.key, "to": recipient1, "topic": ["new_shared_key", recipient1], "payload": keypair.secret ]);
shh.post([ "from": eth.key, "to": recipient2, "topic": ["new_shared_key", recipient2], "payload": keypair.secret ]);
return keypair;
```

Here, the `"new_shared_key"` topic is a sub-band topic (intercepted by the Whisper protocol layer) which takes the key and adds it to the key database. When a packet is addressed to `group`, it encrypts with group's public key. `group` is not generally used for signing.

When signing a message (one with a `from` parameter), the message-hash is the hash of the clear-text (unencrypted) payload.

Topic is constructed from a number of components - this simply compresses (sha3 + crop) and arranges into a final 256-bit crypto-secure hash. When composing filters, it's the same process. Importantly, the same total number of components must be specified and in the same order. However, some may be substituted by a null: this acts like a wildcard for that portion of the topic.

To filter on sender/recipient, they should be encoded within the topic by the sender.

### Wire Protocol

Messages are formed by the RLP from a number of attributes:
```
[
  timestamp: u32,
  ttl: u32,
  sig: h520,
  topic: h256,
  payload: bytes,
  nonce: h256
]
```

The `sig` is a single 520-bit hash that forms the signature from the concatenation of the triplet `v` (8 bits), `r` and `s` (256 bits each). The reason for concatenating is to avoid passing composite types around for a single conceptual noun and thus provide a cleaner upgrade path should the nature of the signature change. 

Messages are forwarded favouring several attributes:
- The magnitude of the hash of the `nonce ++ SHA3(packet without nonce)`, where `++` is the concatenation operator and each of the operands is a fixed-length 256-bit hash. When this hash is interpreted as a BE-encoded value: the smaller, the higher the priority.
- The TTL: the smaller, the higher the priority.
- The size of the payload: the smaller, the higher the priority.
- If the signature is clear, then according to the magnitude of the address (interpreted as a big-endian encoded value): the smaller, the higher the priority.

Any messages that arrive with a `timestamp` after the present time are immediately discarded. Any messages whose `timestamp + ttl` is before the present time are also discarded.
