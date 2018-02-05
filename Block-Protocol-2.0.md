---
name: Block Protocol 2.0
category: 
---

One of the main criticisms that has been made of Ethereum, and Bitcoin-like blockchain protocols in general, is the issue of scalability. Although Bitcoin's core developers, and other platforms such as Ripple, have continued to make iterative improvements to the way that the blockchain is stored, including innovations such as separating the state ("ledger" or "UTXO set") from the transaction list or using separate data structures to store the two on disk/memory, fundamentally no one has successfully implemented any way to improve upon the fundamental limitation that every full node must process every transaction. At this point, Ethereum also does not solve this problem. However, the block protocol described here, and specifically the stack trace mechanism, allows for secure "light nodes" to exist, which download only the headers of every block and not the full transaction set but maintain the same security level for their users as full nodes under the much weaker assumption that at least one node with non-negligible (ie. >0.01%) mining power or ether inside the system is honest.

### Data definitions

A full block is stored as:

    [
        block_header,
        transaction_list,
        uncle_list,
        stack_trace
    ]

Where:

    transaction_list = [
        transaction 0,
        transaction 1,
        ...
    ]

    uncle list = [
        uncle_block_header_1,
        uncle_block_header_2,
        ...
    ]

    block_header = [
        parent hash,
        number,
        TRIEHASH(transaction_list),
        TRIEHASH(uncle_list),
        TRIEHASH(stack_trace),
        coinbase address,
        state_root,
        difficulty,
        timestamp,
        extra_data,
        nonce
    ]

    stack_trace = [
        [ medhash 0, stkhash 0 ],
        [ medhash 1, stkhash 1 ],
        ...
    ]

Each transaction and uncle block header is itself provided directly in the form of a list, not in a serialized form. `uncle_list` and `transaction_list` are the lists of the uncle block headers and transactions in the block, respectively. Note that the block number, difficulty and timestamp are integers, and therefore cannot have leading zeroes; `extra_data` and `nonce` can be byte arrays of at most 32 bytes, NOT lists, although this particular check should not be performed for the genesis block where the `extra_data` field will take up many kilobytes.

The `state_root` is the root of a [Patricia Tree](https://github.com/ethereum/wiki/wiki/%5BEnglish%5D-Patricia-Tree) containing (key, value) pairs for all accounts where each address is represented as a 20-byte binary string. At the address of each account, the value stored in the Merkle Patricia tree is a string which is the RLP-serialized form of an object of the form:

    [ balance, nonce, contract_root ]

The `nonce` is the number of transactions made from the account, and is incremented every time a transaction is made. The purpose of this is to (1) make each transaction valid only once to prevent replay attacks, and (2) to make it impossible (more precisely, cryptographically infeasible) to construct a contract with the same hash as a pre-existing contract. `balance` refers to the account's balance, denominated in wei. `contract_root` is the root of yet another Patricia tree, containing the contract's memory, if that account is controlled by a contract. If an account is not controlled by a contract, the contract root will simply be the empty string.

In order to understand all of what is above, we also need a few function definitions:

    def H(x):
        if len(rlp.encode(x)) < 32: return x
        else: return sha3(rlp.encode(x))

    def H20(x):
        if len(rlp.encode(x)) < 20: return x
        else: return sha3(rlp.encode(x))[:12]

    def adjust_difficulty(pdiff,ptime,ntime):
        if ntime > ptime + 42: return pdiff - int(pdiff / 1024)
        else: return pdiff + int(pdiff / 1024)

    def int_to_bin(n,bytes):
        return '' if bytes == 0 else int_to_bin(int(n / 256),bytes - 1) + chr(n % 256)

    def TRIE(objs):
        t = Trie()
        for i in range(len(objs)):
            t.insert(int_to_bin(i,32),rlp.encode(objs[i]))
        return t
        
    def TRIEHASH(objs):
        return TRIE(objs).root


We also want a few methods for dealing with tries as stacks:

    def TRIELEN(trie):
        i = 0
        while trie.get(int_to_bin(i,32)): i += 1
        return i

    def TRIETOP(trie):
        return trie.get(int_to_bin(TRIELEN(trie)-1,32))

    def TRIEPOP(trie):
        trie.update(int_to_bin(TRIELEN(trie)-1,32),'')

    def TRIEPUSH(trie,node):
        trie.update(int_to_bin(TRIELEN(trie),3),node)

The mining function is tentative, and will be replaced once we know that we have better alternatives:

    def compute_valid_nonce(header):
        header[10] = 0
        while not verify_pow(header):
            header[10] += 1
        return header[10]

    def verify_pow(header):
        return sha3(sha3(header[:10]) + header[10]) * header[7] <= 2**256


### Mining Process

![Mining Process](https://www.ethereum.org/gh_wiki/500px-Minerchart3.png)


When mining a block, a miner goes through the following process:

1- Take as inputs:

* `uncle_headers` to be the list of known unused valid uncle headers
* `timestamp` to be the current timestamp
* `parent` to be the parent block
* `extra_data` to be the extra data desired to be added to the block
* `coinbase` to be the desired coinbase address
* `txlist` to be the list of transactions to be added

2- Set:

* `difficulty = adjust_difficulty(parent.difficulty,timestamp,parent.timestamp)`
* `reward = 15 * 10^18` (tentatively)
* `block_header = [ parent.hash, parent.number + 1, TRIEHASH(txlist), TRIEHASH(uncle_headers), 0, coinbase, 0, difficulty, timestamp, extra_data, 0 ]`

3- Initialize:

* `state` to be the parent block state
* `txstack = TRIE(txlist)`
* `stacktrace = TRIE([])`

4- While `stacktrace.root != ''`:

* `TRIEPUSH(stacktrace,[state.root,txstack.root])`
* Apply the transaction `TRIETOP(txstack)` to `state`.
* `TRIEPOP(txstack)`
* Let `L[0] ... L[m-1]` be the list of new transactions spawned by that transaction via `MKTX`, in the order that they were produced during script execution.
* Initialize `j = m-1`. While `j >= 0`, `TRIEPUSH(txstack,L[j])` and `j -= 1`

5- Make the following modifications to the state tree:

* Increase the balance of `coinbase` by `reward`
* For each uncle `u` in `uncle_headers`, increase the balance of `u.coinbase` by `reward * 13/16` and increase the balance of `coinbase` by `reward * 1/16`

6- Fill in the first two zeroes in `block_header` with `state.root` and `TRIEHASH(stack_trace)`

7- Set `nonce = compute_valid_nonce(block_header)`, and fill in the remaining zero in the block header with this value

### Block Validation Algorithm

![Mining Process](https://www.ethereum.org/gh_wiki/500px-Minerchart2.png)

1- Take as inputs:

* `block` to be the block header
* `uncle_list` to be the block's uncle list
* `transaction_list` to be the block's transaction list
* `stacktrace` to be the block's stack trace
* `now` to be the current time as measured by the miner's CPU

2- Check the following:

* Is there an object, which is a block, in the database with `block.prevhash` as its hash? Let `parent` be that block.
* Is the proof of work on the block valid?
* Is the proof of work on all uncle headers valid?
* Are all uncles unique and actually uncles (ie. children of the parent of the parent, but not the parent)?
* Is `block.timestamp <= now + 900` and is `block.timestamp >= parent.timestamp`?
* Is `block.number == parent.number + 1`?
* Is `block.difficulty == adjust_difficulty(parent.difficulty,timestamp,parent.timestamp)`?
* Is `block.transaction_hash = TRIEHASH(transaction_list)`?
* Is `block.stacktrace_hash = TRIEHASH(stacktrace)`?
* Is `block.uncle_hash = H(uncle_list)`?

3- Initialize:

* `state` to be block parent block's state
* `txstack = TRIE(transaction_list)`
* `i = 0`

4- Let `stacktrace[k] = [ M[k], H[k] ]`, defaulting to '' if `k` is out of bounds. While `i < len(stacktrace)`:

* Check that `state.root == M[i]` and `txstack.root == H[i]`
* Apply `transaction_list[i]` to `state`.
* Apply the transaction `TRIETOP(txstack)` to `state`.
* `TRIEPOP(txstack)`
* Let `L[0] ... L[m-1]` be the list of new transactions spawned by that transaction via `MKTX`, in the order that they were produced during script execution.
* Initialize `j = m-1`. While `j >= 0`, `TRIEPUSH(txstack,L[j])` and `j -= 1`
* Set `i += 1`

5- Make the following modifications to `state`:

* Increase the balance of `coinbase` by `reward`
* For each uncle `u` in `uncle_headers`, increase the balance of `u.coinbase` by `reward * 13/16` and increase the balance of `coinbase` by `reward * 1/16`

6- Check that `txstack.root == ''`

7- Check that `state.root == block.state_root`

8- If any check failed, return FALSE. Otherwise, return TRUE.

If a block is valid, determine TD(block) (&quot;total difficulty&quot;) for the new block. TD is defined recursively by `TD(genesis_block) = 0` and `TD(B) = TD(B.parent) + sum([u.difficulty for u in B.uncles]) + B.difficulty`. If the new block has higher TD than the current block, set the current block to the new block and continue to the next step. Otherwise, exit.

### Semi-collaborative Block Validation Via Challenge-Response Protocol

In Ethereum, a ''light node'' can be defined as a node that accepts block headers, and performs the verifications in (2) with the exception of the transaction and stacktrace trie hash verifications but does not perform the verifications in (4) and (6), similar to the headers-only verification that light nodes do in Bitcoin. Light nodes would thus store the state roots, and perhaps some portion of the state, but not the entire state. If a light node wants to know the balance or contract state of a given account, it can request the value from other nodes in the network alongside the minimal subset of Patricia tree nodes that prove that the given key/value pair is actually in the state.

Although Ethereum cannot run without at least some full nodes processing and verifying every transaction that takes place in the network, this block protocol is designed to provide a somewhat weaker assurance: as long as at least one honest full node exists, light clients can be just as secure and provide the same incentives toward decentralization that full nodes do. The mechanism relies on a challenge-response protocol in which a full node can, subject to certain conditions, submit a challenge that a certain part of a block is invalid, and it would be up to the miner (or another good samaritan node) to provide a response which light nodes can then efficiently verify. If a challenge goes uncontested, then light nodes would distrust the block.

In the validation algorithm described in the previous section, note that there are a few specific places where a block can fail:

1. One of the checks in (2), aside from the transaction list trie hash or stack trace trie hash check, fails.
2. The transaction list trie hash check fails.
3. The stack trace trie hash check fails.
4. One of the checks in (4) fails for `i=0`
5. One of the checks in (4) succeeds for all `i<k` but fails for `i=k`
6. The check in (6) or (7) fails.

(1) is automatically done by light nodes, but the other six require a larger amount of data to properly verify, and so light clients by themselves will not be able to detect such flaws. However, other nodes will, and will be able to submit challenges at any step. Regardless of where a challenge comes in, if the block is correct another node can construct a proof of localized legitimacy. The set of possible proofs of localized legitimacy has 100% cover; in theory, a large collection of proofs of localized legitimacy can be combined together into a complete proof that a block is valid.

For (2) and (3), in order for a trie hash check to pass it means that two conditions must be met:

1. For all indices `i < k` for some `k`, there must be a valid transaction in the trie at `i`
2. For all indices `i >= k`, everything must be blank.

A challenge of invalidity consists of either (1) an index `i` pointing to a potential invalid transaction, or (2) an index `i` pointing to a blank space and an index `j > i` pointing to a transaction. A response should contain (1) `i`, (2) `j` if applicable, (3) the subset of Patricia tree nodes needed to verify the values at `i` and `j` in the trie.

For (5), a challenge of invalidity consists of an index `k` such that everything up to index `k` is valid but index `k` is not. A response consists of (1) `k`, (2) the stack trace entries at `k-1` and `k` along with a subset of Patricia tree nodes to verify them, (3) a subset of Patricia tree nodes from the state tree needed to verify the computation. For (4), a response consists simply of a subset of Patricia tree nodes to prove the stack trace entry at index 0, from which the two components can be checked against the transaction list hash in the block header and the parent block state. For (6), a response functions in the same way as in (4), except verifying the response requires both processing the last transaction and giving the block rewards to the coinbase and the uncles.

### Economics

There is one flaw in the protocol as described above: it is vulnerable to denial of service attacks. Specifically, because challenges are much easier to produce than responses are to produce or verify, it is possible for nodes to pollute the network with false challenges, forcing light nodes to reject every block as other nodes are unable to catch up with verification. To remedy this, we introduce a simple fix: a challenge must be signed by a node that either (1) mined one of the last 10000 blocks, or (2) owns at least 0.01% of all ether. Light nodes would then keep quality scores for all peers, and downgrade peers if they submit a challenge that is successfully countered. Because this method is not anonymous, requiring nodes to submit proof of ownership of an identity tied to the blockchain, it cannot be countered by reconnecting under a new IP address or other such mechanisms.

The protocol altogether can be shown to be incentive-compatible as follows:

1. Miners have the incentive to submit challenges to blocks mined by other nodes because if a challenge is successful and then they mine the next block they will have the opportunity to gain an extra 0.0625X reward by including the header of the invalid block as an uncle.
2. The miner of a block, and all uncles inside the block, at the very least have the incentive to respond to challenges.
3. Light nodes have the incentive to respect the block validity rules because everyone else does, an argument advanced in http://themonetaryfuture.blogspot.ca/2011/07/bitcoin-decentralization-and-nash.html. Without the challenge-response protocol, this argument does not apply because the cost of determining whether or not a block is valid is prohibitively high; with the protocol, this reasoning no longer applies.
4. Substantial stakeholders have the incentive to promote the perceived integrity of the system to maximize the value of their currency units, and thus may want to help actively submit both challenges and responses where possible.
5. Statistically speaking, far more than 0.01% of agents inside of real-world economic agents, whether or not weighted by economic power, tend to be motivated by altruistic/ideological considerations. The market share of charities around the world is sufficient evidence of this.

### Comparison with Bitcoin

In Bitcoin, one can create a challenge-response protocol to achieve similar functionality along very similar principles, but there is one key way in which such a protocol in Bitcoin would be inadequate: miner fees. Because Bitcoin does not include a Merkle-tree mechanism for adding up transaction fees, the only way to prove that a block has a certain quantity of transaction fees is to process every transaction. Furthermore, in Bitcoin transaction fees all go to the miner. Thus, in Bitcoin a light node has no way of knowing if a given block is valid or if it gives its creator excessive fees, and can only rely on the computational majority as a source of information for this. In Ethereum, all changes to the state are incorporated into the stacktrace, so this weakness does not exist and, given the weak security assumption that at least one full node with at least 0.01% mining power or stake is honest, have 100% of the security properties that full nodes have.
