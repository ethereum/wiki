---
name: Patricia Tree
category: 
---

### Merkle Patricia Tree Specification

Merkle Patricia trees provide a cryptographically authenticated data structure that can be used to store all (key, value) bindings, although for the scope of this paper we are restricting keys and values to strings (to remove this restriction, just use any serialization format for other data types). They are fully deterministic, meaning that a Patricia tree with the same (key,value) bindings is guaranteed to be exactly the same down to the last byte and therefore have the same root hash, provide the holy grail of O(log(n)) efficiency for inserts, lookups and deletes, and are much easier to understand and code than more complex comparison-based alternatives like red-black trees.

### Preamble: Basic Radix Trees

In a basic radix tree, every node looks as follows:

    [ value, i0, i1 ... in]

Where i0 ... in represent the symbols of the alphabet (often binary or hex). value is the terminal value at the node, and the values in the i0 ... in slots are either NULL or pointers to (in our case, hashes of) other nodes. This forms a basic (key, value) store; for example, if you are interested in the value that is currently mapped to dog in the tree, you would first convert dog into the alphabet (giving 646f67 if we're using hex), and then descend down the tree following that path until at the end of the path you read the value. That is, you would first look up the root hash in a key/value store to get the root node, then look up node 6 of the root node to get the node one level down, then look up node 4 of that, then look up node 6 of that, and so on, until, once you followed the path root -> 6 -> 4 -> 6 -> f -> 6 -> 7, you look up the value of the node that you have and return the result.

The update and delete operations for radix trees are simple, and can be defined roughly as follows:

    def update(node,key,value):
        if key == '':
            curnode = db.get(node) if node else [ NULL ] * 17
            newnode = curnode.copy()
            newnode['value'] = value
        else:
            curnode = db.get(node) if node else [ NULL ] * 17
            newnode = curnode.copy()
            newindex = update(curnode[key[0]],key[1:],value)
            newnode[key[0]] = newindex
        db.put(hash(newnode),newnode)
        return hash(newnode)

    def delete(node,key):
        if key == '' or node is NULL:
            return NULL
        else:
            curnode = db.get(node)
            newnode = curnode.copy()
            newindex = delete(curnode[key[0]],key[1:])
            newnode[key[0]] = newindex
            if len(filter(x -&gt; x is not NULL, newnode)) == 0:
                return NULL
            else:
                db.put(hash(newnode),newnode)
                return hash(newnode)

The "Merkle" part of the radix tree arises in the fact that a deterministic cryptographic hash of a node is used as the pointer to the node, rather than some 32-bit or 64-bit memory location as might happen in a more traditional tree implemented in C. This provides a form of cryptographic authentication to the data structrure; if the root hash of a given trie is publicly known, then anyone can provide a proof that the trie has a given value at a specific key by providing the nodes going up each step of the way. it is impossible for an attacker to provide a proof of a (key, value) pair that does not exist since the root hash is ultimately based on all hashes below it, so any modification would change the root hash.

However, radix trees have one major limitation: their inefficiency. If you want to store just one (key,value) binding where the key is a few hundred characters long, you will need over a kilobyte of extra space to store one level per character, and each lookup or delete will take hundreds of steps. The Patricia tree introduced here solves this issue.

### Specification: Compact encoding of hex sequence with optional terminator

The traditional compact way of encoding a hex string is to convert it into binary - that is, a string like 0f1248 would become three bytes \[15, 18, 72\] (or in string representation \x0f\x18H). However, this approach has one slight problem: what if the length of the hex string is odd? In that case, there is no way to distinguish between, say, 0f1248 and f1248. Additionally, our application in the Merkle Patricia tree requires the additional feature that a hex string can also have a special &quot;terminator symbol&quot; at the end (denoted by the 'T'). A terminator symbol can occur only once, and only at the end. An alternative way of thinking about this to not think of there being a terminator symbol, but instead treat bit specifying the existence of the terminator symbol as a bit specifying that the given node encodes a final node, where the value is an actual value, rather than the hash of yet another node.

To solve both of these issues, we force the first nibble of the final bytestream to encode two flags, specifying oddness of length (ignoring the 'T' symbol) and terminator status; these are placed, respectively, into the two lowest significant bits of the first nibble. In the case of an even-length hex string, we must introduce a second nibble (of value zero) to ensure the hex-string is even in length and thus is representable by a whole number of bytes. Thus we construct the following encoding:

    def compact_encode(hexarray):
        term = 1 if hexarray[-1] == 16 else 0
        if term: hexarray = hexarray[:-1]
        oddlen = len(hexarray) % 2
        flags = 2 * term + oddlen
        if oddlen:
            hexarray = [flags] + hexarray
        else:
            hexarray = [flags] + [0] + hexarray
        // hexarray now has an even length whose first nibble is the flags.
        o = ''
        for i in range(0,len(hexarray),2):
            o += chr(16 * hexarray[i] + hexarray[i+1])
        return o

Examples:

    &gt; [ 1, 2, 3, 4, 5 ]
    '\x11\x23\x45'
    &gt; [ 0, 1, 2, 3, 4, 5 ]
    '\x00\x01\x23\x45'
    &gt; [ 0, 15, 1, 12, 11, 8, T ]
    '\x20\x0f\x1c\xb8'
    &gt; [ 15, 1, 12, 11, 8, T ]
    '\x3f\x1c\xb8'

### Main specification: Merkle Patricia Tree

Merkle Patricia trees solve the inefficiency issue by adding some extra complexity to the data structure. A node in a Merkle Patricia tree is one of the following:

1. NULL (represented as the empty string)
2. A two-item array `[ key, v ]` (aka kv node)
3. A 17-item array `[ v0 ... v15, vt ]` (aka diverge node)

The idea is that in the event that there is a long path of nodes each with only one element, we shortcut the descent by setting up a kv node `[ key, value ]`, where the key gives the hexadecimal path to descend, in the compact encoding described above, and the value is just the hash of the node like in the standard radix tree. Also, we add another conceptual change: internal nodes can no longer have values, only leaves with no children of their own can; however, since to be fully generic we want the key/value store to be able to store keys like 'dog' and 'doge' at the same time, we simply add a terminator symbol (16) to the alphabet so there is never a value &quot;en-route&quot; to another value.

For a kv node, a two-item array `[ key, v ]`, `v` can be a value or a node. 
* When `v` is a value, key must be the result of compact encoding a nibbles list **with** terminator.
* When `v` is a node, key must be the result of compact encoding a nibbles list **without** terminator.

For a diverge node, a 17-item array `[ v0 ... v15, vt ]`, each item in v0...v15 should always be a node or blank, and vt should always be a value or blank. So to store a value in one item of v0...v15, we should instead store a kv node, where k is the result of compact encoding an empty nibbles list **with** terminator.

Here is the extended code for getting a node in the Merkle Patricia tree:

    def get_helper(node,key):
        if key == []: return node
        if node = '': return ''
        curnode = rlp.decode(node if len(node) < 32 else db.get(node))
        if len(curnode) == 2:
            (k2, v2) = curnode
            k2 = compact_decode(k2)
            if k2 == key[:len(k2)]:
                return get(v2, key[len(k2):])
            else:
                return ''
        elif len(curnode) == 17:
            return get_helper(curnode[key[0]],key[1:])

    def get(node,key):
        key2 = []
        for i in range(len(key)):
            key2.push(int(ord(key) / 16))
            key2.push(ord(key) % 16)
        key2.push(16)
        return get_helper(node,key2)

Example: suppose we had a tree containing the pairs ('dog', 'puppy'), ('horse', 'stallion'), ('do', 'verb'), ('doge', 'coin'). First, we convert the keys over to hex format:

    [ 6, 4, 6, 15, 16 ] : 'verb'
    [ 6, 4, 6, 15, 6, 7, 16 ] : 'puppy'
    [ 6, 4, 6, 15, 6, 7, 6, 5, 16 ] : 'coin'
    [ 6, 8, 6, 15, 7, 2, 7, 3, 6, 5, 16 ] : 'stallion'

Now, we build the tree:

    ROOT: [ '\x16', A ]
    A: [ '', '', '', '', B, '', '', '', C, '', '', '', '', '', '', '', '' ]
    B: [ '\x00\x6f', D ]
    D: [ '', '', '', '', '', '', E, '', '', '', '', '', '', '', '', '', 'verb' ]
    E: [ '\x17', F ]
    F: [ '', '', '', '', '', '', G, '', '', '', '', '', '', '', '', '', 'puppy' ]
    G: [ '\x35', 'coin' ]
    C: [ '\x20\x6f\x72\x73\x65', 'stallion' ]

Where a node is referenced inside a node, what is included is H(rlp.encode(x)) where H(x) = sha3(x) if len(x) >= 32 else x and rlp.encode is the [RLP](https://github.com/ethereum/wiki/wiki/%5BEnglish%5D-RLP) encoding function. Note that when updating a trie, you will need to store the key/value pair (sha3(x), x) in a persistent lookup table when you create a node with length >= 32, but if the node is shorter than that then you do not need to store anything when length < 32 for the obvious reason that the function f(x) = x is reversible.