WORK IN PROGRESS!!!

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

## Introduction to Bitcoin and Existing Concepts

### History

The concept of decentralized digital currency, as well as alternative applications like property registries, has been around for decades. The anonymous e-cash protocols of the 1980s and the 1990s, mostly reliant on a cryptographic primitive known as Chaumian blinding, provide a currency with a high degree of privacy, including from the "central bank", but the protocols largely failed to gain traction because of their reliance on a centralized intermediary. In 1998, Wei Dai's [b-money](http://www.weidai.com/bmoney.txt) became the first proposal to introduce the idea of creating money through solving computational puzzles as well as decentralized consensus, but the proposal was scant on details on how decentralized consensus could actually be implemented. In 2005, Hal Finney introduced a concept of "[reusable proofs of work](http://www.finney.org/~hal/rpow/)", a system which uses ideas from b-money together with Adam Back's computationally difficult Hashcash puzzles to create a concept for a cryptocurrency, but fell short of the ideal by relying on trusted computing as a backend.

Decentralized consensus is necessary because a digital currency is inherently a first-to-file system: if one entity has 50 BTC, and sends those 50 BTC to A and then sends the same 50 BTC to B, only the party whose transaction was confirmed first will get the bitcoins. There is no intrinsic way of looking at two transactions and figuring out which came earlier; the only possible solution is for the users of the system to provide the information, using a decentralized consensus algorithm to allow the network to come to an agreement on the result. The main roadblock that all of these protocols faced is the fact that, while there had been plenty of research on creating secure Byzantine-fault-tolerant multiparty consensus systems for many years, all of the protocols described were solving only half of the problem. The protocols assumed that all participants in the system were known, and produced security margins of the form "if N parties participate, then the system can tolerate up to N/4 malicious actors". The problem is, however, that in an anonymous setting such security margins are vulnerable to sybil attacks, where an attacker turns on a botnet and overwhelms the network with a vast quantity of nodes even to the point that the legitimate network falls below the security margin required for it to disrupt the false consensus.

The innovation provided by Satoshi is the idea of combining a very simple decentralized consensus protocol based on a blockchain, where a random node combines transactions into a "block" every ten minutes creating an ever-growing blockchain, with proof of work as a mechanism through which nodes gain the right to become one of the nodes in the system. While nodes with a large amount of computational power can create multiple accounts (indeed, the Bitcoin system does not even try to stop powerful nodes from gaining proportionately large influence), coming up with more computational power than the entire network combined is much harder than coming up with a million nodes. Even though Bitcoin's fault-tolerant consensus model is crude, and has weaker properties than some of the other more advanced protocols that have been devised, it has proven to be good enough, and the simplicity of the model together with Satoshi's realization that an anti-sybil mechanism was the missing piece, was the ultimate reason for its success.

### Bitcoin As A State Transition System

![statetransition.png](statetransition.png)

From a technical standpoint, the Bitcoin ledger can be thought of as a state transition system, where there is a "state" consisting of the ownership status of all existing bitcoins and a "state transition function" that takes a state and an object called a "transaction" and outputs a new state consisting of the old state plus the modifications that the transaction intends to carry out, if valid. In a standard banking system, for example, the state is a balance sheet, transaction is a request to move $X from A to B, and the state transition function reduces the value in A's account by $X and increases the value in B's account by $X. If A's account has less than $X in the first place, the state transition function returns an error. Hence, one can formally define:

    APPLY(S,TX) -> S' or ERROR

In the banking system defined above:

    APPLY({ Alice: $50, Bob: $50 },"send $20 from Alice to Bob") = { Alice: $30, Bob: $70 }

But:
    
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

### Alternative Blockchain Applications

The idea of taking the underlying blockchain idea and applying it to other concepts also has a long history. In 2005, Nick Szabo came out with the concept of "[secure property titles with owner authority](http://szabo.best.vwh.net/securetitle.html)", a document describing how "new advances in replicated database technology" will allow for a blockchain-based system for storing a registry of who owns what land, creating an elaborate framework including concepts such as homesteading, adverse possession and Georgian land tax. However, there was unfortunately no effective replicated database system available at the time, and so the protocol was never implemented in practice. After 2009, however, once Bitcoin's decentralized consensus was developed a number of alternative applications rapidly began to emerge.

* **Namecoin** - created in 2010, Namecoin is best described as a decentralized name registration database. In decentralized protocols like Tor, Bitcoin and BitMessage, there needs to be some way of identifying accounts so that other people can interact with them, but in all existing solutions the orly kind of identifier available is a pseudorandom hash like `1LW79wp5ZBqaHW1jL5TCiBCrhQYtHagUWy`. Ideally, of course, one would like to be able to have an account with a name like "george". However, the problem is that if one person can create an account named "george" then someone else can use the same process to register "george" for themselves as well and impersonate them. Determining which of those two registrations is legitimate would thus need to be a decentralized first-to-file system, where the first registerer succeeds and the second fails - a problem perfectly suited for the Bitcoin consensus protocol. Namecoin is the oldest, and most successful, implementation of a name registration system using such an idea.
* **Colored coins** - the purpose of colored coins is to serve as a protocol to allow people to create their own digital currencies - or, in the important trivial case of a currency with one unit, digital tokens, on the Bitcoin blockchain. In the colored coins protocol, one "issues" a new currency by publicly assigning a color to a specific UTXO, and the protocol recursively defines the color of other UTXO to be the same as the color of the inputs that the transaction creating them spent (some special rules apply in the case of mixed-color inputs). This allows users to maintain wallets containing only UTXO of a specific color and send them around much like regular bitcoins, backtracking through the blockchain to determine the color of any UTXO that they receive. 
* **Metacoins** - the idea behind a metacoin is to have a protocol that lives on top of Bitcoin, using Bitcoin transactions to store metacoin transactions but having a different state transition function. Because the metacoin protocol cannot prevent invalid metacoin transactions from appearing in the Bitcoin blockchain, a rule is added that if `APPLY(S,TX)` returns an error, the protocol defaults to `APPLY(S,TX) = S`. This provides an easy mechanism for creating an arbitrary cryptocurrency protocol, potentially with advanced features that are not found in Bitcoin, with a very low development cost, since the complexities of mining and networking are already handled by the Bitcoin protocol.

Thus, in general, there are two approaches toward building a consensus protocol: building an independent network, and building a protocol on top of Bitcoin. The former approach, while reasonably successful in the case of applications like Namecoin, is imperfect because implementation is difficult; each individual implementation needs to bootstrap an independentl blockchain, as well as building and testing all of the necessary state transition and networking code, meaning that it currently takes much more effort than necessary to build such a project. Additionally, we predict that under conditions of zero development and upkeep cost decentralized applications would naturally follow a power law distribution where the vast majority would be too small to warrant their own blockchain, and we note that there exist large classes of decentralized applications, particularly decentralized organizations, that need to interact with each other on one blockchain. The latter approach, on the other hand, while elegant in its simplicity of implementation, unfortunately does not work well because it does not inherit the simplified payment verification features of Bitcoin; while SPV works by using blockchain depth as a proxy for validity going back more than a few blocks, blockchain-based meta-protocols cannot force the blockchain not to include transactions that are not valid within the context of their own protocols. Hence, a fully secure SPV meta-protocol implementation would need to backward scan all the way to the beginning of the Bitcoin blockchain.

### Scripting

Even with out any extensions, the Bitcoin protocol actually does facilitate a weak version of a concept of "smart contracts". UTXO in Bitcoin can be owned not just by a public key, but also by a more complicated script expressed in a simple stack-based programming language. A transaction spending that UTXO must then provide, rather than specifically an elliptic curve signature, data that satisfies the script. Indeed, even the basic public and private key ownership mechanism is implemented via a script: the script takes an elliptic curve signature as input, verifies it against the transaction and the key that owns the transaction, and returns 1 if the verification is successful and 0 otherwise. Other, more complicated, scripts exist for various additional use cases. For example, one can construct a script that requires signatures from two out of a given three private keys to validate ("multisig"), a setup useful for corporate accounts, secure storage of individual accounts and some merchant escrow situations. Scripts can also be used to pay bounties for solutions to computational problems, and there is even a protocol which allows decentralized exchange between multiple cryptocurrencies - essentially a script that says "this Bitcoin UTXO is yours if you can provide an SPV proof that you sent a Dogecoin transaction of this denomination to me".

However, the scripting language as implemented in Bitcoin has several important limitations:

* **Lack of Turing-completeness** - that is to say, while there is a large subset of computation that the Bitcoin scripting language supports, it does not nearly support everything. The main category that is missing is loops. This is done to avoid infinite loops during transaction verification; theoretically it is a surmountable obstacle, since any loop can be simulated by simply repeating the underlying code many times with an if statement, but it does lead to scripts that are very space-inefficient. For example, implementing an alternative elliptic curve signature algorithm would likely require 256 repeated multiplication rounds all written in the code.
* **Value-blindness** - there is no way for a UTXO script to provide fine-grained control over the amount that can be withdrawn. For example, one powerful use case of an oracle contract would be a contract for difference, where A and B put in $1000 worth of BTC and after 30 days the script sends $1000 worth of BTC to A and the rest to B. This would require an oracle to determine the value of 1 BTC, but even then it is a massive improvement in terms of trust and infrastructure requirement over the fully centralized solutions that are available now. However, because UTXO are all-or-nothing, the only way to achieve this is through the very inefficient hack of having many UTXO of varying denominations (eg. one UTXO of 2<sup>k</sup> for every k up to 30) and having O pick which UTXO to send to A and which to B.
* **Lack of state** - UTXO can either be spent or unspent; there is no opportunity for multi-stage contracts or scripts which keep any other internal state beyond that. This makes it hard to make multi-stage options contracts, decentralized exchange offers or two-stage cryptographic commitment protocols (necessary for secure computational bounties). It also means that UTXO can only be used to build simple, one-off contracts and not more complex applications such as decentralized organizations or domain name registries, and makes attempts at creating "colored coins" or other alternative digital assets difficult to implement. Binary state combined with value-blindness also mean that another important application, withdrawal limits, is impossible.
* **Blockchain-blindness** - UTXO are blind to blockchain data such as the nonce and previous hash. This severely limits applications in gambling, and several other categories, by depriving the scripting language of a potentially valuable source of randomness.

Many of these issues arise from the fact that, while Bitcoin does introduce many layers of abstraction, it is arguably not abstract enough. Although the rules for when outputs can be spent are arbitrary, the rules for how new outputs can be created are not: the concept of a "denomination" and the rule that the outputs must have equal or lesser denomination compared to the inputs are enshrined in the core protocol. It is the purpose of the Ethereum protocol to take this one last fixed feature and uproot it, allowing for the creation of completely arbitrary state transition functions.

## Ethereum

### Philosophy

The design behind Ethereum is intended to follow the following principles:

1. **Simplicity** - the Ethereum protocol should be as simple as possible, even at the cost of some data storage or time inefficiency. An average programmer should ideally be able to follow and implement the entire specification, so as to fully realize the unprecedented democratizing potential that cryptocurrency brings and further the vision of Ethereum as a protocol that is open to all. Any optimization which adds complexity should not be included unless that optimization provides very substantial benefit.
2. **Universality** - a fundamental part of Ethereum's design philosophy is that Ethereum does not have "features". Instead, Ethereum provides an internal Turing-complete scripting language, which a programmer can use to construct any smart contract or transaction type that can be mathematically defined. Want to invent your own financial derivative? With Ethereum, you can. Want to make your own currency? Set it up as an Ethereum contract. Want to set up a full-scale Daemon or Skynet? You may need to have a few thousand interlocking contracts, and be sure to feed them generously, to do that, but nothing is stopping you with Ethereum at your fingertips.
3. **Modularity** - the parts of the Ethereum protocol should be designed to be as modular and separable as possible. Over the course of development, our goal is to create a program where if one was to make a small protocol modification in one place, the application stack would continue to function without any further modification. Innovations such as [Dagger](https://github.com/ethereum/wiki/wiki/%5BEnglish%5D-Dagger), [Patricia trees](https://github.com/ethereum/wiki/wiki/%5BEnglish%5D-Patricia-Tree) and [RLP](https://github.com/ethereum/wiki/wiki/%5BEnglish%5D-RLP) should be implemented as separate libraries and made to be feature-complete even if Ethereum does not require certain features so as to make them usable in other protocols as well. Ethereum development should be maximally done so as to benefit the entire cryptocurrency ecosystem, not just itself.
4. **Agility** - details of the Ethereum protocol are not set in stone. Although we will be extremely judicious about making modifications to high-level constructs such as the [C-like language](https://github.com/ethereum/wiki/wiki/%5BEnglish%5D-CLL) and the address system, computational tests later on in the development process may lead us to discover that certain modifications to the algorithm or scripting language will substantially improve scalability or security. If any such opportunities are found, we will exploit them.
5. **Non-discrimination** - the protocol should not attempt to actively restrict or prevent specific categories of usage. All regulatory mechanisms in the protocol should be designed to directly regulate the harm and not attempt to oppose specific undesirable applications. A programmer can even run an infinite loop script on top of Ethereum for as long as they are willing to keep paying the per-computational-step transaction fee.

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

## Applications

### Sub-currencies

Sub-currencies have many applications ranging from currencies representing assets such as USD or gold to company stocks and even currencies with only one unit issued to represent collectibles or smart property. Advanced special-purpose financial protocols sitting on top of Ethereum may also wish to organize themselves with an internal currency. Sub-currencies are surprisingly easy to implement in Ethereum. The key point to understand is that all a currency fundamentally is is a database with one operation: subtract X units from A and give X units to B, with the proviso that (i) X had at least X units before the transaction and (2) the transaction is approved by A. All that it takes to implement a sub-currency is to write it as a contract.

The basic code for implementing a currency looks as follows:

    from = msg.sender
    to = msg.data[0]
    value = msg.data[1]

    if contract.storage[from] >= value:
        contract.storage[from] = contract.storage[from] - value
        contract.storage[to] = contract.storage[to] + value

This is basically a literal implementation of the "banking system" state transition function described further above in this document. A few extra lines of code need to be added to provide for the initial step of distributing the currency units in the first place and a few other edge cases, and ideally a function would be added to let other contracts query for the balance of an address. But that's all there is to it. Theoretically, Ethereum-based sub-currencies can potentially include another important feature that on-chain Bitcoin-based meta-currencies lack: the ability to pay transaction fees in the native currency. The way this would be implemented is that the contract would maintain an ether balance with which it would refund ether used to pay fees to the sender, and it would refill this balance by collecting the internal currency units that it takes in fees and reselling them in a constant running auction. Users would thus need to "activate" their accounts with ether, but once the ether is there it would be reusable because the contract would refund it each time.

### Financial derivatives

Financial derivatives are the most common application of a "smart contract", and one of the simplest to implement in code. The main challenge in implementing financial contracts is that the majority of them require reference to an external price ticker; for example, a very desirable application is a smart contract that hedges against the volatility of ether (or another cryptocurrency) with respect to the US dollar, but doing this requires the contract to know what the value of ETH/USD is. The simplest way to do this is through a "data feed" contract maintained by a specific party (eg. NASDAQ) designed so that that party has the ability to update the contract as needed, and providing an interface that allows other contracts to send a message to that contract and get back a response that provides the price.

Given that critical ingredient, the hedging contract would look as follows:

1. Wait for party A to input 1000 ether.
2. Wait for party B to input 1000 ether.
3. Record the USD value of 1000 ether, calculated by querying the data feed contract, in storage, say this is $x.
4. After 30 days, allow A or B to "reactivate" the contract in order to send $x worth of ether (calculated by querying the data feed contract again to get the new price) to A and the rest to B.

Such a contract would have significant potential in crypto-commerce. One of the main problems cited about cryptocurrency is the fact that it's volatile; although many users and merchants may want the security and convenience of dealing with cryptographic assets, they many not wish to face that prospect of losing 23% of the value of their funds in a single day. Up until now, the most commonly proposed solution has been issuer-backed assets; the idea is that an issuer creates a sub-currency in which they have the right to issue and revoke units, and provide one unit of the currency to anyone who provides them (offline) with one unit of a specified underlying asset (eg. gold, USD). The issuer then promises to provide one unit of the underlying asset to anyone who sends back one unit of the crypto-asset. This mechanism allows any non-cryptographic asset to be "uplifted" into a cryptographic asset, provided that the issuer can be trusted.

In practice, however, issuers are not always trustworthy, and in some cases the banking infrastructure is too weak, or too hostile, for such services to exist. Financial derivatives provide an alternative. Here, instead of a single issuer providing the funds to back up an asset, a decentralized market of speculators, betting that the price of a cryptographic reference asset (eg. ETH) will go up, plays that role. Unike issuers, speculators have no option to default on their side of the bargain because the hedging contract holds their funds in escrow. Note that this approach is not fully decentralized, because a trusted source is still needed to provide the price ticker, although arguably even still this is a massive improvement in terms of reducing infrastructure requirements (unlike being an issuer, issuing a price feed may be categorized as free speech and requires no licenses) and reducing the potential for fraud.

### Identity and Reputation Systems

The earliest alternative cryptocurrency of all, [Namecoin](http://namecoin.org/), attempted to use a Bitcoin-like blockchain to provide a name registration system, where users can register their names in a public database alongside other data. The major cited use case is for a [DNS](http://en.wikipedia.org/wiki/Domain_Name_System) system, mapping domain names like "bitcoin.org" (or, in Namecoin's case, "bitcoin.bit") to an IP address. Other use cases include email authentication and potentially more advanced reputation systems. Here is a simple contract to provide a Namecoin-like name registration system on Ethereum:

    if !contract.storage[tx.data[0]]:
        contract.storage[tx.data[0]] = tx.data[1]

The contract is very simple; all it is is a database inside the Ethereum network that can be added to, but not modified or removed from. Anyone can register a name with some value, and that registration then sticks forever. A more sophisticated name registration contract will also have a "function clause" allowing other contracts to query it, as well as a mechanism for the "owner" (ie. the first registerer) of a name to change the data or transfer ownership. One can even add reputation and web-of-trust functionality on top.

### Decentralized Autonomous Organizations

The general concept of a "decentralized autonomous organization" is that of a virtual entity that has a certain set of members or shareholders which, perhaps with a 67% majority, have the right to spend the entity's funds and modify its code. The members would collectively decide on how the organization should allocate its funds. Methods for allocating a DAO's funds could range from bounties, salaries to even more exotic mechanisms such as an internal currency to reward work. This essentially replicates the legal trappings of a traditional company or nonprofit but using only cryptographic blockchain technology for enforcement. So far much of the talk around DAOs has been around the "capitalist" model of a "decentralized autonomous corporation" (DAC) with dividend-receiving shareholders and tradable shares; an alternative, perhaps described as a "decentralized autonomous community", would have all members have an equal share in the decision making and require 67% of existing members to agree to add or remove a member. The requirement that one person can only have one membership would then need to be enforced collectively by the group.

A general outline for how to code a DAO is as follows. The simplest design is simply a piece of self-modifying code that changes if two third of members agree on a change. To accomplish this, there would be three transaction types, distinquished by the data provided in the transaction:

* [0,k] to register a vote in favor of a code change
* [1,k,L,v0,v1...vn] to register a code change at code k in favor of setting memory starting from location L to v0, v1 ... vn
* [2,k] to finalize a given code change

The contract would then have clauses for each of these. It would maintain a record of all open code changes, along with a list of who voted for them. It would also have a list of all members. When any code change gets to two thirds of members voting for it, that code change would be processed and executed. A more sophisticated skeleton would also have built-in voting ability for features like sending a transaction, adding members and removing members, and may even provide for Liquid Democracy-style vote delegation (ie. anyone can assign someone to vote for them, and assignment is transitive so if A assigns B and B assigns C then C determines A's vote). This design would allow the DAO to grow organically as a decentralized community, allowing people to eventually delegate the task of filtering out who is a member to specialists, although unlike in the "current system" specialists can easily pop in and out of existence over time as individual community members change their alignments.

An alternative model is for a decentralized corporation, where any account can have zero or more shares, and two thirds of the shares are required to make a decision. A complete skeleton would involve code change functionality, the ability to make an offer to buy or sell shares, and the ability to accept offers (preferably with an order-matching mechanism inside the contract). Delegation would also exist Liquid Democracy-style, generalizing the concept of a "board of directors".

### Decentralized File Storage

Over the past few years, there have emerged a number of popular online file storage startups, the most prominent being Dropbox, seeking to allow users to upload a backup of their hard drive and have the service store the backup and allow the user to access it in exchange for a monthly fee. However, at this point the file storage market is at times relatively inefficient; a cursory look at various [existing solutions](http://online-storage-service-review.toptenreviews.com/) shows that, particularly at the "uncanny valley" 20-200 GB level at which neither free quotas nor enterprise-level discounts kick in, monthly prices for mainstream file storage costs are such that you are paying for more than the cost of the entire hard drive in a single month. Ethereum contracts can allow for the development of a decentralized file storage ecosystem, where individual users can earn small quantities of money by renting out their own hard drives and unused space can be used to further drive down the costs of file storage. 

The key underpinning piece of such a device would be what we have termed the "decentralized Dropbox contract". This contract works as follows. First, one splits the desired data up into blocks, encrypting each block for privacy, and builds a Merkle tree out of it. One then makes a contract with the rule that, every N blocks, the contract would pick a random index in the Merkle tree (using the previous block hash, accessible from EVM code, as a source of randomness), and give X ether to the first entity to supply a transaction with a simplified payment verification-like proof of ownership of the block at that particular index in the tree. When a user wants to re-download their file, they can use a micropayment channel protocol (eg. pay 1 szabo per 32 kilobytes) to recover the file; the most fee-efficient approach is for the payer not to publish the transaction until the end, instead replacing the transaction with a slightly more lucrative one with the same nonce after every 32 kilobytes.

An important feature of the protocol is that, although it may seem like one is trusting many random nodes not to decide to forget the file, one can reduce that risk down to near-zero by splitting the file into many pieces via secret sharing, and watching the contracts to see each piece is still in some node's possession. If a contract is still paying out money, that provides a cryptographic proof that someone out there is still storing the file. 

### Further Applications

1. **Savings wallets**. Suppose that Alice wants to keep her funds safe, but is worried that she will lose or someone will hack her private key. She puts ether into a contract with Bob, a bank, as follows:
    * Alice alone can withdraw a maximum of 1% of the funds per day.
    * Bob alone can withdraw a maximum of 1% of the funds per day, but Alice has the ability to make a transaction with her key shutting off this ability.
    * Alice and Bob together can withdraw anything.

Normally, 1% per day is enough for Alice, and if Alice wants to withdraw more she can contact Bob for help. If Alice's key gets hacked, she runs to Bob to move the funds to a new contract. If she loses her key, Bob will get the funds out eventually. If Bob turns out to be malicious, then she can turn off his ability to withdraw.

2. **Crop insurance**. One can easily make a financial derivatives contract but using a data feed of the weather instead of any price index. If a farmer in Iowa purchases a derivative that pays out inversely based on the precipitation in Iowa, then if there is a drought, the farmer will automatically receive money and if there is enough rain the farmer will be happy because their crops would do well.

3. **A decentralized data feed**. For financial contracts for difference, it may actually be possible to decentralize the data feed via a protocol called "[SchellingCoin](http://blog.ethereum.org/2014/03/28/schellingcoin-a-minimal-trust-universal-data-feed/)". SchellingCoin basically works as follows: N parties all put into the system the value of a given datum (eg. the ETH/USD price), the values are sorted, and everyone between the 25th and 75th percentile gets one token as a reward. Everyone has the incentive to provide the answer that everyone else will provide, and the only value that a large number of players can realistically agree on is the obvious default: the truth. This creates a decentralized protocol that can theoretically provide any number of values, including the ETH/USD price, the temperature in Berlin or even the result of a particular hard computation.

4. **Smart multisignature escrow**. Bitcoin allows multisignature transaction contracts where, for example, three out of a given five keys can spend the funds. Ethereum allows for more granularity; for example, four out of five can spend everything, three out of five can spend up to 10% per day, and two out of five can spend up to 0.5% per day. Additionally, Ethereum multisig is asynchronous - two parties can register their signatures on the blockchain at different times and the last signature will automatically send the transaction.

5. **Peer-to-peer gambling**. Any number of peer-to-peer gambling protocols, such as Frank Stajano and Richard Clayton's [Cyberdice](http://www.cl.cam.ac.uk/~fms27/papers/2008-StajanoCla-cyberdice.pdf), can be implemented on the Ethereum blockchain. The simplest gambling protocol is actually simply a contract for difference on the next block hash. From there, entire gambling services such as SatoshiDice can be replicated on the blockchain either by creating a unique contract per bet or by using a quasi-centralized contract.

Ultimately, the best P2P gambling implementation may be something like JustDice, where the contract has two ways of interacting with it. First, users can make bets, and receive either N*x or 0 depending on whether or not they won. Second, users can "invest" into the site, and receive shares where, as long as no investors enter or leave, each share is worth a constant percentage of the contract's total reserve. For example, if a contract has a reserve of 50000 ETH and 1000 shares, and someone invests 5000 ETH, they get 100 shares. If the contract then takes a few bets and its total reserve goes up from 55000 ETH to 66000 ETH, then each of the 1100 shares is now worth 60 ETH, so if the investor sells their shares the contract goes down to 60000 ETH and 1000 shares. The reserve provides the "float" for the contract to cover sudden unexpected losses, but in general the investors are expected to win as the odds are slightly in favor of the contract.

6. A full-scale **on-chain stock market**. Provided an oracle or SchellingCoin, prediction markets are also easy to implement as a trivial consequence, and prediction markets together with SchellingCoin may prove to be the first mainstream application of [futarchy](http://hanson.gmu.edu/futarchy.html) as a governance protocol for decentralized organizations.

7. An **on-chain decentralized marketplace**, using the identity and reputation system as a base.

## Concerns

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

Now, send a transaction to A. Thus, in 51 transactions, we have a contract that takes up 2<sup>50</sup> computational steps. Miners could try to detect such logic bombs ahead of time by maintaining a value alongside each contract specifying the maximum number of computational steps that it can take, and calculating this for contracts calling other contracts recursively, but that would require miners to forbid contracts that create other contracts (since the creation and execution of all 26 contracts above could easily be rolled into a single contract).Another problematic point is that the address field of a message is a variable, so in general it may not even be possible to tell which other contracts a given contract will call ahead of time. Hence, all in all, we have a surprising conclusion: Turing-completeness is surprisingly manageable, and the lack of Turing-completeness is equally surprisingly not - unless the same controls are in place, but in that case why not just bring back the loops?

### Modified GHOST Implementation

The "Greedy Heavist Observed Subtree" (GHOST) protocol is an innovation first introduced by Yonatan Sompolinsky and Aviv Zohar in [December 2013](http://www.cs.huji.ac.il/~avivz/pubs/13/btc_scalability_full.pdf). The motivation behind GHOST is that blockchains with fast confirmation times currently suffer from reduced security due to a high stale rate - because blocks take a certain time to propagate through the network, if miner A mines a block and then miner B happens to mine another block before miner A's block propagates to B, miner B's block will end up wasted and will not contribute to network security. Furthermore, there is a centralization issue: if miner A is a mining pool with 30% hashpower and B has 10% hashpower, A will have a risk of producing stale blocks 70% of the time whereas B will have a risk of producing stale blocks 90% of the time. Thus, if the stale rate is high, A will be substantially more efficient simply by virtue of its size. With these two effects combined, blockchains which produce blocks quickly are very likely to lead to one mining pool having a large enough percentage of the network hashpower to have de facto control over the mining process.

As described by Sompolinsky and Zohar, GHOST solves the first issue of network security loss by including stale blocks in the calculation of which chain is the "longest"; that is to say, not just the parent and further ancestors of a block, but also the stale descendants of the block's ancestor (in Ethereum jargon, "uncles") are added to the calculation of which block has the largest total proof of work backing it. To solve the second issue of centralization bias, we go beyond the protocol described by Sompolinsky and Zohar, and also provide block rewards to stales: a stale block receives 87.5% of its base reward, and the nephew that includes the stale block receives the remaining 12.5%. Transaction fees, however, are not awarded to uncles.

Ethereum implements a simplified version of GHOST which only goes down one level. Specifically, a stale block can only be included as an uncle by the direct child of one of its direct siblings, and not any block with a more distant relation. This was done for several reasons. First, unlimited GHOST would include too many complications into the calculation of which uncles for a given block are valid. Second, unlimited GHOST with compensation as used in Ethereum removes the incentive for a miner to mine on the main chain and not the chain of a public attacker. Finally, calculations show that single-level GHOST has over 80% of the benefit of unlimited GHOST, and provides a stale rate comparable to the 2.5 minute Litecoin even with a 40-second block time. However, we will be conservative and still retain a Primecoin-like 60-second block time because individual blocks may take a longer time to verify.

### Block Rewards and Limits

A miner receives three kinds of rewards: a static block reward for producing a block, fees from transactions, and nephew/uncle rewards as described in the GHOST section above. The miner will receive 100% of the block reward and transaction fees for themselves. As described in the GHOST section, uncles only receive 87.5% of their block reward, with the remaining 12.5% going to the including nephew; the transaction fees from the stale block do not go to anyone.

The default approach is to have no mandatory fees, allowing miners to include any transactions that they deem profitable to include. This approach has been received very favorably in the Bitcoin community particularly because it is "market-based" - that is, there is a market with miners on one side and transaction senders on the other where supply and demand determine the price. However, the problem with this line of reasoning is that transaction processing is not a market; although it is intuitively attractive to construe transaction processing as a service that the miner is making the effort to offer to transaction senders that needs to be paid for at an agreed-upon rate, in reality every transaction that a single miner includes needs to be processed by every node in the network, so the external costs of transaction processing borne by the network, which has no say in whether or not a transaction takes place, are thousands of times higher than the internal costs borne by the miner.

However, as it turns out this flaw in the market-based mechanism, when given a particular inaccurate simplifying assumption, magically cancels itself out. The argument is as follows. Suppose that:

1. The mining block reward is `B`.
2. A transaction leads to `k` operations, offering the reward `kR` to any miner that includes it where `R` is set by the sender and `k` and `R` are visible to the miner beforehand.
3. An operation has a processing cost of `C` to any node (ie. all nodes have equal efficiency)
4. There are `N` mining nodes, each with exactly equal processing power (ie. `1/N` of total)
5. No non-mining full nodes exist.

A miner would be willing to process a transaction if the expected reward is greater than the cost. The expected reward is `kR/N` since the miner has a `1/N` chance of processing the next block. The cost is simply `kC`. Thus, the miner is willing to process transactions where:

    kR/N > kC
    kR > kNC
    R > NC

Note that `R` is the per-operation fee, and `NC` is the cost to the entire network together of processing an operation. Hence, miners have the incentive to include only those transactions for which the total utilitarian benefit exceeds the cost. However, there are three important deviations from those assumptions in reality:

1. The miner does pay a higher cost to process the transaction than the other verifying nodes, since the extra verification time delays block propagation and thus increases the chance the block will become a stale.
2. There do exist nonmining full nodes.
3. The mining power distribution may end up radically inegalitarian in practice.
4. Speculators, political enemies and crazies whose utility function includes causing harm to the network do exist, and they can cleverly set up contracts where their cost is much lower than the cost paid by other verifying nodes.

(1) provides a tendency for the miner to include fewer transactions, and (2) increases `NC`; hence, these two effects at least partially cancel each other out. (3) and (4) are the major issue; to solve them we simply institute a floating cap: no block can have more operations than BLK_LIMIT_FACTOR times the long-term exponential moving average. Specifically:

    blk.oplimit = floor((parent.oplimit * (EMAFACTOR - 1) + floor(parent.opcount * BLK_LIMIT_FACTOR)) / EMA_FACTOR)

`BLK_LIMIT_FACTOR` and `EMA_FACTOR` are constants that will be set to 65536 and 1.5 for the time being, but can be changed pending further analysis.

### Mining Centralization



### Scalability

## Conclusion

The Ethereum protocol's design philosophy is in many ways the opposite from that taken by many other cryptocurrencies today. Other cryptocurrencies aim to add complexity and increase the number of "features"; Ethereum, on the other hand, takes features away. The protocol does not "support" multisignature transactions, multiple inputs and outputs, hash codes, lock times or many other features that even Bitcoin provides. Instead, all complexity comes from a universal, Turing-complete scripting language, which can be used to build up literally any feature that is mathematically describable through the contract mechanism. As a result, we have a protocol with unique potential; rather than being a closed-ended, single-purpose protocol intended for a specific array of applications in data storage, gambling or finance, Ethereum is open-ended by design, and we believe that it is extremely well-suited to serving as a foundational layer for a very large number of both financial and non-financial protocols in the years to come.



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
