<<<<<<< HEAD
_Note: The light client protocol is under development. See the following links for current state of the protocol specification._

* https://github.com/zsfelfoldi/go-ethereum/wiki/Light-Ethereum-Subprotocol-%28LES%29
* https://github.com/zsfelfoldi/go-ethereum/wiki/Client-Side-Flow-Control-model-for-the-LES-protocol

***

=======
---
name: Light Client Protocol
category: 
---
>>>>>>> b14c975a3152e2312735fd0f93b838a16161bc25

The purpose of the light client protocol is to allow users in low-capacity environments (embedded smart property environments, smartphones, browser extensions, some desktops, etc) to maintain a high-security assurance about the current state of some particular part of the Ethereum state or verify the execution of a transaction. Although full security is only possible for a full node, the light client protocol allows light nodes processing about 1KB of data per 2 minutes to receive data from the network about the parts of the state that are of concern to them, and be sure that the data is correct  provided that the majority of miners are correctly following the protocol, and perhaps even only provided that at least one honest verifying full node exists.

### Background: Patricia Merkle Trees

All substantial quantities of data in Ethereum are stored in a data structure known as the [Patricia Merkle tree](https://github.com/ethereum/wiki/wiki/%5BEnglish%5D-Patricia-Tree), a tree structure where each node in the tree is the hash of its children. Each set of key/value pairs maps to a unique root hash, and only a small subset of nodes is needed to prove that a particular key/value combination is in the tree corresponding to a particular root hash.

![](http://vitalik.ca/files/spv.png)

The size complexity of a Merkle proof scales linearly with the height of a tree; because each child in a tree has a particular number of children (in our case, up to 17), this means that the size complexity of a Merkle proof is logarithmic in the quantity of data stored. This means that, even if the entire state tree is a few gigabytes in size, if a node receives a state root from a trusted source that node has the ability to know with full certainty the validity of any information with the tree by only downloading a few kilobytes of data in a proof.

An SPV proof of a node in a Patricia tree simply consists of the complete subset of tree nodes that were processed in order to access it (or, more specifically, the tree nodes that needed to be looked up in a reverse-hash-lookup database). In a simple implementation of a Patricia tree, retrieving the value associated with a particular key requires descending the hash tree, constantly looking up nodes in the database by their hashes, until you eventually reach the final leaf node; a simple algorithm for producing an SPV proof is to simply run this naive algorithm, and record all of the database lookups that were made. SPV verification consists of running the naive lookup algorithm but pointing it to a custom database populated only with the nodes in the SPV proof; if there is a "node not found" error, then the proof is invalid. 

## Principles

In Ethereum, a light client can be viewed as a client that downloads block headers by default, and verifies only a small portion of what needs to be verified, using a distributed hash table as a database for trie nodes in place of its local hard drive. For a "partially light client", which processes everything but is constrained by hard drive space and so stores almost nothing, swapping out a database read with a DHT get request is by itself sufficient to meet the requirements. Indeed, all "full clients" except for archive nodes (intended to be run by businesses, block explorers, etc) will eventually be set up as "partially light clients" with respect to all history older than a few thousand blocks. However, what we are also interested in supporting is _fully_ light clients, which never even process most transactions. Formally, we can say that _all_ measures of a full light client are bounded by a sublinear function of the number of transactions in a block - in most cases, the protocols below work for a bound of O(log(n)), though one particular mechanism works only for ~O(sqrt(n)).

Some use cases for a fully light client, and how the light client meets those use cases, include:

* A light client wants to know the state of an account (nonce, balance, code or storage index) at a particular time. The light client can simply recursively download trie nodes from the state root until it gets to the desired value.
* A light client wants to check that a transaction was confirmed. The light client can simply ask the network for the index and block number of that transaction, and recursively download transaction trie nodes to check for availability.
* Light clients want to collectively validate a block. Each light client `C[i]` chooses one transaction index `i` with transaction `T[i]` (with corresponding receipt `R[i]`) and does the following:
    * Initiate the state with state root `R[i-1].medstate` and `R[i-1].gas_used` (if `i = 0` use the parent endstate and 0 `gas_used`)
    * Process transaction `T[i]`
    * Check that the resulting state root is `R[i].medstate` and the gas_used is `R[i].gas_used`
    * Check that the set of logs and bloom produced matches `R[i].logs` and `R[i].logbloom`
    * Checks that the bloom is a subset of the block header-level bloom (this detects block header-level blooms with false negatives); then pick a few random indices of the block header-level bloom where that bloom contains a 1 and ask other nodes for a transaction-level bloom that contains a 1 at that index, rejecting the block if no response is given (this detects block header-level blooms with false positives)
* Light clients want to "watch" for events that are logged. The protocol here is the following:
    * A light client gets all block headers, checks for block headers that contain bloom filters that match one of a desired list of addresses or topics that the light client is interested in
    * Upon finding a potentially matching block header, the light client downloads all transaction receipts, checks them for transactions whose bloom filters match
    * Upon finding a potentially matching transaction, the light client checks its actual log RLP, and sees if it actually matches

The first three light client protocols require a logarithmic amount of data access and computation; the fourth requires ~O(sqrt(N)) since bloom filters are only a two-level structure, although this can be improved to O(log(N)) if the light client is willing to rely on multiple providers to point to "interesting" transaction indices and decommission providers if they are revealed to have missed a transaction. The first protocol is useful to simply check up on state, and the second in consumer-merchant scenarios to check that a transaction was validated. The third protocol allows Ethereum light clients to collectively validate blocks with a very low degree of trust. In Bitcoin, for example, a miner can create a block that gives the miner an excessive amount of transaction fees, and there would be no way for light nodes to detect this themselves, or upon seeing an honest full node detect it verify a proof of invalidity. In Ethereum, if a block is invalid, it must contain an invalid state transition at some index, and so a light client that happens to be verifying that index can see that something is wrong, either because the proof step does not check out, or because data is unavailable, and that client can then raise the alarm.

The fourth protocol is useful in cases where a dapp wants to keep track of some kind of events that need to be efficiently verifiable, but which do not need to be part of the permanent state; an example is a decentralized exchange logging trades or a wallet logging transactions (note that the light client protocol will need to be augmented with header-level coinbase and uncle checks for this to work fully with mining accounts). In Bitcoin terminology, `LOG` can be viewed as a pure "proof of publication" opcode.