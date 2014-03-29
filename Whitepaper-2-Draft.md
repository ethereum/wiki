WORK IN PROGRESS!!!

### A Next-Generation Smart Contract and Decentralized Application Platform

When Satoshi Nakamoto first set the Bitcoin blockchain into motion in January 2009, he was simultaneously introducing two radical and highly untested concepts. The first is the "bitcoin", a decentralized peer-to-peer online currency that maintains a value without any backing, [intrinsic value](http://bitcoinmagazine.com/8640/an-exploration-of-intrinsic-value-what-it-is-why-bitcoin-doesnt-have-it-and-why-bitcoin-does-have-it/) or central issuer. In the first four years of Bitcoin's development, this first aspect of Bitcoin has taken up the bulk of the public attention; political activists have seized upon it as a beacon of freedom from central banking, economists of varying stripes criticize or praise it, and front-page articles appear hailing its meteoric rises in price and gloating over its equally impressive crashes. However, there is also another, equally important, part to Satoshi's grand experiment: the concept of a proof of work-based blockchain to allow for public agreement on the state of a distributed database. Bitcoin is one of a class of applictions that can be described as a first-to-file system: if one entity has 50 BTC, and sends those 50 BTC to A and then sends the same 50 BTC to B, only the party whose transaction was confirmed first will get the bitcoins. There is no intrinsic way of looking at two transactions and figuring out which came earlier, and for several decades this stymied the development of decentralized digital currency. Satoshi's blockchain created the first credible decentralized solution. And now, more recently, attention is rapidly starting to shift toward this second part of Bitcoin's technology, and how the blockchain concept can be used for more than just money.

Commonly cited applications include using on-blockchain digital assets to represent custom currencies and financial instruments (["colored coins"](https://docs.google.com/a/buterin.com/document/d/1AnkP_cVZTCMLIzw4DvsW6M8Q2JC0lIzrTLuoWu2z1BE/edit)), ["smart property"](https://en.bitcoin.it/wiki/Smart_Property) devices such as cars which track a colored coin on a blockchain to determine their present legitimate owner, the resigtration and management of non-fungible assets such as domain names ("Namecoin") as well as more advanced applications such as decentralized exchange, financial derivatives, peer-to-peer gambling and on-blockchain identity and reputation systems. Another important area of inquiry is "smart contracts" - systems which automatically move digital assets according to arbitrary pre-specified rules. For example, one might have a treasury contract of the form "A can withdraw up to X currency units per day, B can withdraw up to Y per day, A and B together can withdraw anything, and A can shut off B's ability to withdraw". The logical extension of this is [decentralized autonomous organizations](http://bitcoinmagazine.com/7050/bootstrapping-a-decentralized-autonomous-corporation-part-i/) (DAOs) - long-term smart contracts that encode the bylaws of an entire organization. What Ethereum intends to provide is a blockchain with a built-in fully fledged Turing-complete programming language that can be used to create "contracts" that can be used to encode arbitrary state transition functions, allowing users to create any of the systems described above, as well as many others that we have not yet imagined, simply by writing up the logic in a few lines of code.

### Table of Contents

* [History](#history)
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


## History

The concept of decentralized digital currency, as well as alternative applications like property registries, has been around for decades. The anonymous e-cash protocols of the 1980s and the 1990s, mostly reliant on a cryptographic primitive known as Chaumian blinding, provide a currency with a high degree of privacy, including from the "central bank", but the protocols largely failed to gain traction because of their reliance on a centralized intermediary. In 1998, Wei Dai's [b-money](http://www.weidai.com/bmoney.txt) became the first proposal to introduce the idea of creating money through solving computational puzzles as well as decentralized consensus, but the proposal was scant on details on how decentralized consensus could actually be implemented. In 2005, Hal Finney introduced a concept of "[reusable proofs of work](http://www.finney.org/~hal/rpow/)", a system which uses ideas from b-money together with Adam Back's computationally difficult Hashcash puzzles to create a concept for a cryptocurrency, but fell short of the ideal by relying on trusted computing as a backend.

Decentralized consensus is necessary because a digital currency is inherently a first-to-file system: if one entity has 50 BTC, and sends those 50 BTC to A and then sends the same 50 BTC to B, only the party whose transaction was confirmed first will get the bitcoins. There is no intrinsic way of looking at two transactions and figuring out which came earlier; the only possible solution is for the users of the system to provide the information, using a decentralized consensus algorithm to allow the network to come to an agreement on the result. The main roadblock that all of these protocols faced is the fact that, while there had been plenty of research on creating secure Byzantine-fault-tolerant multiparty consensus systems for many years, all of the protocols described were solving only half of the problem. The protocols assumed that all participants in the system were known, and produced security margins of the form "if N parties participate, then the system can tolerate up to N/4 malicious actors". The problem is, however, that in an anonymous setting such security margins are vulnerable to sybil attacks, where an attacker turns on a botnet and overwhelms the network with a vast quantity of nodes even to the point that the legitimate network falls below the security margin required for it to disrupt the false consensus.

The innovation provided by Satoshi is the idea of combining a very simple decentralized consensus protocol based on a blockchain, where a random node combines transactions into a "block" every ten minutes creating an ever-growing blockchain, with proof of work as a mechanism through which nodes gain the right to become one of the nodes in the system. While nodes with a large amount of computational power can create multiple accounts (indeed, the Bitcoin system does not even try to stop powerful nodes from gaining proportionately large influence), coming up with more computational power than the entire network combined is much harder than coming up with a million nodes.

### Namecoin

The idea of taking the underlying blockchain idea and applying it to other concepts also has a long history. In 2005, Nick Szabo came out with the concept of "[secure property titles with owner authority](http://szabo.best.vwh.net/securetitle.html)", a document describing how "new advances in replicated database technology" will allow for a blockchain-based system for storing a registry of who owns what land, creating an elaborate framework including concepts such as homesteading, adverse possession and Georgian land tax. However, there was unfortunately no effective replicated database system available at the time, and so the protocol has not yet been implemented, although the hope is that with the advent of Ethereum implementation will become trivial. The first alternative blockchain application ever to be implemented to completion is Namecoin, created in 2010.

The purpose of Namecoin is to serve as a distributed domain name system, or a distributed phonebook for public keys in general. The problem that such systems are trying to solve is an issue that is known as [Zooko's triangle](http://en.wikipedia.org/wiki/Zooko's_triangle). Ideally, names should have three properties:

* **Security** - if one person is using a particular name, another person should not be able to use the same name
* **Decentralization** - distribution of names should not be controlled by some single gatekeeper
* **Human-memorable** - names should be sufficietly short as to be easily memorized in large quantities

However, all systems so far have only succeeded at achieving a maximum of two of these qualities. For example:

* Human names are decentralized, since everyone issues their own (or more precisely their children's), and human-memorable, since it is quite easy to remember Bob, George, Fred, etc. However, they are not secure - anyone can change their name to Bill Gates and start speaking out against Microsoft on television.
* Domain names like google.com are secure, since I cannot easily make a server that pretends to be google.com, and they are human-memorable, but they are not decentralized - a set of agencies governed by ICANN hands them out.
* Bitcoin addresses are secure, since it's computationally infeasible to generate a private key that maps to a given pre-specified Bitcoin address and steal its funds, and they are decentralized, since everyone generates their own, but they are not human-memorable - good luck memorizing `1PsFQAkQ5jkN35cVhG4SE1uxAfzNB65S3M`.
* Tor onion addresses are secure, decentralized, but also not memorable.
* Email addresses are secure and memorable, but centralized.
* Phone numbers are secure, centralized, and only partially human-memorable.

Namecoin is the first system that arguably broke the triangle, having all three properties. Normally, having all three properties runs into a paradox. If names are short enough to be memorable, then if someone registered themselves "george" then you can go through the same process to register yourself as "george" as well. Determining which of those two registrations is legitimate would thus need to be a decentralized first-to-file system, where the first registerer succeeds and the second fails. Fortunately, it just so happens that this is precisely the same problem as that faced by Bitcoin.

So far, Namecoin has not particularly taken off in practice, and arguably its main flaw, beyond simple network effects, was pricing. In the traditional, centralized, domain name system, there is a centralized agency tasked with setting the price of domain names, and there is a fine balance that that agency must maintain. If the price is set too high (eg. $9000), then ordinary users will not be able to afford names, and if the price is set too low (eg. $0.009) then speculators will buy up all of the domain names and resell them for the optimal price at a profit. The current rate of $9 per year seems to be close to optimal. In Namecoin, it is very difficult to target a price like $9, and so in practice domains have ended up being very cheap and thus largely taken up by speculators. Future versions of Namecoin can attempt to address this problem through various market-based pricing schemes, but several iterations will likely be necessary.

### Colored Coins

The first attempt to implement a system for managing smart property and custom currencies and assets on top of a blockchain was built as a sort of overlay protocol on top of Bitcoin, with many advocates making a comparison to the way that, in the [internet protocol stack](http://en.wikipedia.org/wiki/Internet_protocol_suite), [HTTP](http://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol) serves as a layer on top of [TCP](http://en.wikipedia.org/wiki/Transmission_Control_Protocol). The [colored coins](https://docs.google.com/a/ursium.com/document/d/1AnkP_cVZTCMLIzw4DvsW6M8Q2JC0lIzrTLuoWu2z1BE/edit) protocol is roughly defined as follows:

1. A colored coin issuer determines that a given transaction output H:i (H being the transaction hash and `i` the output index) represents a certain asset, and publishes a "color definition" specifying this transaction output alongside what it represents (eg. 1 satoshi from `H:i` = 1 ounce of gold redeemable at Stephen's Gold Company)
2. Others "install" the color definition file in their colored coin clients.
3. When the color is first released, output H:i is the only transaction output to have that color.
4. If a transaction spends inputs with color X, then its outputs will also have color X. For example, if the owner of H:i immediately makes a transaction to split that output among five addresses, then those transaction outputs will all also have color X. If a transaction has inputs of different colors, then a "color transfer rule" or "color kernel" determines which colors which outputs are (eg. a very naive implementation may say that output 0 has the same color as input 0, output 1 the same color as input 1, etc).
5. When a colored coin client notices that it received a new transaction output, it uses a back-tracing algorithm based on the color kernel to determine the color of the output. Because the rule is deterministic, all clients will agree on what color (or colors) each output has.

However, the protocol has several large weaknesses:


**Simplified Payment Verification in Bitcoin **

![SPV in bitcoin](https://www.ethereum.org/gh_wiki/spv_bitcoin.png)

_Left: it suffices to present only a small number of nodes in a Merkle tree to give a proof of the validity of a branch._

_Right: any attempt to change any part of the Merkle tree will eventually lead to an inconsistency somewhere up the chain._


1. **Difficulty of simplified payment verification** - Bitcoin's [Merkle Tree](http://en.wikipedia.org/wiki/Merkle_tree) construction allows for a protocol known as "[simplified payment verification](https://en.bitcoin.it/wiki/Scalability#Simplified_payment_verification)", where a client that does not download the full blockchain can quickly determine the validity of a transaction output by asking other nodes to provide a cryptographic proof of the validity of a single branch of the tree. The client will still need to download the block headers to be secure, but the amount of data bandwidth and verification time required drops by a factor of nearly a thousand. With colored coins, this is much harder. The reason is that one cannot determine the color of a transaction output simply by looking up the Merkle tree; rather, one needs to employ the backward scanning algorithm, fetching potentially thousands of transactions and requesting a Merkle tree validity proof of each one, before a client can be fully satisfied that a transaction has a certain color. After over a year of investigation, including help from ourselves, no solution has been found to this problem.
2. **Incompatibility with scripting** - as mentioned above, Bitcoin does have a moderately flexible scripting system, for example allowing users to sign transactions of the form "I release this transaction output to anyone willing to pay to me 1 BTC". Other examples include [assurance contracts](http://en.wikipedia.org/wiki/Assurance_contract), [efficient micropayments](https://en.bitcoin.it/wiki/Contracts#Example_7:_Rapidly-adjusted_.28micro.29payments_to_a_pre-determined_party) and on-blockchain auctions. However, this system is inherently not color-aware; that is to say, one cannot make a transaction of the form "I release this transaction output to anyone willing to pay me one gold coin defined by the genesis H:i", because the scripting language has no idea that a concept of "colors" even exists. One major consequence of this is that, while trust-free swapping of two different colored coins is possible, a full decentralized exchange is not since there is no way to place an enforceable order to buy or sell.
3. **Same limitations as Bitcoin** - ideally, on-blockchain protocols would be able to support advanced derivatives, bets and many forms of conditional transfers. Unfortunately, colored coins inherits the limitations of Bitcoin in terms of the impossibility of many such arrangements.

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

In both cases, the conclusion is as follows. The effort to build more advanced protocols on top of Bitcoin, like HTTP over TCP, is admirable, and is indeed the correct way to go in terms of implementing advanced decentralized applications. However, the attempt to build colored coins and metacoins on top of Bitcoin is more like building HTTP over SMTP. The intention of SMTP was to transfer email messages, not serve as a backbone for generic internet communications, and one would have had to implement many inefficient and architecturally ugly practices in order to make it effective. Similarly, while Bitcoin is a great protocol for making simple transactions and storing value, the evidence above shows that Bitcoin is absolutely not intended to function, and cannot function, as a base layer for financial peer-to-peer protocols in general.

Ethereum solves the scalability issues by being hosted on its own blockchain, and by storing a distinct "state tree" in each block along with a transaction list. Each "state tree" represents the current state of the entire system, including address balances and contract states. Ethereum contracts are allowed to store data in a persistent memory storage. This storage, combined with the Turing-complete scripting language, allows us to encode an entire currency inside of a single contract, alongside countless other types of cryptographic assets. Thus, the intention of Ethereum is not to replace the colored coins and metacoin protocols described above. Rather, Ethereum intends to serve as a superior foundational layer offering a uniquely powerful scripting system on top of which arbitrarily advanced contracts, currencies and other decentralized applications can be built. If existing colored coins and metacoin projects were to move onto Ethereum, they would gain the benefits of Ethereum's simplified payment verification, the option to be compatible with Ethereum's financial derivatives and decentralized exchange, and the ability to work together on a single network. With Ethereum, someone with an idea for a new contract or transaction type that might drastically improve the state of what can be done with cryptocurrency would not need to start their own coin; they could simply implement their idea in Ethereum script code. In short, Ethereum is a foundation for innovation.

## Philosophy

The design behind Ethereum is intended to follow the following principles:

1. **Simplicity** - the Ethereum protocol should be as simple as possible, even at the cost of some data storage or time inefficiency. An average programmer should ideally be able to follow and implement the entire specification, so as to fully realize the unprecedented democratizing potential that cryptocurrency brings and further the vision of Ethereum as a protocol that is open to all. Any optimization which adds complexity should not be included unless that optimization provides very substantial benefit.
2. **Universality** - a fundamental part of Ethereum's design philosophy is that Ethereum does not have "features". Instead, Ethereum provides an internal Turing-complete scripting language, which a programmer can use to construct any smart contract or transaction type that can be mathematically defined. Want to invent your own financial derivative? With Ethereum, you can. Want to make your own currency? Set it up as an Ethereum contract. Want to set up a full-scale Daemon or Skynet? You may need to have a few thousand interlocking contracts, and be sure to feed them generously, to do that, but nothing is stopping you with Ethereum at your fingertips.
3. **Modularity** - the parts of the Ethereum protocol should be designed to be as modular and separable as possible. Over the course of development, our goal is to create a program where if one was to make a small protocol modification in one place, the application stack would continue to function without any further modification. Innovations such as [Dagger](https://github.com/ethereum/wiki/wiki/%5BEnglish%5D-Dagger), [Patricia trees](https://github.com/ethereum/wiki/wiki/%5BEnglish%5D-Patricia-Tree) and [RLP](https://github.com/ethereum/wiki/wiki/%5BEnglish%5D-RLP) should be implemented as separate libraries and made to be feature-complete even if Ethereum does not require certain features so as to make them usable in other protocols as well. Ethereum development should be maximally done so as to benefit the entire cryptocurrency ecosystem, not just itself.
4. **Agility** - details of the Ethereum protocol are not set in stone. Although we will be extremely judicious about making modifications to high-level constructs such as the [C-like language](https://github.com/ethereum/wiki/wiki/%5BEnglish%5D-CLL) and the address system, computational tests later on in the development process may lead us to discover that certain modifications to the algorithm or scripting language will substantially improve scalability or security. If any such opportunities are found, we will exploit them.
5. **Non-discrimination** - the protocol should not attempt to actively restrict or prevent specific categories of usage. All regulatory mechanisms in the protocol should be designed to directly regulate the harm and not attempt to oppose specific undesirable applications. A programmer can even run an infinite loop script on top of Ethereum for as long as they are willing to keep paying the per-computational-step transaction fee.

## Basic Building Blocks

At its core, Ethereum starts off as a fairly regular memory-hard proof-of-work mined cryptocurrency without many extra complications. In fact, Ethereum is in some ways simpler than the Bitcoin-based cryptocurrencies that we use today. The concept of a transaction having multiple inputs and outputs, for example, is gone, replaced by a more intuitive balance-based model (to prevent transaction replay attacks, as part of each account balance we also store an incrementing nonce). Sequence numbers and lock times are also removed, and all transaction and block data is encoded in a single format. Instead of addresses being the RIPEMD160 hash of the SHA256 hash of the public key prefixed with 04, addresses are simply the last 20 bytes of the SHA3 hash of the public key. Unlike other cryptocurrencies, which aim to offer a large number of "features", Ethereum intends to take features away, and instead provide its users with near-infinite power through an all-encompassing mechanism known as "contracts".

### Modified GHOST Implementation

The "Greedy Heavist Observed Subtree" (GHOST) protocol is an innovation first introduced by Yonatan Sompolinsky and Aviv Zohar in [December 2013](http://www.cs.huji.ac.il/~avivz/pubs/13/btc_scalability_full.pdf). The motivation behind GHOST is that blockchains with fast confirmation times currently suffer from reduced security due to a high stale rate - because blocks take a certain time to propagate through the network, if miner A mines a block and then miner B happens to mine another block before miner A's block propagates to B, miner B's block will end up wasted and will not contribute to network security. Furthermore, there is a centralization issue: if miner A is a mining pool with 30% hashpower and B has 10% hashpower, A will have a risk of producing stale blocks 70% of the time whereas B will have a risk of producing stale blocks 90% of the time. Thus, if the stale rate is high, A will be substantially more efficient simply by virtue of its size. With these two effects combined, blockchains which produce blocks quickly are very likely to lead to one mining pool having a large enough percentage of the network hashpower to have de facto control over the mining process.

As described by Sompolinsky and Zohar, GHOST solves the first issue of network security loss by including stale blocks in the calculation of which chain is the "longest"; that is to say, not just the parent and further ancestors of a block, but also the stale descendants of the block's ancestor (in Ethereum jargon, "uncles") are added to the calculation of which block has the largest total proof of work backing it. To solve the second issue of centralization bias, we go beyond the protocol described by Sompolinsky and Zohar, and also provide block rewards to stales: a stale block receives 87.5% of its base reward, and the nephew that includes the stale block receives the remaining 12.5%. Transaction fees, however, are not awarded to uncles.

Ethereum implements a simplified version of GHOST which only goes down one level. Specifically, a stale block can only be included as an uncle by the direct child of one of its direct siblings, and not any block with a more distant relation. This was done for several reasons. First, unlimited GHOST would include too many complications into the calculation of which uncles for a given block are valid. Second, unlimited GHOST with compensation as used in Ethereum removes the incentive for a miner to mine on the main chain and not the chain of a public attacker. Finally, calculations show that single-level GHOST has over 80% of the benefit of unlimited GHOST, and provides a stale rate comparable to the 2.5 minute Litecoin even with a 40-second block time. However, we will be conservative and still retain a Primecoin-like 60-second block time because individual blocks may take a longer time to verify.


### Ethereum Client P2P Protocol

**P2P Protocol**
![SPV in bitcoin](https://www.ethereum.org/gh_wiki/minerchart.png)

The Ethereum client P2P protocol is a fairly standard cryptocurrency protocol, and can just as easily be used for any other cryptocurrency; the only modification is the introduction of the GHOST protocol described above. The Ethereum client will be mostly reactive; if not provoked, the only thing the client will do by itself is have the networking daemon maintain connections and periodically send a message asking for blocks whose parent is the current block. However, the client will also be more powerful. Unlike bitcoind, which only stores a limited amount of data about the blockchain, the Ethereum client will also act as a fully functional backend for a block explorer.

When the client reads a message, it will perform the following steps:

1. Hash the data, and check if the data with that hash has already been received. If so, exit.
2. Determine the data type. If the data is a transaction, if the transaction is valid add it to the local transaction list, process it onto the current block and publish it to the network. If the data item is a message, respond to it. If the data item is a block, go to step 3.
3. Check if the parent of the block is already stored in the database. If it is not, exit.
4. Check if the proof of work on the block header and all block headers in the "uncle list" is valid. If any are not, exit.
5. Check if every block header in the "uncle list" in the block has the block's parent's parent as its own parent. If any is not, exit. Note that uncle block headers do not need to be in the database; they just need to have the correct parent and a valid proof of work. Also, make sure that uncles are unique and distinct from the parent.
6. Check if the timestamp of the block is at most 15 minutes into the future and that it is ahead of the timestamp of the parent. Check if the difficulty of the block and the block number are correct. If either of these checks fails, exit.
7. Start with the state of the parent of the block, and sequentially apply every transaction in the block to it. At the end, add the miner rewards. If the root hash of the resulting state tree does not match the state root in the block header, exit. If it does, add the block to the database and advance to the next step.
8. Determine TD(block) ("total difficulty") for the new block. TD is defined recursively by TD(genesis_block) = 0 and TD(B) = TD(B.parent) + sum([u.difficulty for u in B.uncles]) + B.difficulty. If the new block has higher TD than the current block, set the current block to the new block and continue to the next step. Otherwise, exit.
9. If the new block was changed, apply all transactions in the transaction list to it, discarding from the transaction list any that turn out to be invalid, and rebroadcast the block and those transactions to the network.

The "current block" is a pointer maintained by each node that refers to the block that the node deems as representing the current official state of the network. All messages asking for balances, contract states, etc, have their responses computed by looking at the current block. If a node is mining, the process is only slightly changed: while doing all of the above, the node also continuously mines on the current block, using its transaction list as the transaction list of the block.


### Currency and Issuance

The Ethereum network includes its own built-in currency, ether. The main reason for including a currency in the network is twofold. First, like Bitcoin, ether is rewarded to miners so as to incentivize network security. Second, it serves as a mechanism for paying transaction fees for anti-spam purposes. Of the two main alternatives to fees, per-transaction proof of work similar to [Hashcash](http://en.wikipedia.org/wiki/Hashcash) and zero-fee laissez-faire, the former is wasteful of resources and unfairly punitive against weak computers and smartphones and the latter would lead to the network being almost immediately overwhelmed by an infinitely looping "logic bomb" contract. For convenience and to avoid future argument (see the current mBTC/uBTC/satoshi debate), the denominations will be pre-labelled:

* 1: wei
* 10^3: lovelace
* 10^6: babbage
* 10^9: turing
* 10^12: szabo
* 10^15: finney
* 10^18: ether

This should be taken as an expanded version of the concept of "dollars" and "cents" or "BTC" and "satoshi" that is intended to be future proof. In the near future, we expect "ether" to be the primary unit in the system, much like the dollar or bitcoin; "finney" will likely be used for microtransactions, "szabo" for per-step fees and "wei" to refer specifically to the lowest-level "base" unit much like the satoshi in Bitcoin. The remaining denominations will likely only come into play if Ethereum grows much larger or computers get much more efficient and it makes sense to use lower units than szabo to measure fees.

The issuance model will be as follows:

* Ether will be released in a fundraiser at the price of 1000-2000 ether per BTC, with earlier funders getting a better price to compensate for the increased uncertainty of participating at an earlier stage. The minimum funding amount will be 0.01 BTC. Suppose that X ether gets released in this way
* 0.225X ether will be allocated to the fiduciary members and early contributors who substantially participated in the project before the start of the fundraiser. This share will be stored in a time-lock contract; about 40% of it will be spendable after one year, 70% after two years and 100% after 3 years.
* 0.05X ether will be allocated to a fund to use to pay expenses and rewards in ether between the start of the fundraiser and the launch of the currency
* 0.225X ether will be allocated as a long-term reserve pool to pay expenses, salaries and rewards in ether after the launch of the currency
* 0.4X ether will be mined per year forever after that point


| Group  | After 1 year | After 5 years
| ------------- | ------------- |-------------|
| Currency units  | 1.9X  |  3.5X |
| Fundraiser participants  | 52.6%  | 28.6% |
| Fiduciary members and early contributors | 11.8% | 6.42% |
| Additional pre-launch allocations | 2.63% | 1.42% |
| Reserve | 11.8% | 6.42% |
| Miners | 21.1% | 57.1% |
	 	 

**Long-Term Inflation Rate (percent)**

![SPV in bitcoin](https://www.ethereum.org/gh_wiki/inflation.svg)

_Despite the linear currency issuance, just like with Bitcoin over time the inflation rate nevertheless tends to zero_


For example, after five years and assuming no transactions, 28.6% of the ether will be in the hands of the fundraiser participants, 6.42% in the fiduciary member and early contributor pool, 6.42% paid to the reserve pool, and 57.1% will belong to miners. The permanent linear inflation model reduces the risk of what some see as excessive wealth concentration in Bitcoin, and gives individuals living in present and future eras a fair chance to acquire currency units, while at the same time retaining a strong incentive to obtain and hold ether because the inflation "rate" still tends to zero over time (eg. during year 1000001 the money supply would increase from 500001.5 * X to 500002 * X, an inflation rate of 0.0001%). Furthermore, much of the interest in Ethereum will be medium-term; we predict that if Ethereum succeeds it will see the bulk of its growth on a 1-10 year timescale, and supply during that period will be very much limited.

We also theorize that because coins are always lost over time due to carelessness, death, etc, and coin loss can be modeled as a percentage of the total supply per year, that the total currency supply in circulation will in fact eventually stabilize at a value equal to the annual issuance divided by the loss rate (eg. at a loss rate of 1%, once the supply reaches 40X then 0.4X will be mined and 0.4X lost every year, creating an equilibrium).


### Data Format

All data in Ethereum will be stored in [recursive length prefix encoding](https://github.com/ethereum/wiki/wiki/%5BEnglish%5D-RLP), which serializes arrays of strings of arbitrary length and dimension into strings. For example, ['dog', 'cat'] is serialized (in byte array format) as [ 130, 67, 100, 111, 103, 67, 99, 97, 116]; the general idea is to encode the data type and length in a single byte followed by the actual data (eg. converted into a byte array, 'dog' becomes [ 100, 111, 103 ], so its serialization is [ 67, 100, 111, 103 ]. Note that RLP encoding is, as suggested by the name, recursive; when RLP encoding an array, one is really encoding a string which is the concatenation of the RLP encodings of each of the elements. Additionally, note that block number, timestamp, difficulty, memory deposits, account balances and all values in contract storage are integers, and Patricia tree hashes, root hashes, addresses, transaction list hashes and all keys in contract storage are strings. The main difference between the two is that strings are stored as fixed-length data (20 bytes for addresses, 32 bytes for everything else), and integers take up only as much space as they need. Integers are stored in big-endian base 256 format (eg. 32767 in byte array format as [ 127, 255 ]).

A full block is stored as:

    [
        block_header,
        transaction_list,
        uncle_list 
    ]

Where:

    transaction_list = [
        transaction 1,
        transaction 2,
        ...
    ]

    uncle list = [
        uncle_block_header_1,
        uncle_block_header_2,
        ...
    ]

    block_header = [
        number,
        prevhash,
        sha3(rlp_encode(uncle_list)),
        coinbase address,
        state_root,
        sha3(rlp_encode(transaction_list)),
        difficulty,
        oplimit,
        timestamp,
        extra_data,
        nonce
    ]

Each transaction and uncle block header is itself a list. The data for the proof of work is the RLP encoding of the block WITHOUT the nonce. `uncle_list` and `transaction_list` are the lists of the uncle block headers and transactions in the block, respectively. `difficulty` is a parameter used to calibrate mining, and `oplimit` is a parameter used to calibrate the operation count limit of a block. `nonce` and `extra_data` are both limited to a maximum of 32 bytes, except the genesis block where the `extra_data` parameter will be much larger.

The `state_root` is the root of a [Merkle Patricia tree](https://github.com/ethereum/wiki/wiki/%5BEnglish%5D-Patricia-Tree) containing (key, value) pairs for all accounts where each address is represented as a 20-byte binary string. At the address of each account, the value stored in the Merkle Patricia tree is a string which is the RLP-serialized form of an object of the form:

    [ balance, nonce, contract_root ]

The nonce is the number of transactions made from the account, and is incremented every time a transaction is made. The purpose of this is to (1) make each transaction valid only once to prevent replay attacks, and (2) to make it impossible (more precisely, cryptographically infeasible) to construct a contract with the same hash as a pre-existing contract. `balance` refers to the account's balance, denominated in wei. `contract_root` is the root of yet another Patricia tree, containing the contract's memory, if that account is controlled by a contract. If an account is not controlled by a contract, the contract root will simply be the empty string.

### Mining algorithm

One highly desirable property in mining algorithms is resistance to optimization through specialized hardware. Originally, Bitcoin was conceived as a highly democratic currency, allowing anyone to participate in the mining process with a CPU. In 2010, however, much faster miners exploiting the rapid parallelization offered by graphics processing units (GPUs) rapidly took over, increasing network hashpower by a factor of 100 and leaving CPUs essentially in the dust. In 2013, a further category of specialized hardware, application-specific integrated circuits (ASICs) outcompeted the GPUs in turn, achieving another 100x speedup by using chips fabricated for the sole purpose of computing SHA256 hashes. Today, it is virtually impossible to mine without first purchasing a mining device from one of these companies, and some people are concerned that in 5-10 years' time mining will be entirely dominated by large centralized corporations such as AMD and Intel.

To date, the main way of achieving this goal has been "memory-hardness", constructing proof of work algorithms that require not only a large number of computations, but also a large amount of memory, to validate, thereby making highly parallelized specialized hardware implementations less effective. There have been several implementations of memory-hard proof of work, all of which have their flaws:

* **Scrypt** - Scrypt is a function which is designed to take 128 KB of memory [to compute](https://litecoin.info/User:Iddo/Comparison_between_Litecoin_and_Bitcoin#SHA256_mining_vs_scrypt_mining). The algorithm essentially works by filling a memory array with hashes, and then computing intermediate values and finally a result based on the values in the memory array. However, the 128 KB parameter is a very weak threshold, and ASICs for Litecoin are [already under development](https://axablends.com/merchants-accepting-bitcoin/litecoin-discussion/litecoin-scrypt-asic-miners/). Furthermore, there is a natural limit to how much memory hardness with Scrypt can be tweaked up to achieve, as the verification process takes just as much memory, and just as much computation, as one round of the mining process.
* **Birthday attacks** - the idea behind birthday-based proofs of work is simple: find values xn, i, j such that i < k, j < k and abs(H(data+xn+i) - H(data+xn+j)) < 2^256 / d^2. The d parameter sets the computational difficulty of finding a block, and the k parameter sets the memory hardness. Any birthday algorithm must somehow store all computations of H(data+xn+i) in memory so that future computations can be compared against them. Here, computation is memory-hard, but verification is memory-easy, allowing for extreme memory hardness without compromising the ease of verification. However, the algorithm is problematic for two reasons. First, there is a time-memory tradeoff attack where users 2x less memory can compensate with 2x more computational power, so its memory hardness is not absolute. Second, it may be easy to build specialized hardware devices for the problem, especially once one moves beyond traditional chip and processor architecture and into various classes of hardware-based hash tables or probabilistic analog computing.
* **Dagger** - the idea behind [Dagger](https://github.com/ethereum/wiki/wiki/%5BEnglish%5D-Dagger), an in-house algorithm developed by the Ethereum team, is to have an algorithm that is similar to Scrypt, but which is specially designed so that each individual nonce only depends on a small portion of the data tree that gets built up for each group of ~10 million nonces. Computing nonces with any reasonable level of efficiency requires building up the entire tree, taking up over 100 MB of memory, whereas verifying a nonce only takes about 100 KB. However, Dagger-style algorithms are vulnerable to devices that have multiple computational circuits sharing the same memory, and although this threat can be mitigated it is arguably impossible to fully remove.

One of the key ingredients in a standard cryptocurrency is the idea of proof of work. A proof of work, in general, if a function which is hard to compute, but easy to verify, allowing it to serve as a probabilistic cryptographic proof of the quantity of computational resources controlled by a given node. In Bitcoin, and Ethereum, this mechanism is intimately tied in with the blockchain: every block requires a proof of work of some prespecified difficulty level in order to be valid, and in the event of multiple competing blockchains the chain with the largest total quantity of proof of work is considered to be valid. Thus, in order to reverse a transaction, an attacker needs to start a new fork of the blockchain from before the block the transaction was confirmed in, and then apply more computational power than the rest of the network combined in order to overtake the legitimate fork.

However, one of the key requirements for a proof of work algorithm to, well, work is decentralization. If the proof of work algorithm is designed in such a way that the proof of work computation can only be efficiently done by entities with the millions of dollars of capital required to develop specialized hardware, then the number of participants in the process will be small enough that it will be possible for the majority of miners to conspire and reverse transactions. Alternatively, if the proof of work algorithm encourages miners to outsource their block verification work to centralized entities, then that is another way that centralization can creep in, and it would be the mining pools that have the potential to form conspiracies. Ideally, proof of work algorithms should solve both of those problems.

#### Blockchain Based PoW Specification

This mining algorithm is based on Adam Back's Hashcash, also used by Bitcoin. In Hashcash, we take a hash function `H` (assume 256-bit length) which takes as input data and a nonce and a difficulty parameter, and say that a valid nonce is one where `H(data,nonce) < 2^256 / difficulty`. Completing the proof of work essentially entails trying different nonces until one works, and verifying means applying the hash function to the data and nonce provided and making sure that the result is indeed below the target. In Bitcoin, `H` is a simple computation of `sha256(block_header + nonce)`. Here, however, `H` is much more complex, taking in as data not just the block header but also the state data and transactions from the last 16 blocks.

In this early sketch of the mining algorithm, `H` is defined as follows:

1. Let `h[i] = sha3(sha3(block_header) ++ nonce ++ i)` for `1 <= i <= 16`
2. Let `S` be the blockchain state 16 blocks ago.
3. Let `C[i]` be the transaction count of the block `i` blocks ago. Let `T[i]` be the `(h[i] mod C[i])`th transaction from the block `i` blocks ago.
4. Apply `T[0]`, `T[1]` … `T[15]` sequentially to `S`. However, every time the transaction leads to processing a contract, (pseudo-)randomly make minor modifications to the code of all contracts affected.
5. Let `S'` be the resulting state. Let `r` be the sha3 of the root of `S'`.

#### Properties

1. The algorithm is memory-hard; mining requires the miner to store the full state of the last 16 blocks in order to be able to query the blockchain. However, the algorithm is not sequentially memory-hard and is vulnerable to shared memory optimizations.
2. The algorithm requires every node to store the entire blockchain state and be able to process transactions. Furthermore, as new blocks are created, it will likely be more efficient to generate the new blockchain state by computing it rather than by asking for and downloading the missing Patricia tree nodes. Hence, there is no reason why a miner would not want to be a full node.
3. The EVM code is Turing-complete. Hence, an ASIC that performs transaction processing vastly more efficiently than existing CPUs would necessarily be a general-purpose computing device vastly more efficient  than existing CPUs. Thus, if you can make an Ethereum ASIC, you can push the entire computing industry forward by about 5 years.
4. Because every miner must have the full blockchain, there is no equivalent to the Bitcoin mining strategy of only downloading headers from a centralized source. Hence, centralized mining pools offer no benefits over p2pools, and so miners are more likely to use p2pools.
5. The algorithm is relatively computationally quick to verify, although there is no "nice" verification formula that can be run inside EVM code.

(3) and (4) combined basically mean that this proof of work algorithm provides both forms of decentralization, and (2) helps prevent centralization due to blockchain bloat since it helps ensure that at least miners will store the chain.

In the above given form, the algorithm has several faults. First, due to the Pareto effect, it is likely that over 90% of transactions will use up less than 10% of the blockchain, so a truly full node will not be required to mine. Second, some targeted optimizations involving executing contracts and caching results may be possible. Finally, arguably most importantly, a deep 51% attack becomes possible, where attackers start hundreds of blocks back, produce 16 blocks with transactions optimized for themselves to process quickly (eg. obfuscated loops, weakened trapdoor functions), and then mine much faster than the legitimate chain. For the last two reasons, we expect that very heavy use (perhaps even dominance) of contract corruption/randomization will be necessary in the final version of the algorithm.

### Difficulty adjustment

Difficulty is adjusted by the formula:

    D(genesis_block) = GENESIS_DIFF
    D(block) =
        if anc(block,1).timestamp >= anc(block,501).timestamp + 60 * 500: D(block.parent) - floor(D(block.parent) / 1000)
        else:                                                             D(block.parent) + floor(D(block.parent) / 1000)

`anc(block,n)` is the nth generation ancestor of the block; all blocks before the genesis block are assumed to have the same timestamp as the genesis block. This stabilizes around a block time of 60 seconds automatically. The choice of 500 was made in order to balance the concern that for smaller values miners with sufficient hashpower to often produce two blocks in a row would have the incentive to provide an incorrect timestamp to maximize their own reward and the fact that with higher values the difficulty oscillates too much; with the constant of 500, simulations show that a constant hashpower produces a variance of about +/-20%.

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

`BLK_LIMIT_FACTOR` and `EMA_FACTOR` are constants defined below, and can be changed pending further economic analysis.

### Transactions

A transaction is stored as:

    [ NONCE, VALUE, GASPRICE, RECEIVING_ADDRESS, MAXGAS, DATA, v, r, s ]

The transaction data fields are defined as follows:

* `NONCE` is the number of transactions already sent by that account, encoded in binary form (eg. 0 -> '', 7 -> '\x07', 1000 -> '\x03\xd8').
* `VALUE` is the amount of wei to send
* `GASPRICE` is the amount of wei that the transaction sender is willing to pay as a fee to the miner for each unit of gas consumed. "Gas" is used to pay for computational steps in contract execution.
* `MAXGAS` is the maximum amount of gas that the transaction sender is willing to consume
* `DATA` is an arbitrarily sized byte-array into which the transaction sender can put data; some contracts will use this field
* `(v,r,s)` is the raw Electrum-style signature of the transaction without the signature made with the private key corresponding to the sending account, with 0 <= v <= 3. From an Electrum-style signature (65 bytes) it is possible to extract the public key, and thereby the address, directly.

A valid transaction is one where the following are true:

1. the signature is well-formed (ie. 0 <= v <= 3, 0 <= r < P, 0 <= s < N, 0 <= r < P - N if v >= 2)
2. the sending account has at least `GAS * GASPRICE + len(tx) * DATAGAS * GASPRICE + VALUE` wei, where `len(tx)` is the length of the RLP of the transaction in bytes and `DATAGAS` is an adjustable parameter.
3. The nonce given is actually the number of transactions already made by the sending account

Transaction execution works as follows:

1. Check if the transaction is valid.
2. Subtract `GAS * GASPRICE + len(tx) * DATAGAS * gasprice + value` wei from the sender account and add `value` to the receiver account (if nonexistent, create a new one)
3. Increment the nonce.
4. If the receiving account includes a contract, execute the contract code.
5. If the contract execution exits successfully, with `g` gas remaining, return `g * gasprice` to the sender and add `(gas - g) * gasprice` to the miner's account. If contract execution runs out of gas, revert all changes to the state except for the initial subtraction of wei from the sender's account and the nonce increment, and then add `gas * gasprice` to the miner's account (ie. the sender pays for all execution and the miner receives their reward anyway). 

Details on exactly how contract execution works will be given in the next section.

## Contracts

In Ethereum, there are two types of entities that can generate and receive transactions: actual people (or bots, as cryptographic protocols cannot distinguish between the two) and contracts. A contract is essentially an automated agent that lives on the Ethereum network, has an Ethereum address and balance, and can send and receive transactions. A contract is "activated" every time someone sends a transaction to it, at which point it runs its code, perhaps modifying its internal state or even sending some transactions, and then shuts down. The "code" for a contract is executed in a special-purpose low-level virtual machine ("EVM") consisting of a stack of 32-byte values, which is not persistent, an expandable byte array for memory, which is also not persistent, and 2^256 storage entries (also 32-byte values) which constitute the contract's permanent state. Note that Ethereum users will not need to code in this low-level stack language; we are providing a simple [C-Like language](https://github.com/ethereum/wiki/wiki/%5BEnglish%5D-CLL) with variables, expressions, conditionals, arrays and while loops, and provide a compiler down to EVM code.

### How to make contracts

A contract making transaction is encoded as follows:

    [ nonce, value, gasprice, [ dat0, dat1 ... ], v, r, s ]

The data items will, in most cases, be script codes (more on this below). Contract creation transaction validation happens as follows:

1. Check that the sending account has at least `gasprice * DATAGAS + len(dat) * SSTOREGAS` gas where `len(dat)` is the number of nonzero data items. Subtract this amount from the sending account balance.
2. Check that the nonce is valid, and increment the nonce in the sending account.
3. To calculate the account address, take the last 20 bytes of the sha3 hash of the transaction.
4. Add a new account to the state tree, and add the data items to the appropriate indices in its storage (expanding integers to 32 bytes)

### How do contracts work?

As described above, when a miner wants to process a transaction, the algorithm involves first performing a few validity checks, then subtracting the total fee from the sender, then executing the contract, and then finally refunding excess fees to the sender and providing the required fees to the miner. The middle step, that of executing the contract, is by far the most complex. Executing the contract consists of applying an operation called a "message call", which we will define in much more detail below. Intuitively, a message call takes as input a state tree, an origin, a sender, a starting amount of gas, a receiver, a wei value and a data field, and processes that transaction. If there is no contract at the receiving account, then it will simply transfer the wei and exit; if there is a contract, then it will run through the process of executing the contract code.

First, we need to define three types of state, at different levels of scope, and the corresponding update functions.

1. Between blocks, the state is defined by the latest block. The update function at this level is `APPLY_TXS(BLOCK, TX_LIST, UNCLE_LIST, TIMESTAMP) -> BLOCK'`
2. Inside of a block but between transactions, the state is defined by the triple of `(STATE_TREE, GAS_CONSUMED, BLOCK_HEADER)`, where `BLOCK_HEADER` is immutable. The update function at this level is `APPLY_TX(STATE_TREE, GAS_CONSUMED, TX) -> (STATE_TREE', GAS_CONSUMED')` with `BLOCK_HEADER` available as queryable data.
3. Inside of a transaction but between operations, the state is defined by the tuple `(STATE_TREE, GAS_CONSUMED, BLOCK_HEADER, TX, MSG, MEMORY, STACK, PC, GAS)` (and a call stack in the event of recursion, but this can be ignored for now), where `GAS_CONSUMED`, `BLOCK_HEADER, `TX` and `MSG`` are immutable (`TX` refers to the current transaction and `MSG` refers to the current message). The update function is `APPLY_OP(I, BLOCK_HEADER, TX, MSG, STATE_TREE, MEMORY, STACK, PC, GAS) -> (STATE_TREE', MEMORY', STACK', PC', GAS', EXIT)`, where `EXIT` is either null (ie. execution can continue, or it is an array, in which case the intent is for message call processing to stop and return that value.

We now also define a few convenience operations:

* `balance(STATE_TREE, ADDRESS) -> VALUE` returns the balance of the given `ADDRESS` in the given `STATE_TREE`
* `update_balance(STATE_TREE, ADDRESS, VALUE) -> STATE_TREE'` returns a new state tree with the balance of the given `ADDRESS` in the given `STATE_TREE` set to the given `VALUE`
* `add_balance(STATE_TREE, ADDRESS, VALUE) -> STATE_TREE'` returns a new state tree with the balance of the given `ADDRESS` in the given `STATE_TREE` increased by the given `VALUE`
* `storage(STATE_TREE, ADDRESS, INDEX) -> VALUE` returns the item in the storage of `ADDRESS` at the specified `INDEX` in the given `STATE_TREE`
* `update_storage(STATE_TREE, ADDRESS, INDEX, VALUE) -> STATE_TREE'` returns the item in the storage of `ADDRESS` at the specified `INDEX` in the given `STATE_TREE`
* `del(STATE_TREE, ADDRESS) -> STATE_TREE` deletes the entire account at the given `ADDRESS` from the `STATE_TREE`

`update_storage` and `update_balance` automatically create new accounts if they are used to modify an account that does not yet exist.

`MSGCALL` can be defined as a function which takes as input `( BLOCK_HEADER, TX, STATE_TREE, SENDER, STARTGAS, RECEIVER, VALUE, IN_DATA )`, and outputs `( STATE_TREE', OUT_DATA, ENDGAS, STATUS )`. The steps for computing `MSGCALL` can be defined as follows:

1. Let `ST0 = STATE_TREE`
2. If `ST0.balance[SENDER] < VALUE`, return `(ST0, [], 0, FAIL)`
3. `ST1 = add_balance(STATE_TREE, SENDER, -VALUE)`
4. `ST2 = add_balance(ST1, SENDER, -VALUE)`
5. Set `CURSTATE = (S2, [], [], 0, STARTGAS)`
6. Loop:
    * Let `(ST, M, S, PC, GAS) = CURSTATE`
    * Let `I = STG.storage(RECEIVER, PC)`
    * If `I` is an invalid instruction, or if there are not enough elements in the stack to process `I`, exit and return `(ST, [], GAS, SUCCESS)`
    * Let `FEE` be the cost of executing instruction `I`
    * If `GAS < FEE`, exit and return `(ST0, [], 0, FAIL)`
    * Define MSG as the set of (SENDER, RECEIVER, VALUE, DATA) provided by the message.
    * Execute `NEWSTATE, EXITDATA = (NEW_ST, NEW_MEMORY, NEW_STACK, NEW_PC, NEW_GAS), EXITDATA = APPLY_OP(BLOCK_HEADER, TX, MSG, I, CURSTATE)`
    * If `EXITDATA` is not null, exit and return `(NEW_ST, EXITDATA, NEW_GAS, SUCCESS)`
    * Otherwise, set `CURSTATE = NEWSTATE`

### Defining APPLY_OP

The `APPLY_OP` command is essentially the core of the EVM. The precise steps taken depend primarily on the instruction that is processed. Nearly all operations consume some quantity of gas, take one or more elements off the top of the stack, and push zero or one values to the stack. In the below descriptions, instructions are given in the form `0xYZ: OP (n gas, -x +y) - do something` where `YZ` represents the number code for the instruction, `n` is its gas price, `x` is the number of items it pops off the stack, `y` is the number of items it pushes and `OP` is the name of the operation. `S[i]` for `1 <= i <= x` represents the ith item at the top of the stack before the operation and `S'[j]` for `1 <= j <= y` represents the jth item at the top of the stack after the operation. `M` and `M'` represent the old and new memory, `PC` and `PC'` represent the old and new program counter, `GAS` and `GAS'` represent the old and new gas and `ST` and `ST'` represent the old and new state tree. The phrase "stops execution and returns [ ... ]" informally means to stop execution and provide the given array as a return value, and formally in the above framework means to set `EXIT = [ ... ]`. "Exits" means "stops execution and returns an empty array"

For example, consider the operation `ADD`:

* 0x01 (1 gas, -2 +1) ADD - `S'[0] = S[2] + S[1] mod 2^256` to the stack

Suppose the initial conditions are:

    ST: (state tree)
    M: []
    S: [ 12, 45, 728289, 20 ]
    PC: 5
    GAS: 100

The steps are as follows:

1. Set `GAS' = GAS - 1`
2. Set `PC' = PC + 1`
3. Let `S' = S[:2] + [0]` where `S[:2]` is shorthand for "`S` without the last two elements" and the `[0]` is a dummy element which will be replaced by the operation below.
4. Apply the main operation; in this case, set `S'[0] = S[2] + S[1] mod 2^256` to `S'`

The final state would be:

    ST: (state tree)
    M: []
    S: [ 12, 45, 728309 ]
    PC: 6
    GAS: 99
    EXIT: null

### List of operations

**Arithmetic and logic**

* 0x00 (-0 +0) **STOP** - stops execution and returns an empty array
* 0x01 (-2 +1) **ADD** - `S'[0] = S[0] + S[1] mod 2^256`
* 0x02 (-2 +1) **MUL** - `S'[0] = S[0] * S[1] mod 2^256`
* 0x03 (-2 +1) **SUB** - `S'[0] = S[0] - S[1] mod 2^256`
* 0x04 (-2 +1) **DIV** - `S'[0] = floor(S[0] / S[1])`. If `S[1] = 0`, exits.
* 0x05 (-2 +1) **SDIV** - `S'[0] = floor(S[0] / S[1])` but treating values above 2^255 - 1 as negative (ie. x -> 2^256 - x). Divisions involving negative values are done as in Python (eg. `-7 / -3 = 2`, `-7 / 3 = -3`, `7 / -3 = -3`). If `S[1] = 0`, exits.
* 0x06 (-2 +1) **MOD** - `S'[0] = S[0] mod S[1]`. If `S[1] = 0`, exits. 
* 0x07 (-2 +1) **SMOD** - `S'[0] = S[0] mod S[1]`, treating values above 2^255 -1 as in SDIV. With negative numbers: `-7 % -3 = -1`, `-7 % 3 = 2`, `7 % -3 = -2`. If `S[1] = 0`, exits.
* 0x02 (-2 +1) **EXP** - `S'[0] = S[0] ^ S[1] mod 2^256`
* 0x09 (-1 +1) **NEG** - `S'[0] = 2^256 - S[0]`
* 0x0a (-2 +1) **LT** - `S'[0] = 1 if S[0] < S[1] else 0`
* 0x0b (-2 +1) **GT** - `S'[0] = 1 if S[0] > S[1] else 0`
* 0x0c (-2 +1) **EQ** - `S'[0] = 1 if S[0] == S[1] else 0`
* 0x0d (-2 +1) **NOT** - `S'[0] = 1 if S[0] == 0 else 0`

**Bitwise operations**

* 0x10 (-2 +1) **AND** - `S'[0]` = bitwise and of `S[0]` and `S[1]`
* 0x11 (-2 +1) **OR** - `S'[0]` = bitwise or of `S[0]` and `S[1]`
* 0x12 (-2 +1) **XOR** - `S'[0]` = bitwise xor of `S[0]` and `S[1]`
* 0x12 (-2 +1) **BYTE** - `S'[0]` = `S[0]`th byte of `S[1]` if `S[0] < 32` else `0`

**Special crypto operations**

* 0x20 (20 gas, -2 +1) **SHA3** - `S'[0] = SHA3(M[S[0] ... S[0]+S[1]-1])`

**Message and transaction data operations**

* 0x30 (-0 +1) **ADDRESS** - `S'[0] = MSG.RECEIVER`
* 0x31 (-0 +1) **BALANCE** - `S'[0] = ST.balance(MSG.RECEIVER)`
* 0x32 (-0 +1) **ORIGIN** - `S'[0] = TX.SENDER`
* 0x33 (-0 +1) **CALLER** - `S'[0] = MSG.SENDER`
* 0x34 (-0 +1) **CALLVALUE** - `S'[0] = MSG.VALUE`
* 0x35 (-1 +1) **CALLDATALOAD** - `S'[0] = MSG.DATA[S[1] ... S[1] + 31]`, with out-of-bounds bytes defined as zero
* 0x36 (-1 +1) **CALLDATASIZE** - `S'[0] = len(MSG.DATA)`
* 0x37 (-1 +1) **GASPRICE** - `S'[0] = TX.GASPRICE`

**Block data operations**

* 0x40 (-0 +1) **PREVHASH** - `S'[0] = BLOCK_HEADER.PREVHASH`
* 0x41 (-0 +1) **COINBASE** - `S'[0] = BLOCK_HEADER.COINBASE`
* 0x42 (-0 +1) **TIMESTAMP** - `S'[0] = BLOCK_HEADER.TIMESTAMP`
* 0x43 (-0 +1) **NUMBER** - `S'[0] = BLOCK_HEADER.NUMBER`
* 0x44 (-0 +1) **DIFFICULTY** - `S'[0] = BLOCK_HEADER.DIFFICULTY`
* 0x45 (-0 +1) **GASLIMIT** - `S'[0] = BLOCK_HEADER.GASLIMIT`

**Stack, memory and execution path operations**

* 0x50 (-0 +1) PUSH - `S'[0] = ST.storage(MSG.RECEIVER, PC + 1)`, `PC' = PC + 2`
* 0x51 (-1 +0) POP - does nothing except popping the top item off the stack
* 0x52 (-1 +2) DUP - `S'[0] = S'[1] = S[0]`
* 0x53 (-2 +2) SWAP - `S'[0] = S[1]`, `S'[1] = S[0]`
* 0x54 (-1 +1) MLOAD - `S'[0] = M[S[0] ... S[0]+31]`
* 0x55 (-2 +0) MSTORE - `len(M') = max(len(M'),S[0]+32)`, then `M'[S[0] ... S[0]+31] = S[1]`
* 0x56 (-2 +0) MSTORE8 - `len(M') = max(len(M'),S[0]+1)`, then `M'[S[0]] = S[1] mod 256`
* 0x57 (-1 +1) SLOAD - `S'[0] = ST.storage(MSG.RECEIVER,S[0])`
* 0x58 (-2 +0) SSTORE - `ST' = ST.update_storage(MSG.RECEIVER,S[0],S[1])`
* 0x59 (-1 +0) JMP - `PC' = S[0]`
* 0x5a (-2 +0) JMPI - `PC' = S[1]` if `S[0] != 0`
* 0x5b (-0 +1) PC - `S'[0] = PC`
* 0x5c (-0 +1) MSIZE - `S'[0] = len(M)`
* 0x5d (-0 +1) GAS - `S'[0] = GAS`

**Meta-operations**

`MSGCALL` can be defined as a function which takes as input `( BLOCK_HEADER, TX, STATE_TREE, SENDER, STARTGAS, RECEIVER, VALUE, IN_DATA )`, and outputs `( STATE_TREE', OUT_DATA, ENDGAS, STATUS )`. The steps for computing `MSGCALL` can be defined as follows:

* 0x60 (-5 +1) CREATE - set `ST'` to be a copy of `ST` with a new contract created with initial balance `S[0]` and storage slots filled by `M[S[1]:S[1]+31], M[S[1]+32:S[1]+63] ... M[S[1]+S[2]*32-32:S[1]+S[2]*32-1]`, and with account `MSG.RECEIVER` losing `S[0]` wei. `S'[0]` becomes the address of the newly created contract.
* 0x61 (-7 +1) CALL - immediately executes `MSGCALL( BLOCK_HEADER, TX, ST, MSG.RECEIVER, S[2], S[0], S[1], M[S[3] ... S[3]+[4]-1] )` and let `(NEW_ST, EXITDATA, NEW_GAS, SUCCESS)` be the output. Let `ST' = NEW_ST`, `GAS' = NEW_GAS`, `S'[0] = SUCCESS` and set `M'[S[5]+S[6]-1] = EXITDATA`, cropping if the output is too long and zero-padding if it is too short.
* 0x62 (-2 +0) RETURN - sets EXIT to `M[S[0] ... S[0]+S[1]-1]`
* 0x7f (-1 +0) SUICIDE - set `ST'` to `ST` but with the `MSG.RECEIVER` contract deleted and with the balance of `S[0]` increased by the `MSG.RECEIVER` contract's value, and sets `EXITDATA = []`. Note: in the event of an A -> B -> A (or A -> A) recursive execution, if A suicides in the inner loop, then the execution in the outer loop will attempt to read the next instruction, but will return zero by default due to the contract's newfound nonexistence, and so execution will stop immediately as well.

### Applications

Here are some examples of Ethereum contracts can be used to actually do. Code examples in detail are provided in the document [here]

### Sub-currencies

Sub-currencies have many applications ranging from currencies representing assets such as USD or gold to company stocks and even currencies with only one unit issued to represent collectibles or smart property. Advanced special-purpose financial protocols sitting on top of Ethereum may also wish to organize themselves with an internal currency. Sub-currencies are surprisingly easy to implement in Ethereum. The key point to understand is that all a currency fundamentally is is a database with one operation: subtract X units from A and give X units to B, with the proviso that (i) X had at least X units before the transaction and (2) the transaction is approved by A. All that it takes to implement a sub-currency is to write it as a contract.

The basic code for implementing a currency looks as follows:

    from = msg.sender
    to = msg.data[0]
    value = msg.data[1]

    if contract.storage[from] < value:
        stop

    contract.storage[from] = contract.storage[from] - value
    contract.storage[to] = contract.storage[to] + value

A few extra lines of code need to be added to provide for the initial step of distributing the currency units in the first place and a few other edge cases, and ideally a function would be added to let other contracts query for the balance of an address. But that's all there is to it.

Ethereum sub-currency developers may also wish to add some other more advanced features:

* Include a mechanism by which people can buy currency units in exchange for ether, perhaps auctioning off a set number of units every day.
* Allow transaction fees to be paid in the internal currency, and then refund the ether transaction fee to the sender. This solves one major problem that all other "sub-currency" protocols have had to date: the fact that sub-currency users need to maintain a balance of sub-currency units to use and units in the main currency to pay transaction fees in. Here, a new account would need to be "activated" once with ether, but from that point on it would not need to be recharged.
* Allow for a trust-free decentralized exchange between the currency and ether. Note that trust-free decentralized exchange between any two contracts is theoretically possible in Ethereum even without special support, but special support will allow the process to be done about ten times more cheaply.

### Financial derivatives

Financial derivatives are the most common application of a "smart contract", and one of the simplest to implement in code. The main challenge in implementing financial contracts is that the majority of them require reference to an external price ticker; for example, a very desirable application is a smart contract that hedges against the volatility of ether (or another cryptocurrency) with respect to the US dollar, but doing this requires the contract to know what the value of ETH/USD is. The simplest way to do this is through a "data feed" contract maintained by a specific party (eg. NASDAQ) designed so that that party has the ability to update the contract as needed, and providing an interface that allows other contracts to send a message to that contract and get back a response that provides the price.

Given that critical ingredient, the hedging contract would look as follows:

1. Wait for party A to input 1000 ether.
2. Wait for party B to input 1000 ether.
3. Record the USD value of 1000 ether, calculated by querying the data feed contract, in storage, say this is $x.
4. After 30 days, allow A or B to "reactivate" the contract in order to send $x worth of ether (calculated by querying the data feed contract again to get the new price) to A and the rest to B.

The potential that this kind of contract offers is massive. One of the main problems cited about cryptocurrency is the fact that it's volatile; although many merchants want to accept Bitcoin payments, and many individuals like the idea of having a form of asset that they can control using the tools of cryptocurrency (signed transactions, multisig, smart contracts, hardware wallets, etc) they are uncomfortable with having to face that prospect of losing 23% of the value of their funds in a single day. Up until now, the most commonly provided solution has been issuer-backed assets; the idea is that an issuer creates a sub-currency where they have the right to issue and revoke units, and provide one unit of the currency to anyone who provides them (offline) with one unit of a specified non-cryptographic asset (eg. gold, USD). The issuer then promises to provide one unit of the underlying asset to anyone who sends back one unit of the crypto-asset. In more sophisticated versions, the issuer sells the non-cryptographic asset in exchange for some cryptographic asset (eg. BTC) at the going rate and then provides this to the sender directly; this option appeals to those who have no interest in the asset itself and simply want its price exposure. Assuming the issuer can be trusted, either of these mechanisms essentially creates a cryptographic asset whose value is guaranteed to closely track the value of the underlying asset.

Financial derivatives provide an alternative. Here, instead of a single issuer providing the funds to back up an asset, a decentralized market of speculators plays that role. Speculators are forced to actually provide the funds in the event that the contract ends up unfavorable to them by means of a smart contract which actually holds the funds in escrow, and they benefit because they believe strongly that the value of the cryptoasset will increase and want to hold it at more than 1x leverage.

However, this is not fully decentralized, since some point of trust is needed; specifically, the price ticker. However, this still has several substantial advantages over the issuer model. First, price tickers are low-infrastructure and depend on much less institutional support; while issuers are often stymied by banks' unwillingness to allow crypto-to-fiat exchanges to exist on their platform, this mechanism relies on no banks. Second, the potential for fraud is smaller - price ticker operators can earn some profit by falsifying prices, but they cannot simply run away with everyone's money. Third, price tickers can be at least partially decentralized. Taking the median or three or five is an obvious protocol, and a more detailed proposal called [SchellingCoin](http://blog.ethereum.org/2014/03/28/schellingcoin-a-minimal-trust-universal-data-feed/) creates what may be the closest thing to an anonymous highly-multiparty solution with very little trust required.

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

DAOs and DACs have already been the topic of a large amount of interest among cryptocurrency users as a future form of economic organization, and we are very excited about the potential that DAOs can offer. In the long term, the Ethereum fund itself intends to transition into being a fully self-sustaining DAO.

### Further Applications

1. **Savings wallets**. Suppose that Alice wants to keep her funds safe, but is worried that she will lose or someone will hack her private key. She puts ether into a contract with Bob, a bank, as follows:
    * Alice alone can withdraw a maximum of 1% of the funds per day.
    * Bob alone can withdraw a maximum of 1% of the funds per day, but Alice has the ability to make a transaction with her key shutting off this ability.
    * Alice and Bob together can withdraw anything.

Normally, 1% per day is enough for Alice, and if Alice wants to withdraw more she can contact Bob for help. If Alice's key gets hacked, she runs to Bob to move the funds to a new contract. If she loses her key, Bob will get the funds out eventually. If Bob turns out to be malicious, then she can turn off his ability to withdraw.

2. **Crop insurance**. One can easily make a financial derivatives contract but using a data feed of the weather instead of any price index. If a farmer in Iowa purchases a derivative that pays out inversely based on the precipitation in Iowa, then if there is a drought, the farmer will automatically receive money and if there is enough rain the farmer will be happy because their crops would do well.

3. **A decentralized data feed**. The "Schellingcoin" protocol basically works as follows: N parties all put into the system the value of a given datum (eg. the ETH/USD price), the values are sorted, and everyone between the 25th and 75th percentile gets one token ("schell") as a reward. Everyone has the incentive to provide the answer that everyone else will provide, and the only value that a large number of players can realistically agree on is the obvious default: the truth. This creates a decentralized protocol that can theoretically provide any number of values, including the ETH/USD price, the temperature in Berlin or even the result of a particular hard computation.

4. **Smart multisignature escrow**. Bitcoin allows multisignature transaction contracts where, for example, three out of a given five keys can spend the funds. Ethereum allows for more granularity; for example, four out of five can spend everything, three out of five can spend up to 10% per day, and two out of five can spend up to 0.5% per day. Additionally, Ethereum multisig is asynchronous - two parties can register their signatures on the blockchain at different times and the last signature will automatically send the transaction.

5. **Peer-to-peer gambling**. Any number of peer-to-peer gambling protocols, such as Frank Stajano and Richard Clayton's [Cyberdice](http://www.cl.cam.ac.uk/~fms27/papers/2008-StajanoCla-cyberdice.pdf), can be implemented on the Ethereum blockchain. The simplest gambling protocol is actually simply a contract for difference on the next block hash. From there, entire gambling services such as SatoshiDice can be replicated on the blockchain either by creating a unique contract per bet or by using a quasi-centralized contract.

Ultimately, the best P2P gambling implementation may be something like JustDice, where the contract has two ways of interacting with it. First, users can make bets, and receive either N*x or 0 depending on whether or not they won. Second, users can "invest" into the site, and receive shares where, as long as no investors enter or leave, each share is worth a constant percentage of the contract's total reserve. For example, if a contract has a reserve of 50000 ETH and 1000 shares, and someone invests 5000 ETH, they get 100 shares. If the contract then takes a few bets and its total reserve goes up from 55000 ETH to 66000 ETH, then each of the 1100 shares is now worth 60 ETH, so if the investor sells their shares the contract goes down to 60000 ETH and 1000 shares. The reserve provides the "float" for the contract to cover sudden unexpected losses, but in general the investors are expected to win as the odds are slightly in favor of the contract.

6. A full-scale **on-chain stock market**. Prediction markets are also easy to implement as a trivial consequence.

7. An **on-chain decentralized marketplace**, using the identity and reputation system as a base.

8. **Decentralized Dropbox**. One setup is to encrypt a file, build a Merkle tree out of it, put the Merkle root into a contract alongside a certain quantity of ether, and distribute the file across some secondary network. Every day, the contract would randomly select a branch of the Merkle tree depending on the block hash, and give X ether to the first node to provide that branch to the contract, thereby encouraging nodes to store the data for the long term in an attempt to earn the prize. This approach allows for nodes to cryptographically prove to the contract that they are still in possession of the file, creating a trust-free mechanism which contracts can use to reward nodes storing the file over time. The advantage of this mechanism is that the user does not need to be online; they can preload the contract, and the contract makes sure that the file is still being stored and makes the appropriate payouts completely independently. If one wants to download any portion of the file, one can use a [micropayment-channel](https://bitcointalk.org/index.php?topic=244656.0)-style contract to download the file from a few nodes a block at a time.

## Parameters

* `DATAGAS` = 100
* `GENESIS_DIFF` = 2^23
* `GENESIS_BLKLIMIT` = 1000000
* `BLK_LIMIT_FACTOR` = 1.5
* `EMA_FACTOR` = 32768

## Conclusion

The Ethereum protocol's design philosophy is in many ways the opposite from that taken by many other cryptocurrencies today. Other cryptocurrencies aim to add complexity and increase the number of "features"; Ethereum, on the other hand, takes features away. The protocol does not "support" multisignature transactions, multiple inputs and outputs, hash codes, lock times or many other features that even Bitcoin provides. Instead, all complexity comes from a universal, Turing-complete scripting language, which can be used to build up literally any feature that is mathematically describable through the contract mechanism. As a result, we have a protocol with unique potential; rather than being a closed-ended, single-purpose protocol intended for a specific array of applications in data storage, gambling or finance, Ethereum is open-ended by design, and we believe that it is extremely well-suited to serving as a foundational layer for a very large number of both financial and non-financial protocols in the years to come.


## References and Further Reading

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
