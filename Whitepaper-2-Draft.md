WORK IN PROGRESS!

### A Next-Generation Smart Contract and Decentralized Application Platform

When Satoshi Nakamoto first set the Bitcoin blockchain into motion in January 2009, he was simultaneously introducing two radical and highly untested concepts. The first is the "bitcoin", a decentralized peer-to-peer online currency that maintains a value without any backing, [intrinsic value](http://bitcoinmagazine.com/8640/an-exploration-of-intrinsic-value-what-it-is-why-bitcoin-doesnt-have-it-and-why-bitcoin-does-have-it/) or central issuer. In the first four years of Bitcoin's development, this first aspect of Bitcoin has taken up the bulk of the public attention, both in terms of the political aspects of a currency without a central bank and its extreme upward and downward volatility in price. However, there is also another, equally important, part to Satoshi's grand experiment: the concept of a proof of work-based blockchain to allow for public agreement on the order of transactions. Bitcoin belongs to a class of applications that can be described as a first-to-file system: if one entity has 50 BTC, and sends those 50 BTC to A and then sends the same 50 BTC to B, only the party whose transaction was confirmed first will get the bitcoins. There is no intrinsic way of looking at two transactions and figuring out which came earlier, and for several decades this stymied the development of decentralized digital currency. Satoshi's blockchain created the first credible decentralized solution. And now, more recently, attention is rapidly starting to shift toward this second part of Bitcoin's technology, and how the blockchain concept can be used for more than just money.

Commonly cited applications include using on-blockchain digital assets to represent custom currencies and financial instruments (["colored coins"](https://docs.google.com/a/buterin.com/document/d/1AnkP_cVZTCMLIzw4DvsW6M8Q2JC0lIzrTLuoWu2z1BE/edit)), ["smart property"](https://en.bitcoin.it/wiki/Smart_Property) devices such as cars which track a colored coin on a blockchain to determine their present legitimate owner, the resigtration and management of non-fungible assets such as domain names ("Namecoin") as well as more advanced applications such as decentralized exchange, financial derivatives, peer-to-peer gambling and on-blockchain identity and reputation systems. Another important area of inquiry is "smart contracts" - systems which automatically move digital assets according to arbitrary pre-specified rules. For example, one might have a treasury contract of the form "A can withdraw up to X currency units per day, B can withdraw up to Y per day, A and B together can withdraw anything, and A can shut off B's ability to withdraw". The logical extension of this is [decentralized autonomous organizations](http://bitcoinmagazine.com/7050/bootstrapping-a-decentralized-autonomous-corporation-part-i/) (DAOs) - long-term smart contracts that encode the bylaws of an entire organization. What Ethereum intends to provide is a blockchain with a built-in fully fledged Turing-complete programming language that can be used to create "contracts" that can be used to encode arbitrary state transition functions, allowing users to create any of the systems described above, as well as many others that we have not yet imagined, simply by writing up the logic in a few lines of code.

### Table of Contents

* [History](#history)
    * [Namecoin](#namecoin)
    * [Colored Coins](#colored-coins)
    * [Metacoins](#metacoins)
* [Philosophy](#philosophy)
* [Basic Building Blocks](#basic-building-blocks)
    * [Modified GHOST Implementation](#modified-ghost-implementation)
    * [Ethereum Client P2P Protocol](#ethereum-client-p2p-protocol)
    * [Currency and Issuance](#currency-and-issuance)
    * [Data Format](#data-format)
    * [Mining Algorithm](#mining-algorithm)
    * [Transactions](#transactions)
    * [Difficulty Adjustment](#difficulty-adjustment)
    * [Block Rewards and Limits](#block-rewards-and-limits)
* [Contracts](#contracts)
    * [Applications](#applications)
    * [Sub-currencies](#sub-currencies)
    * [Financial derivatives](#financial-derivatives)
    * [Identity and Reputation Systems](#identity-and-reputation-systems)
    * [Decentralized File Storage](#decentralized-file-storage)
    * [Decentralized Autonomous Organizations](#decentralized-autonomous-organizations)
    * [Further Applications](#further-applications)
    * [How Do Contracts Work?](#how-do-contracts-work)
    * [Language Specification](#language-specification)
* [Fees](#fees)
* [Conclusion](#conclusion)
* [References and Further Reading](#references-and-further-reading)

## Introduction to Bitcoin and Cryptocurrency 2.0

The concept of decentralized digital currency, as well as alternative applications like property registries, has been around for decades. The anonymous e-cash protocols of the 1980s and the 1990s, mostly reliant on a cryptographic primitive known as Chaumian blinding, provide a currency with a high degree of privacy, including from the "central bank", but the protocols largely failed to gain traction because of their reliance on a centralized intermediary. In 1998, Wei Dai's [b-money](http://www.weidai.com/bmoney.txt) became the first proposal to introduce the idea of creating money through solving computational puzzles as well as decentralized consensus, but the proposal was scant on details on how decentralized consensus could actually be implemented. In 2005, Hal Finney introduced a concept of "[reusable proofs of work](http://www.finney.org/~hal/rpow/)", a system which uses ideas from b-money together with Adam Back's computationally difficult Hashcash puzzles to create a concept for a cryptocurrency, but fell short of the ideal by relying on trusted computing as a backend.

Decentralized consensus is necessary because a digital currency is inherently a first-to-file system: if one entity has 50 BTC, and sends those 50 BTC to A and then sends the same 50 BTC to B, only the party whose transaction was confirmed first will get the bitcoins. There is no intrinsic way of looking at two transactions and figuring out which came earlier; the only possible solution is for the users of the system to provide the information, using a decentralized consensus algorithm to allow the network to come to an agreement on the result. The main roadblock that all of these protocols faced is the fact that, while there had been plenty of research on creating secure Byzantine-fault-tolerant multiparty consensus systems for many years, all of the protocols described were solving only half of the problem. The protocols assumed that all participants in the system were known, and produced security margins of the form "if N parties participate, then the system can tolerate up to N/4 malicious actors". The problem is, however, that in an anonymous setting such security margins are vulnerable to sybil attacks, where an attacker turns on a botnet and overwhelms the network with a vast quantity of nodes even to the point that the legitimate network falls below the security margin required for it to disrupt the false consensus.

The innovation provided by Satoshi is the idea of combining a very simple decentralized consensus protocol based on a blockchain, where a random node combines transactions into a "block" every ten minutes creating an ever-growing blockchain, with proof of work as a mechanism through which nodes gain the right to become one of the nodes in the system. While nodes with a large amount of computational power can create multiple accounts (indeed, the Bitcoin system does not even try to stop powerful nodes from gaining proportionately large influence), coming up with more computational power than the entire network combined is much harder than coming up with a million nodes. Even though Bitcoin's fault-tolerant consensus model is crude, and has weaker properties than some of the other more advanced protocols that have been devised, it has proven to be good enough, and the simplicity of the model together with Satoshi's realization that an anti-sybil mechanism was the missing piece, was the ultimate reason for its success.

### Bitcoin As A State Transition System

![statetransition.png](statetransition.png)

From a technical standpoint, the Bitcoin ledger can be thought of as a state transition system, where there is a "state" consisting of the ownership status of all existing bitcoins and a "state transition function" that takes a state and an object called a "transaction" and outputs a new state consisting of the old state plus the modifications that the transaction intends to carry out, if valid. In a standard banking system, for example, the state is a balance sheet, transaction is a request to move $X from A to B, and the state transition function reduces the value in A's account by $X and increases the value in B's account by $X. If A's account has less than $X in the first place, the state transition function returns an error. Hence, one can formally define:

    APPLY(S,TX) -> S' or ERROR

In the banking system defined above:

    APPLY({ Alice: $50, Bob: $50 },"send $20 from Alice to Bob") = { Alice: $30, Bob: $70 }
    
    APPLY({ Alice: $50, Bob: $50 },"send $70 from Alice to Bob") = ERROR

The "state" in Bitcoin is the collection of all coins (technically, "unspent transaction outputs" or UTXO) that have been minted and not yet spent, along with the denomination and owner (defined by a 20-byte address which is essentially a cryptographic public key<sup>[1]</sup>) for each UTXO. A transaction contains one or more inputs, with each input containing a reference to an existing UTXO and a cryptographic signature produced by the private key associated with the owner's address, and one or more outputs, with each output containing a new UTXO.

The state transition function `APPLY(S,TX) -> S'` can be defined roughly as follows:

1. For each input in `TX`:
    * If the referenced UTXO is not in `S`, return an error.
    * If the provided signature is invalid, return an error.
2. If the sum of the denominations of all input UTXO is less than the sum of the denominations of all output UTXO, return an error.
3. Return `S` with all input UTXO removed and all output UTXO added.

The first half of the first step prevents transaction senders from spending coins that do not exist, the second half of the first step prevents transaction senders from spending other people's coins, and the second step enforces conservation of value. In order to use this for payment, the protocol is as follows. Suppose Alice wants to send 11.7 BTC to Bob. First, Alice will look for a set of UTXO in the state that she owns that totals up to at least 11.7 BTC. Realistically, Alice will not be able to get exactly 11.7 BTC; say that the smallest she can get is 6+4+2=12. She then creates a transaction with those three inputs and two outputs. The first output will be 11.7 BTC with Bob's address as its owner; the second output will be the remaining 0.3 BTC "change", with the owner being Alice herself.

### Mining

If we had access to a trustworthy centralized service, this system would be trivial to implement; it could simply be coded in a bank with an API. However, with Bitcoin we are trying to build a decentralized currency system, so we will need to combine the state transaction system with a consensus system in order to ensure that everyone agrees on the order of transactions. Bitcoin's decentralized consensus process requires nodes in the network to continuously attempt to produce packages of transactions called "blocks". The network is intended to produce roughly one block every ten minutes, with each block containing a timestamp, a nonce, a reference to (ie. hash of) the previous block and a list of all of the transactions that have taken place since the previous block. Over time, this creates a persistent, ever-growing, "blockchain" that constantly updates to represent the latest state of the Bitcoin ledger.

The algorithm for checking if a block is valid, expressed in this paradigm, is as follows:

1. Check if the previous block referenced by the block exists and is valid.
2. Check that the timestamp of the block is greater than that of the previous block<sup>[2]</sup> and less than 2 hours into the future
3. Check that the proof of work on the block is valid.
4. Let `S0` be the state at the end of the previous block. Initialize `S = S0`.
5. For every transaction `TX` in the block's transaction list, set `S = APPLY(S,TX)`. If this returned an error, exit and return false.
6. Return true, and register `S` as the state at the end of this block.

Essentially, each transaction in the block must provide a state transition that is valid. Note that the state is not encoded in the block in any way; it is purely an abstraction to be remembered by the validating node and can only be (securely) computed for any block by starting from the genesis state and sequentially applying every transaction in every block.

The interesting part of the block validation algorithm is actually the proof of work checker: the condition is that the SHA256 hash of every block, treated as a 256-bit number, must be less than a dynamically adjusted target, which is currently sitting around 2<sup>192</sup>. The purpose of this is to make block creation computationally "hard", both ensuring that blocks get created at a reasonable and constant rate and preventing sybil attackers from remaking the entire blockchain in their favor. Because SHA256 is designed to be a completely unpredictable pseudorandom function, the only way to create a valid block is simply trial and error, repeatedly incrementing the nonce and seeing if the new hash matches. At the current target, as of April 2014, of roughly 2<sup>192</sup>, this means an average of 2<sup>64</sup> tries; in general, the target is recalibrated by the network every 2016 blocks so that on average one node in the network produces a new block every ten minutes. In order to compensate miners for this computational work, if a miner produces a valid block the network entitles them to include a transaction giving themselves 25 BTC out of nowhere, a reward that is scheduled to decrease over time. If any transaction has a higher total denomination in its inputs than in its outputs, the difference also goes to the miner as a "transaction fee". Incidentally, this is also the only mechanism by which BTC are issued; the genesis state contained no coins at all.

In order to better understand the purpose of mining, let us examine what happens in the event of a malicious attacker. Since Bitcoin's underlying cryptography is known to be secure, the attacker will target the one part of the Bitcoin system that is not protected by cryptography directly: the order of transactions. The attacker's strategy is simple:

1. Send 100 BTC to a merchant in exchange for some product (preferably a rapid-delivery digital good)
2. Wait for the delivery of the product
3. Produce another transaction sending the same 100 BTC to himself
4. Try to convince the network that his transaction to himself was the one that came first.

Once step (1) has taken place, after a few minutes some miner will include the transaction in a block, say block number 270000. After about one hour, five more blocks will have been created after that block, meaning that there will have been a total of six blocks' worth of proof of work computation directly or indirectly referencing that transaction ("six confirmations"). At this point, the merchant will accept the payment as finalized and deliver the product; since we are assuming this is a digital good, delivery is instant. Now, the attacker creates another transaction sending the 100 BTC to himself. If the attacker simply releases it into the wild, the transaction will not be processed; miners will attempt to run `APPLY(S,TX)` and notice that `TX` consumes a UTXO which is no longer in the state. So instead, the attacker creates his own blockchain, starting by mining another version of block 270000 pointing to the same block 269999 as a parent and containing the same transactions except swapping in his new transaction in place of the old one. Because the block data is different, this requires redoing the proof of work. Furthermore, the attacker's new version of block 270000 has a different hash, so the original blocks 270001 to 270005 are not valid descendants of it; thus, there are now two separate blockchains in the wild, one at block 270005 and the other at block 270000. The rule is that the longest blockchain is taken to be the truth, and miners workin on the longest chain by default. At this point, all legitimate miners work to lengthen the 270005 chain, and the attacker alone is lengthening the 270000 chain. In order for the attacker to catch up, he would need to have more computational power than the rest of the network combined (hence, "51% attack").

### Merkle Trees

![SPV in bitcoin](https://www.ethereum.org/gh_wiki/spv_bitcoin.png)

_Left: it suffices to present only a small number of nodes in a Merkle tree to give a proof of the validity of a branch._

_Right: any attempt to change any part of the Merkle tree will eventually lead to an inconsistency somewhere up the chain._

An important scalability feature of Bitcoin is that the block is stored in a multi-level data structure. The "hash" of a block is actually only the hash of the block header, a roughly 200-byte piece of data that contains the timestamp, nonce, previous block hash and the root hash of a data structure called the Merkle tree storing all transactions in the block. A Merkle tree is a type of binary tree, composed of a set of nodes with a large number of leaf nodes containing the underlying data, a set of intermediate nodes where each node is the hash of its two children, and finally a single root node, also formed from the hash of its two children, representing the "top" of the tree. The purpose of the Merkle tree is to allow the data in a block to be delivered piecemeal: a node can download only the header of a block from one source, the small part of the tree relevant to them from another source, and still be assured that all of the data is correct. The reason why this works is that hashes propagate upward: if a malicious user attempts to swap in a fake transaction into the bottom of a Merkle tree, this change will cause a change in the node above, and then a change in the node above that, finally changing the root of the tree and therefore the hash of the block, causing the protocol to register it as a completely different block (almost certainly with an invalid proof of work).

The Merkle tree protocol is arguably essential to long-term sustainability. A "full node" in the Bitcoin network, one that stores and processes the entirety of every block, takes up about 15 GB of disk space in the Bitcoin network as of April 2013, and is growing by over a gigabyte per month. Currently, this is viable for some desktop computers and not phones, and later on in the future only businesses and hobbyists will be able to participate. The SPV protocol allows for another class of nodes to exist, called "light nodes", which download only the block headers, verify the proof of work on the block headers, and then download only the "branches" associated with transactions that are relevant to them. This allows light nodes to determine with a strong guarantee of security what the status of any Bitcoin transaction, and their current balance, is.

### Namecoin

Roughly a year after Bitcoin's decentralized consensus protocol was invented, the first application came along which tried to use it for something that is not related to money: a distributed [domain name system](http://en.wikipedia.org/wiki/Domain_Name_System) (DNS), or a distributed phonebook for public keys in general. The problem that such systems are trying to solve is an issue that is known as [Zooko's triangle](http://en.wikipedia.org/wiki/Zooko's_triangle). Ideally, names should have three properties:

* **Security** - if one person is using a particular name, another person should not be able to use the same name
* **Decentralization** - distribution of names should not be controlled by some single gatekeeper
* **Human-memorable** - names should be sufficietly short as to be easily memorized in large quantities

However, all systems so far have only succeeded at achieving a maximum of two of these qualities. For example:

* Human names are decentralized, since everyone issues their own (or more precisely their children's), and human-memorable, since it is quite easy to remember Bob, George, Fred, etc. However, they are not secure - anyone can change their name to Bill Gates and start speaking out against Microsoft on television.
* Domain names like google.com are secure, since I cannot easily make a server that pretends to be google.com, and they are human-memorable, but they are not decentralized - a set of agencies governed by ICANN hands them out.
* Bitcoin addresses are secure, since it's computationally infeasible to generate a private key that maps to a given pre-specified Bitcoin address and steal its funds, and they are decentralized, since everyone generates their own, but they are not human-memorable - good luck memorizing `1PsFQAkQ5jkN35cVhG4SE1uxAfzNB65S3M`.
* BitMessage addresses are generated like Bitcoin addresses, but have the same properties.
* Tor onion addresses are secure, decentralized, but also not memorable.
* Email addresses are secure and memorable, but centralized.
* Phone numbers are secure, centralized, and only partially human-memorable.

Namecoin is the first system that arguably broke the triangle, having all three properties. Normally, having all three properties runs into a paradox. If names are short enough to be memorable, then if someone registered themselves "george" then you can go through the same process to register yourself as "george" as well. Determining which of those two registrations is legitimate would thus need to be a decentralized first-to-file system, where the first registerer succeeds and the second fails. Fortunately, it just so happens that this is precisely the same problem that was faced, and solved, by Bitcoin as well. Right now, Namecoin has not had as much success in the domain registration sphere, but there are a number of projects trying to integrate the technology for other applications including name registration for decentralized messaging applications.

### Colored Coins

The first attempt to implement a system for managing smart property and custom currencies and assets on top of a blockchain was built as a sort of overlay protocol on top of Bitcoin, with many advocates making a comparison to the way that, in the [internet protocol stack](http://en.wikipedia.org/wiki/Internet_protocol_suite), [HTTP](http://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol) serves as a layer on top of [TCP](http://en.wikipedia.org/wiki/Transmission_Control_Protocol). The [colored coins](https://docs.google.com/a/ursium.com/document/d/1AnkP_cVZTCMLIzw4DvsW6M8Q2JC0lIzrTLuoWu2z1BE/edit) protocol is roughly defined as follows:

1. A colored coin issuer determines that a given transaction output H:i (H being the transaction hash and `i` the output index) represents a certain asset, and publishes a "color definition" specifying this transaction output alongside what it represents (eg. 1 satoshi from `H:i` = 1 ounce of gold redeemable at Stephen's Gold Company)
2. Others "install" the color definition file in their colored coin clients.
3. When the color is first released, output H:i is the only transaction output to have that color.
4. If a transaction spends inputs with color X, then its outputs will also have color X. For example, if the owner of H:i immediately makes a transaction to split that output among five addresses, then those transaction outputs will all also have color X. If a transaction has inputs of different colors, then a "color transfer rule" or "color kernel" determines which colors which outputs are (eg. a very naive implementation may say that output 0 has the same color as input 0, output 1 the same color as input 1, etc).
5. When a colored coin client notices that it received a new transaction output, it uses a back-tracing algorithm based on the color kernel to determine the color of the output. Because the rule is deterministic, all clients will agree on what color (or colors) each output has.

However, the protocol has two major weaknesses:

1. **Difficulty of simplified payment verification** - with Bitcoin, it is possible for a light client to quickly determine the status of any UTXO via the simplified payment verification protocol.  With colored coins, however, this is much harder. The reason is that one cannot determine the color of a transaction output simply by looking up the Merkle tree; rather, one needs to employ the backward scanning algorithm, fetching potentially thousands of transactions and requesting a Merkle tree validity proof of each one, before a client can be fully satisfied that a transaction has a certain color. After over a year of investigation, including help from ourselves, no solution has been found to this problem.
2. **Incompatibility with scripting** - as will be further described below, Bitcoin does have a moderately flexible scripting system, for example allowing users to sign transactions of the form "I release this transaction output to anyone willing to pay to me 1 BTC". Other examples include [assurance contracts](http://en.wikipedia.org/wiki/Assurance_contract), [efficient micropayments](https://en.bitcoin.it/wiki/Contracts#Example_7:_Rapidly-adjusted_.28micro.29payments_to_a_pre-determined_party) and on-blockchain auctions. However, this system is inherently not color-aware; that is to say, one cannot make a transaction of the form "I release this transaction output to anyone willing to pay me one gold coin defined by the genesis H:i", because the scripting language has no idea that a concept of "colors" even exists. One major consequence of this is that, while trust-free swapping of two different colored coins is possible, a full decentralized exchange is not since there is no way to place an enforceable order to buy or sell.

### Metacoins

Another concept, once again in the spirit of sitting on top of Bitcoin much like HTTP over TCP, is that of "metacoins". The concept of a metacoin is simple: the metacoin protocol provides for a way of encoding metacoin transaction data into the outputs of a Bitcoin transaction, and a metacoin node works by processing all Bitcoin transactions and evaluating Bitcoin transactions that are valid metacoin transactions in order to determine the current account balances at any given time. For example, a simple metacoin protocol might require a transaction to have four outputs: MARKER, FROM, TO and VALUE. MARKER would be a specific marker address to identify a transaction as a metacoin transaction. FROM would be the address that coins are sent from. `TO` would be the address that coins are sent to, and VALUE would be an address encoding the amount sent. Because the Bitcoin protocol is not metacoin-aware, and thus will not reject invalid metacoin transactions, the metacoin protocol must treat all transactions with the first output going to MARKER as valid and react accordingly. For example, an implementation of the transaction processing part of the above described metacoin protocol might look like this:

    if tx.output[0] != MARKER:
        break
    else if balance[tx.output[1]] < decode_value(tx.output[3]):
        break
    else if not tx.hasSignature(tx.output[1]):
        break
    else:
        balance[tx.output[1]] -= decode_value(tx.output[3]);
        balance[tx.output[2]] += decode_value(tx.output[3]);


The advantage of a metacoin protocol is that the protocol can allow for more advanced transaction types, including custom currencies, decentralized exchange, derivatives, etc, that are impossible to implement using the underlying Bitcoin protocol by itself. However, metacoins on top of Bitcoin have one major flaw: simplified payment verification, already difficult with colored coins, is outright impossible on a metacoin. The reason is that while one can use SPV to determine that there is a transaction sending 30 metacoins to address X, that by itself does not mean that address X has 30 metacoins. What if the sender of the transaction did not have 30 metacoins to start with and so the transaction is invalid? Ultimately, finding out any part of the current state requires scanning through all transactions since the metacoin's original launch to figure out which transactions are valid and which ones are not. This makes it impossible to have a truly secure client without downloading the entire, arguably prohibitively large, Bitcoin blockchain.

The above discussion is not to say we believe colored coins and metacoins have no future in the cryptocurrency space; on the contrary, they will likely be very valuable tools specifically in the field of Bitcoin exchange. Currently, Bitcoin exchange is a three-step process: one deposits USD via bank transfer to convert them to exchange-USD, makes a trade on the exchange to convert exchange-USD into exchange-BTC, and then withdraw exchange-BTC for real BTC. In that process, exchange-USD and exchange-BTC are assets backed by the exchange, and there have been numerous cases where exchanges have lost the underlying funds especially on the BTC side. With colored coins, the process becomes two-step: one deposits USD via bank transfer to convert them to colored coin exchange-USD, and then converts that directly to BTC on the blockchain. The issue of BTC liabilities and safe BTC storage for the exchange disappears completely. However, the argument we are making is that Bitcoin-based protocols are simply not suited for making alternative blockchain applications in general; Bitcoin is much more analogous to SMTP, the special-purpose protocol for email, than it is to TCP.

### Scripting

Even with out any extensions, the Bitcoin protocol actually facilitates a weak version of a concept of "smart contracts" itself. UTXO in Bitcoin can be owned not just by a public key, but also by a more complicated script expressed in a simple stack-based programming language. A transaction spending that UTXO must then provide, rather than specifically an elliptic curve signature, data that satisfies the script. Indeed, even the basic public and private key ownership mechanism is implemented via a script: the script takes an elliptic curve signature as input, verifies it against the transaction and the key that owns the transaction, and returns 1 if the verification is successful and 0 otherwise. Other, more complicated, scripts exist for various additional use cases:

* **Multi-signature transactions**: a script can contain three public keys and accept an input if it contains valid signatures for at least two of those keys (2-of-3 is the most common configuration, other configurations such as 2-of-2, 3-of-5, 4-of-11, etc are also possible). This is very useful in multiparty treasury applications where having a very large amount of funds protected by only one private key is deemed too risky, as well as various types of escrow situations. A particularly interesting example is bets with an oracle: if A and B want to make a bet that P will be true, they can assign an oracle O to be the judge of P and create a 2-of-3 multisig between A, B and O. If A wins, A and O sign a transaction to withdraw the funds to A's address, and likewise for B. The advantage of this approach is that O cannot unilaterally disappear with the funds.
* **Computational bounties**: a script can accept an input if it contains a value that has certain mathematical properties. For example, one can conceivably make a UTXO that basically serves as an automatic bounty for finding a prime number p such that 2p-1, 4p-1 ... 2<sup>20</sup>p-1 are all also prime, or potentially even a bounty for cracking the password on a hard drive (as long as there is some compact mechanism for determining whether a given password is legitimate). 
* **Cross-chain trading**: in Bitcoin, it is possible to construct a compact (~500 bytes) independently verifiable cryptographic proof that a given transaction was included in a block, or even that a given transaction was included in a block with N confirmations, and the same is true for most other cryptocurrencies. Thus, one can make a flyweight decentralized exchange between, say, Bitcoin and Dogecoin, by making a Bitcoin UTXO that can be spent only by an input containing (1) a proof of a Dogecoin transaction of at least 10000 DOGE to a specific address A, and (2) a signature produced by the same private key that signed the Dogecoin transaction. This would allow anyone wishing to trade their DOGE for BTC to simply send their DOGE to A, and make a Bitcoin transaction containing a proof of the Dogecoin transaction to unlock the funds.

However, the scripting language as implemented in Bitcoin has several important limitations:

* **Lack of Turing-completeness** - that is to say, while there is a large subset of computation that the Bitcoin scripting language supports, it does not nearly support everything. The main category that is missing is loops. This is done to avoid infinite loops during transaction verification; theoretically it is a surmountable obstacle, since any loop can be simulated by simply repeating the underlying code many times with an if statement, but it does lead to scripts that are very space-inefficient. For example, implementing an alternative elliptic curve signature algorithm would likely require 256 repeated multiplication rounds all written in the code.
* **Value-blindness** - there is no way for a UTXO script to provide fine-grained control over the amount that can be withdrawn. For example, one powerful use case of an oracle contract would be a contract for difference, where A and B put in $1000 worth of BTC and after 30 days the script sends $1000 worth of BTC to A and the rest to B. This would require an oracle to determine the value of 1 BTC, but even then it is a massive improvement in terms of trust and infrastructure requirement over the fully centralized solutions that are available now. However, because UTXO are all-or-nothing, the only way to achieve this is through the very inefficient hack of having many UTXO of varying denominations (eg. one UTXO of 2<sup>k</sup> for every k up to 30) and having O pick which UTXO to send to A and which to B.
* **Lack of state** - UTXO can either be spent or unspent; there is no opportunity for multi-stage contracts or scripts which keep any other internal state beyond that. This makes it hard to make multi-stage options contracts, decentralized exchange offers or two-stage cryptographic commitment protocols (necessary for secure computational bounties). It also means that UTXO can only be used to build simple, one-off contracts and not more complex applications such as decentralized organizations or domain name registries, and makes attempts at creating "colored coins" or other alternative digital assets difficult to implement. Binary state combined with value-blindness also mean that another important application, withdrawal limits, is impossible.
* **Blockchain-blindness** - UTXO are blind to blockchain data such as the nonce and previous hash. This severely limits applications in gambling, and several other categories, by depriving the scripting language of a potentially valuable source of randomness.

Many of these issues arise from the fact that, while Bitcoin does introduce many layers of abstraction, it is arguably not abstract enough. Although the rules for when outputs can be spent are arbitrary, the rules for how new outputs can be created are not: the concept of a "denomination" and the rule that the outputs must have equal or lesser denomination compared to the inputs are enshrined in the core protocol. It is the purpose of the Ethereum protocol to take this one last fixed feature and uproot it, allowing for the creation of completely arbitrary state transition functions.

## Philosophy

The design behind Ethereum is intended to follow the following principles:

1. **Simplicity** - the Ethereum protocol should be as simple as possible, even at the cost of some data storage or time inefficiency. An average programmer should ideally be able to follow and implement the entire specification, so as to fully realize the unprecedented democratizing potential that cryptocurrency brings and further the vision of Ethereum as a protocol that is open to all. Any optimization which adds complexity should not be included unless that optimization provides very substantial benefit.
2. **Universality** - a fundamental part of Ethereum's design philosophy is that Ethereum does not have "features". Instead, Ethereum provides an internal Turing-complete scripting language, which a programmer can use to construct any smart contract or transaction type that can be mathematically defined. Want to invent your own financial derivative? With Ethereum, you can. Want to make your own currency? Set it up as an Ethereum contract. Want to set up a full-scale Daemon or Skynet? You may need to have a few thousand interlocking contracts, and be sure to feed them generously, to do that, but nothing is stopping you with Ethereum at your fingertips.
3. **Modularity** - the parts of the Ethereum protocol should be designed to be as modular and separable as possible. Over the course of development, our goal is to create a program where if one was to make a small protocol modification in one place, the application stack would continue to function without any further modification. Innovations such as [Dagger](https://github.com/ethereum/wiki/wiki/%5BEnglish%5D-Dagger), [Patricia trees](https://github.com/ethereum/wiki/wiki/%5BEnglish%5D-Patricia-Tree) and [RLP](https://github.com/ethereum/wiki/wiki/%5BEnglish%5D-RLP) should be implemented as separate libraries and made to be feature-complete even if Ethereum does not require certain features so as to make them usable in other protocols as well. Ethereum development should be maximally done so as to benefit the entire cryptocurrency ecosystem, not just itself.
4. **Agility** - details of the Ethereum protocol are not set in stone. Although we will be extremely judicious about making modifications to high-level constructs such as the [C-like language](https://github.com/ethereum/wiki/wiki/%5BEnglish%5D-CLL) and the address system, computational tests later on in the development process may lead us to discover that certain modifications to the algorithm or scripting language will substantially improve scalability or security. If any such opportunities are found, we will exploit them.
5. **Non-discrimination** - the protocol should not attempt to actively restrict or prevent specific categories of usage. All regulatory mechanisms in the protocol should be designed to directly regulate the harm and not attempt to oppose specific undesirable applications. A programmer can even run an infinite loop script on top of Ethereum for as long as they are willing to keep paying the per-computational-step transaction fee.

## Basic Building Blocks

### Ethereum State Transition Function

In Ethereum, the UTXO-based system used by Bitcoin is replaced by a more traditional account-based system, where there exist objects called "accounts", with state transitions being direct transfers of value between accounts. There are two types of accounts: externally owned accounts and contract accounts. An externally owned account, much like the default type of account in Bitcoin, is identified by a 20-byte address and requires a cryptographic signature produced with the associated private key to send transactions from it. A contract account is the more interesting type of account, and is where the true power of Ethereum lies. Every contract account contains a balance, a key/value store to provide long-term internal state, and a computer program written in Ethereum's internal scripting code. That computer program is activated by incoming transactions or messages, and has the right to read and write to its internal state and itself send messages to other accounts. "Messages" and "transactions" are distinct concepts in Ethereum: a message is an object created inside of the Ethereum state transition function that sends ether and data from one account to another, and a transaction is a package authorizing a message to be sent from an externally owned account.

![]()

A transaction in Ethereum contains the following values:

* `NONCE`, a counter used to make sure each transaction can only be processed once
* `TO`, the address of the destination account
* `VALUE`, the amount of ether to transfer
* `STARTGAS`, the amount of "gas" to allow for the computation. Gas is the fuel of the computational engine; every computational step taken and every byte added to the state or transaction list consumes some gas.
* `GASPRICE`, the amount of ether to pay as a transaction fee to the miner for each unit of gas
* `DATA`, a byte array that can contain any arbitrary data; usually, it is used as a series of 32-byte values.
* `V,R,S`, the three values that make up an elliptic curve signature (including public key recovery bits)

The Ethereum state transition function, `APPLY(S,TX) -> S'` can be defined as follows:

1. Check if the transaction is well-formed (ie. has the right number of values), the signature is valid, and the nonce matches the nonce in the sender's account. If not, return an error.
2. Calculate the transaction fee as `STARTGAS * GASPRICE`, and determine the sending address from the signature. Subtract the fee from the sender's account balance and increment the sender's account nonce. If there is not enough balance to spend, return an error. Otherwise, let `S'` be the state after this step (ie. after paying the fee but before doing anything else).
3. Initialize `GAS = STARTGAS`, and take off 5 gas per byte to pay for the bytes in the transaction.
4. Transfer the transaction value from the sender's account to the receiving account. If the receiving account does not yet exist, create it. If there is not enough value to send, revert to and return `S'`.
5. If the receiving account is a contract, run the contract's code either to completion or until the execution runs out of gas. If execution runs out of gas, revert to and return `S'`. If execution runs to completion with `G` gas remaining, refund `G * GASPRICE` to the sender's account, and return the final state.

For example, suppose that the contract's code is:

    if !contract.storage[msg.data[0]]:
        contract.storage[msg.data[0]] = msg.data[1]

Note that in reality the contract code is written in a low-level stack-based language, not easily human-readable pseudo-Python; the example here is given just for clarity. Suppose that a contract's storage starts off empty, and a transaction is sent with 10 ether value, 200 gas, 0.01 ether gasprice, and two data fields: `[ 2, 'CHARLIE' ]`<sup>[3]</sup>. The process for the state transition function in this case is as follows:

1. Check that the transaction is valid and well formed.
2. Check that the transaction sender has at least 2000 * 0.001 = 2 ether. If it is, then subtract 2 ether from the sender's account.
3. Initialize gas = 2000; assuming the transaction is 170 bytes long, subtract 850 so that there is 1150 gas left.
3. Subtract 10 more ether from the sender's account, and add it to the contract's account.
4. Run the code. Suppose this takes 187 gas.
5. Add 963 * 0.001 = 0.963 ether to the sender's account, and return the resulting state.

### Code Execution

An Ethereum account contains four fields:

* The nonce, a counter used to make sure each transaction can only be processed once
* The account's current ether holdings
* The account's contract code, if present
* The account's storage (empty by default)

The code in Ethereum contracts is written in a low-level, stack-based bytecode language. The core consists of a series of bytes, where each byte represents an operation. In general, code execution is an infinite loop that consists of repeatedly carrying out the operation at the current program counter (which begins at zero) and then incrementing the program counter by one, until the end of the code is reached or an error or STOP or RETURN instruction is detected. The operations have the right to access and modify three types of memory:

* The **stack**, a container to which the allowed operations are pushing an element (32 bytes) to the top and popping an element off the top.
* **Memory**, an infinitely expandable byte array
* The contract's **long-term storage**, a key/value store where keys and values are both 32 bytes.

The stack and memory are reset as soon as computation ends; the storage, on the other hand, persists for the long term. The code can also access all of the details about the incoming message, including the "data" field that is supposed to serve as the primary input, and the code can also provide a byte array of data as an output. Block header data is also available. The most interesting part, however, is that the contract system is recursive: contracts themselves are allowed to send messages to other contracts, triggering the execution of those contracts. This has the important consequence that contracts are "first class citizens" - that is, contract accounts can do everything that normal accounts can. This also allows contracts to be chained together; for example, one might have a member of a decentralized organization (a contract) be an escrow account (another contract) between an paranoid individual employing custom quantum-proof Lamport signatures (a third contract) and a co-signing entity which itself uses an account with five keys for security (a fourth contract). The decentralized organization and the escrow contract do not need to care about what kind of account each party to the contract is; all that matters is that they provide a consistent interface.

### Computation and Turing-Completeness

An important node is that the EVM is Turing-complete; this means that EVM code can encode any computation that can be conceivably carried out, including infinite loops. EVM code allows looping in two ways. First, there is a `JUMP` instruction that allows the program to jump back to a previous spot in the code, and a `JUMPI` instruction to do conditional jumping, allowing for statements like `while x < 27: x = x * 2`. Second, contracts can call other contracts, potentially allowing for looping through recursion. This naturally leads to a problem: can malicious users essentially shut miners and full nodes down by forcing them to enter into an infinite loop? The issue arises because of a problem in computer science known as the halting problem: there is no way to tell, in the general case, whether or not a given program will ever halt.

To show the motivation behind our solution, consider the following examples:

* An attacker creates a contract which runs an infinite loop, and then sends a transaction activating that loop to the miner. The miner will process the transaction, running the infinite loop, and wait for it to run out of gas. Even though the execution runs out of gas and stops halfway through, the transaction is still valid and the miner still claims the fee from the attacker for each computational step.
* An attacker sees a contract with code of some form like `send(A,contract.storage[A]); contract.storage[A] = 0`, and sends a transaction with just enough gas to run the first step but not the second (ie. making a withdrawal but not letting the balance go down). The contract author does not need to worry about protecting against such attacks, because if execution stops halfway through the changes get reverted.
* An attacker creates a very long infinite loop with the intent of forcing the miner to keep computing for such a long time that by the time computation finishes a few more blocks will have come out and it will not be possible for the miner to include the transaction to claim the fee. However, the attacker will be required to submit a value for `STARTGAS` limiting the number of computational steps that execution can take, so the miner will know ahead of time that the computation will take an excessively large number of steps.

The alternative to Turing-completeness is Turing-incompleteness, where `JUMP` and `JUMPI` do not exist and only one copy of each contract is allowed to exist in the call stack at any given time. With this system, the fee system described and the uncertainties around the effectiveness of our solution might not be necessary, as the cost of executing a contract would be bounded above by its size. Additionally, Turing-incompleteness is not even that big a limitation; out of all the contract examples we have conceived interally, so far only one required a loop, and even that loop could be removed by making 26 repetitions of a one-line piece of code. Given the serious implications of Turing-completeness, and the limited benefit, why not simple have a Turing-incomplete language? In reality, however, Turing-incompleteness is far from a neat solution to the problem. To see why, consider the following contracts:

    C0: call(C1); call(C1);
    C1: call(C2); call(C2);
    C2: call(C3); call(C3);
    ...
    C49: call(C50); call(C50);
    C50: contract.storage[0] = contract.storage[0] + 1

Now, send a transactin to A. Thus, in 51 transactions, we have a contract that takes up 2<sup>50</sup> computational steps. Miners could try to detect such logic bombs ahead of time by maintaining a value alongside each contract specifying the maximum number of computational steps that it can take, and calculating this for contracts calling other contracts recursively, but that would require miners to forbid contracts that create other contracts (since the creation and execution of all 26 contracts above could easily be rolled into a single contract).Another problematic point is that the address field of a message is a variable, so in general it may not even be possible to tell which other contracts a given contract will call ahead of time. Hence, all in all, we have a surprising conclusion: Turing-completeness is surprisingly manageable, and the lack of Turing-completeness is equally surprisingly not - unless the same controls are in place, but in that case why not just bring back the loops?

### Blockchain and Mining

The Ethereum blockchain is in many ways similar to the Bitcoin blockchain, although it does have some differences. The main difference between Ethereum and Bitcoin with regard to the blockchain architecture is that, unlike Bitcoin, Ethereum blocks contain a copy of both the transaction list and the most recent state. Aside from that, two other values, the block number and the difficulty, are also stored in the block. Thus, altogether, the block header contains the following data, given in order:

* `PREVHASH`, the hash of the previous block
* `NUMBER`, the number of the block (0 for the genesis block, `n+1` for another block where `n` is the number of the previous block)
* `COINBASE`, the account of the miner
* `STATE_ROOT`, the "root" of the state tree (a Merkle tree-like structure storing the state)
* `TRANSACTION_ROOT`, the "root" of the transaction tree
* `UNCLE_ROOT`, the "root" of the uncle tree
* `DIFFICULTY`, the parameter determining the difficulty of mining
* `GASLIMIT`, a protocol-determined limit to the amount of gas that can be used in the block
* `TIMESTAMP`, the Unix time when the block was created
* `EXTRA_DATA`, an optional data field
* `NONCE`, a value that can be changed at will for mining purposes

The block validation algorithm in Ethereum is as follows:

1. Check if the previous block referenced exists and is valid.
2. Check that the timestamp of the block is greater than that of the referenced previous block and less than 15 minutes into the future
3. Check that the block number, difficulty, transaction root, uncle root and gas limit are valid.
4. Check that the proof of work on the block is valid.
5. Let `S` be the `STATE_ROOT` of the previous block.
6. For every transaction `TX` in the block's transaction list:
    * Let `S' = APPLY(S,TX)`
    * If the above step returned an error, or if the total gas consumed in the block up until this point exceeds the `GASLIMIT`, return an error. Otherwise, set `S = S'`
7. Pay the block reward to the miner.
8. Check if `S` is the same as the `STATE_ROOT`. If it is, the block is valid; otherwise, it is not valid.

The approach may seem highly inefficient at first glance, because it needs to store the entire state with each block, but in reality efficiency should be comparable to that of Bitcoin. The reason is that the state is stored in the tree structure, and after every block only a small part of the tree needs to be changed. Thus, in general, between two adjacent blocks the vast majority of the tree should be the same, and therefore the data can be stored once and referenced twice using pointers (ie. hashes of subtrees). A special kind of tree known as a "Patricia tree" is used to accomplish this, including a modification to the Merkle tree concept that allows for nodes to be inserted and deleted, and not just changed, efficiently. 

### Currency and Issuance

The Ethereum network includes its own built-in currency, ether. The main reason for including a currency in the network is twofold. First, like Bitcoin, ether is rewarded to miners so as to incentivize network security. Second, it serves as a mechanism for paying transaction fees for anti-spam purposes. Of the two main alternatives to fees, per-transaction proof of work similar to [Hashcash](http://en.wikipedia.org/wiki/Hashcash) and zero-fee laissez-faire, the former is wasteful of resources and unfairly punitive against weak computers and smartphones and the latter would lead to the network being almost immediately overwhelmed by an infinitely looping "logic bomb" contract. For convenience and to avoid future argument (see the current mBTC/uBTC/satoshi debate in Bitcoin), the denominations will be pre-labelled:

* 1: wei
* 10<sup>3</sup>: lovelace
* 10<sup>6</sup>: babbage
* 10<sup>9</sup>: shannon
* 10<sup>12</sup>: szabo
* 10<sup>15</sup>: finney
* 10<sup>18</sup>: ether

This should be taken as an expanded version of the concept of "dollars" and "cents" or "BTC" and "satoshi" that is intended to be future proof. In the near future, we expect "ether" to be the primary unit in the system, much like the dollar or bitcoin; "finney" will likely be used for microtransactions, and "szabo" and "wei" specifically in technical discussions around fees and protocol implementation, much like the satoshi in Bitcoin. The remaining denominations will likely only come into play if Ethereum grows much larger or computers get much more efficient and it makes sense to use lower units than szabo to measure fees.

The issuance model will be as follows:

* Ether will be released in a currency sale at the price of 1000-2000 ether per BTC, a mechanism intended to fund the Ethereum organization and pay for development that has been used with success by other platforms such as Mastercoin and NXT. Earlier buyers will benefit from larger discounts.
* 0.3X ether will be allocated to miners per year forever after that point
* 0.4X ether will be allocated to the organization as a reserve

| Group  | After 1 year | After 5 years
| ------------- | ------------- |-------------|
| Currency units  | 1.7X  |  2.9X |
| Purchasers  | 58.8%  | 34.4% |
| Reserve | 29.4% | 17.2% |
| Miners | 17.6% | 51.7% |

**Long-Term Supply Growth Rate (percent)**

![SPV in bitcoin](https://www.ethereum.org/gh_wiki/inflation.svg)

_Despite the linear currency issuance, just like with Bitcoin over time the supply growth rate nevertheless tends to zero_

For example, after five years and assuming no transactions, 38.4% of the ether will be in the hands of participants in the original purchase, 57.8% would be in the hands of miners, and 3.84% for the organization as a reserve. The permanent linear supply growth model reduces the risk of what some see as excessive wealth concentration in Bitcoin, and gives individuals living in present and future eras a fair chance to acquire currency units, while at the same time retaining a strong incentive to obtain and hold ether because the "supply growth rate" still tends to zero over time (eg. during year 1000001 the money supply would increase from 500001.5 * X to 500002 * X, a rate of 0.0001%). Furthermore, much of the interest in Ethereum will be medium-term; we predict that if Ethereum succeeds it will see the bulk of its growth on a 1-20 year timescale, and supply during that period will be very much limited.

We also theorize that because coins are always lost over time due to carelessness, death, etc, and coin loss can be modeled as a percentage of the total supply per year, that the total currency supply in circulation will in fact eventually stabilize at a value equal to the annual issuance divided by the loss rate (eg. at a loss rate of 1%, once the supply reaches 30X then 0.3X will be mined and 0.3X lost every year, creating an equilibrium).


## References and Further Reading

1. A sophisticated reader may notice that in fact a Bitcoin address is the hash of the elliptic curve public key, and not the public key itself. However, it is in fact perfectly legitimate cryptographic terminology to refer to the pubkey hash as a public key itself. This is because Bitcoin's cryptography can be considered to be a custom digital signature algorithm, where the public key consists of the hash of the ECC pubkey, the signature consists of the ECC pubkey concatenated with the ECC signature, and the verification algorithm involves checking the ECC pubkey in the signature against the ECC pubkey hash provided as a public key and then verifying the ECC signature against the ECC pubkey.
2. Technically, the median of the 11 previous blocks.
3. Internally, 2 and "CHARLIE" are both numbers, with the latter being in big-endian base 256 representation. Numbers can be at least 0 and at most 2<sup>256<sup>-1.
4. David Friedman on enforcement: http://www.daviddfriedman.com/Academic/Course_Pages/legal_systems_very_different_12/Book_Draft/Threads/EnforcingRules.html
1. Colored coins whitepaper: https://docs.google.com/a/buterin.com/document/d/1AnkP_cVZTCMLIzw4DvsW6M8Q2JC0lIzrTLuoWu2z1BE/edit
2. Mastercoin whitepaper: https://github.com/mastercoin-MSC/spec
3. Decentralized autonomous corporations, Bitcoin Magazine: http://bitcoinmagazine.com/7050/bootstrapping-a-decentralized-autonomous-corporation-part-i/
4. Smart property: https://en.bitcoin.it/wiki/Smart_Property
5. Smart contracts: https://en.bitcoin.it/wiki/Contracts
6. Simplified payment verification: https://en.bitcoin.it/wiki/Scalability#Simplifiedpaymentverification
7. Merkle trees: http://en.wikipedia.org/wiki/Merkle_tree
8. Patricia trees: http://en.wikipedia.org/wiki/Patricia_tree
9. Bitcoin whitepaper: http://bitcoin.org/bitcoin.pdf
10. GHOST: http://www.cs.huji.ac.il/~avivz/pubs/13/btc_scalability_full.pdf
11. StorJ and Autonomous Agents, Jeff Garzik: http://garzikrants.blogspot.ca/2013/01/storj-and-bitcoin-autonomous-agents.html
12. Mike Hearn on Smart Property at Turing Festival: http://www.youtube.com/watch?v=Pu4PAMFPo5Y
13. Ethereum RLP: https://github.com/ethereum/wiki/wiki/%5BEnglish%5D-RLP
14. Ethereum Merkle Patricia trees: https://github.com/ethereum/wiki/wiki/%5BEnglish%5D-Patricia-Tree
15. Ethereum Dagger: https://github.com/ethereum/wiki/wiki/%5BEnglish%5D-Dagger
16. Ethereum C-like language: https://github.com/ethereum/wiki/wiki/%5BEnglish%5D-CLL
17. Ethereum Slasher: http://blog.ethereum.org/?p=39/slasher-a-punitive-proof-of-stake-algorithm
18. Scrypt parameters: https://litecoin.info/User:Iddo/ComparisonbetweenLitecoinandBitcoin#SHA256miningvsscryptmining
19. Litecoin ASICs: https://axablends.com/merchants-accepting-bitcoin/litecoin-discussion/litecoin-scrypt-asic-miners/
20. Intrinsic value: http://bitcoinmagazine.com/8640/an-exploration-of-intrinsic-value-what-it-is-why-bitcoin-doesnt-have-it-and-why-bitcoin-does-have-it/
21. B-money: http://www.weidai.com/bmoney.txt
21. Reusable proofs of work: http://www.finney.org/~hal/rpow/ 
22. Secure property titles with owner authority: http://szabo.best.vwh.net/securetitle.html
23. Zooko's triangle: http://en.wikipedia.org/wiki/Zooko's_triangle
