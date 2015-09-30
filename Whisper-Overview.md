---
name: Whisper Overview
category: 
---

### Example ("Dream") API Usage

```js
var shh = web3.shh;
var appName = "My silly app!";
var myName = "Gav Would";
var myIdentity = shh.newIdentity();

shh.post({
  "from": myIdentity,
  "topic": [ web3.fromAscii(appName) ],
  "payload": [ web3.fromAscii(myName), web3.fromAscii("What is your name?") ],
  "ttl": 100,
  "priority": 1000
});

var replyWatch = shh.watch({
  "topic": [ web3.fromAscii(appName), myIdentity ],
  "to": myIdentity
});
// could be "topic": [ web3.fromAscii(appName), null ] if we wanted to filter all such
// messages for this app, but we'd be unable to read the contents.

replyWatch.arrived(function(m)
{
	// new message m
	console.log("Reply from " + web3.toAscii(m.payload) + " whose address is " + m.from);
});

var broadcastWatch = shh.watch({ "topic": [ web3.fromAscii(appName) ] });
broadcastWatch.arrived(function(m)
{
  if (m.from != myIdentity)
  {
    // new message m: someone's asking for our name. Let's tell them.
    var broadcaster = web3.toAscii(m.payload).substr(0, 32);
    console.log("Broadcast from " + broadcaster + "; replying to tell them our name.");
    shh.post({
      "from": eth.key,
      "to": m.from,
      "topics": [ eth.fromAscii(appName), m.from ],
      "payload": [ eth.fromAscii(myName) ],
      "ttl": 2,
      "priority": 500
    });
  }
});
```

### Basic operation

`post` takes a JSON object containing four key parameters: 

```js
shh.post({ "topic": t, "payload": p, "ttl": ttl, "workToProve": work });
```

- `topic`, provided as either a list of, or a single, arbitrary data items that are used to encode the abstract topic of this message, later used to filter messages for those that are of interest;
- `payload`, provided similarly to topic but left as an unformatted byte array provides the data to be sent.
- `ttl` is a time for the message to live on the network, specified in seconds. This defaults to 50.
- `work` is the amount of priority you want the packet to have on the network. It is specified in milliseconds of processing time on your machine. This defaults to 50.

Two other parameters optionally specify the addressing: recipient (`to`), sender (`from`). The latter is meaningless unless a recipient has been specified.

### Use cases
- `shh.post({ "topic": t, "payload": p });` No signature, no encryption: Anonymous broadcast; a bit like an anonymous subject-filtered twitter feed.
- `shh.post({ "from": myIdentity, "topic": t, "payload": p });` Open signature, no encryption: Clear-signed broadcast; a bit like a normal twitter feed - anyone interested can see a particular identity is sending particular stuff out to no-one in particular.
- `shh.post({ "to": recipient, "topic": t, "payload": p });` No signature, encryption: Encrypted anonymous message; a bit like an anonymous drop-box - message is private to the owner of the dropbox. They can't tell from whom it is.
- `shh.post({ "from": myIdentity, "to": recipient, "topic": t, "payload": p });` Secret signature, encryption: Encrypted signed message; like a secure e-mail. One identity tells another something - nobody else can read it. The recipient alone knows it came from the sender.
- `shh.post({ "from": myIdentity, "to": recipient, "topic": t, "payload": p, "deniable": d });` Secret signature, encryption with optional plausible deniability. If boolean parameter `d` is **false**, it is equivalent to the previous call. If `d` is **true**, recipient cannot prove to any third party that the message originates from sender, though still can verify it for herself. This is achieved by the digital signature being calculated on the symmetric session encryption key instead of the message body.

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
var group = shh.newIdentity();
shh.post([ "from": myIdentity, "to": recipient1, "topic": [invSHA3(2^255), recipient1], "payload": secretFromPublic(group) ]);
shh.post([ "from": myIdentity, "to": recipient2, "topic": [invSHA3(2^255), recipient2], "payload": secretFromPublic(group) ]);
return keypair;
```

Here, the `invSHA3(2^255)` topic is a sub-band topic (intercepted by the Whisper protocol layer) which takes the key and adds it to the key database. When a packet is addressed to `group`, it encrypts with group's public key. `group` is not generally used for signing. `secretFromPublic` obviously isn't a public API and invSHA3 is only possible because we know each item of the topic is SHA3ed prior to amalgamation in the final topic.

When signing a message (one with a `from` parameter), the message-hash is the hash of the clear-text (unencrypted) payload.

Topics is constructed from a number of components - this simply compresses (sha3 + crop) each into a final set of 4-byte crypto-secure hashes. When composing filters, it's the same process. Importantly, all such hashes given in the filters must be includes in.

To filter on sender/recipient, they should be encoded within the topic by the sender.

### Silent Operation

In normal operation (and assuming a non-degenerate attack condition), there is a trade-off between true anonymity/plausible deniability over ones communications and efficiency of operation. The more one advertises to ones peers attempting to "fish" for useful messages and steer such message towards oneself, the more one reveals to ones peers.

For a securely anonymous dynamic two-way conversation, this trade-off becomes problematic; significant topic-advertising would be necessary for the point-to-point conversation to happen with sensible latency and yet so little about the topic can be advertised to guide messages home without revealing substantial information should there be adversary peers around an endpoint. (If substantial numbers of adversary peers surround both endpoints, a tunnelling system similar to TOR must be used to guarantee security.)

In this situation, dynamic topic generation would be used. This effectively turns the datagram-orientated channel into a connection-oriented channel. The endpoint to begin the conversation sends a point-to-point (i.e. signed and encrypted) conversation-begin message that contains a randomly chosen 256-bit topic seed. The seed is combined (by both endpoints) with a message nonce (beginning at 0 and incrementing over the course of the conversation) to provide a secure chain of single-use topics. It then generates a bloom filter using randomly selected bits from the new topic to match against and gives the filter to its peers; once a randomly selected minimum of messages fitting this filter have been collected (we assume one of these is the message we are interested in), we send our reply, deriving a new topic (incrementing the nonce), then advertise for that with yet another topic (another nonce increment) with another randomly selected group of bits.
