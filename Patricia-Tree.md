### Merkle Patricia Tree Specification

Merkle Patricia trees provide a cryptographically authenticated data structure that can be used to store all (key, value) bindings, although for the scope of this paper we are restricting keys and values to strings (to remove this restriction, just use any serialization format for other data types). They are fully deterministic, meaning that a Patricia tree with the same (key,value) bindings is guaranteed to be exactly the same down to the last byte and therefore have the same root hash, provide the holy grail of O(log(n)) efficiency for inserts, lookups and deletes, and are much easier to understand and code than more complex comparison-based alternatives like red-black trees.

### Preamble: Basic Radix Trees

In a basic radix tree, every node looks as follows:

    [i0, i1 ... in, value]

Where i0 ... in represent the symbols of the alphabet (often binary or hex). value is the terminal value at the node, and the values in the i0 ... in slots are either NULL or pointers to (in our case, hashes of) other nodes. This forms a basic (key, value) store; for example, if you are interested in the value that is currently mapped to dog in the tree, you would first convert dog into the alphabet (giving `64 6f 67`), and then descend down the tree following that path until at the end of the path you read the value. That is, you would first look up the root hash in a flat key/value DB to find the root node of the trie (which is basically an array of keys to other nodes), use the value at index `6` as a key (and look it up in the flat key/value DB) to get the node one level down, then pick index `4` of that to lookup the next value, then pick index `6` of that, and so on, until, once you followed the path: `root -> 6 -> 4 -> 6 -> 15 -> 6 -> 7`, you look up the value of the node that you have and return the result.

Note there is a difference between looking something up in the the "trie" vs the underlying flat key/value "DB". They both define key/values arrangements, but the underlying DB can do a traditional 1 step lookup of a key, while looking up a key in the trie requires multiple underlying DB lookups to get to the final value as described above. To eliminate ambiguity, let's refer to the latter as a `path`.

The update and delete operations for radix trees are simple, and can be defined roughly as follows:

    def update(node,path,value):
        if path == '':
            curnode = db.get(node) if node else [ NULL ] * 17
            newnode = curnode.copy()
            newnode[-1] = value
        else:
            curnode = db.get(node) if node else [ NULL ] * 17
            newnode = curnode.copy()
            newindex = update(curnode[path[0]],path[1:],value)
            newnode[path[0]] = newindex
        db.put(hash(newnode),newnode)
        return hash(newnode)

    def delete(node,path):
        if node is NULL:
            return NULL
        else:
            curnode = db.get(node)
            newnode = curnode.copy()
            if path == '':
                newnode[-1] = NULL
            else: 
                newindex = delete(curnode[path[0]],path[1:])
                newnode[path[0]] = newindex

            if len(filter(x -> x is not NULL, newnode)) == 0:
                return NULL
            else:
                db.put(hash(newnode),newnode)
                return hash(newnode)

The "Merkle" part of the radix tree arises in the fact that a deterministic cryptographic hash of a node is used as the pointer to the node (for every lookup in the key/value DB `key == sha3(rlp(value))`, rather than some 32-bit or 64-bit memory location as might happen in a more traditional tree implemented in C. This provides a form of cryptographic authentication to the data structure; if the root hash of a given tree is publicly known, then anyone can provide a proof that the tree has a given value at a specific path by providing the nodes going up each step of the way. It is impossible for an attacker to provide a proof of a (path, value) pair that does not exist since the root hash is ultimately based on all hashes below it, so any modification would change the root hash.

While traversing a path 1 nibble at a time as described above, most nodes contain a 17-element array. 1 index for each possible value held by the next hex character (nibble) in the path, and 1 to hold the final target value in the case that the path has been fully traversed. These 17-element array nodes are called `branch` nodes.

### Main specification: Merkle Patricia Tree

However, radix trees have one major limitation: their inefficiency. If you want to store just one (path,value) binding where the path is (in the case of the ethereum state trie), 64 characters long (number of nibbles in `bytes32`), you will need over a kilobyte of extra space to store one level per character, and each lookup or delete will take the full 64 steps. The Patricia tree introduced here solves this issue.

### Optimization

Merkle Patricia trees solve the inefficiency issue by adding some extra complexity to the data structure. A node in a Merkle Patricia tree is one of the following:

1. `NULL` (represented as the empty string)
2. `branch` A 17-item node `[ v0 ... v15, vt ]`
3. `leaf` A 2-item node `[ encodedPath, value ]`
4. `extension` A 2-item node `[ encodedPath, key ]`

With 64 character paths it is inevitable that after traversing the first few layers of the trie, you will reach a node where no divergent path exists for at least part of the way down. It would be naive to require such a node to have empty values in every index (one for each of the 16 hex characters) besides the target index (next nibble in the path). Instead we shortcut the descent by setting up a `extension` node of the form `[ encodedPath, key ]`, where `encodedPath` contains the "partial path" to skip ahead (using compact encoding described below), and the `key` is for the next db lookup.

In the case of a `leaf` node, which can be determined by a flag in the first nibble of `encodedPath`, the situation above occurs and also the "partial path" to skip ahead completes the full remainder of a path. In this case `value` is the target value itself.

The optimization above however introduces some ambiguity.

When traversing paths in nibbles, we may end up with an odd number of nibbles to traverse, but because all data is stored in `bytes` format, it is not possible to differentiate between, for instance, the nibble `1`, and the nibbles `01` (both must be stored as `<01>`). To specify odd length, the partial path is prefixed with a flag.

### Specification: Compact encoding of hex sequence with optional terminator

The flagging of both *odd vs. even remaining partial path length* and *leaf vs. extension node* as described above reside in the first nibble of the partial path of any 2-item node. They result in the th following:

    hex char    bits    |    node type partial     path length
    ----------------------------------------------------------
       0        0000    |       extension              even        
       1        0001    |       extension              odd         
       2        0010    |   terminating (leaf)         even        
       3        0011    |   terminating (leaf)         odd         

For even remaining path length (`0` or `2`), another `0` "padding" nibble will always follow.

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

    > [ 1, 2, 3, 4, 5, ...]
    '11 23 45'
    > [ 0, 1, 2, 3, 4, 5, ...]
    '00 01 23 45'
    > [ 0, f, 1, c, b, 8]
    '20 0f 1c b8'
    > [ f, 1, c, b, 8]
    '3f 1c b8'



Here is the extended code for getting a node in the Merkle Patricia tree:

    def get_helper(node,path):
        if path == []: return node
        if node = '': return ''
        curnode = rlp.decode(node if len(node) < 32 else db.get(node))
        if len(curnode) == 2:
            (k2, v2) = curnode
            k2 = compact_decode(k2)
            if k2 == path[:len(k2)]:
                return get(v2, path[len(k2):])
            else:
                return ''
        elif len(curnode) == 17:
            return get_helper(curnode[path[0]],path[1:])

    def get(node,path):
        path = []
        for i in range(len(path)):
            path2.push(int(ord(path) / 16))
            path2.push(ord(path) % 16)
        path2.push(16)
        return get_helper(node,path2)

Example: Suppose we want a trie containing the path/value pairs  ('do', 'verb'), ('dog', 'puppy'), ('doge', 'coin'), ('horse', 'stallion'). First, we convert all paths to `bytes` denoted by `<>` (note: the *values* would also be stored as `bytes` but we will leave them as strings here for comprehension):

    <64 6f> : 'verb'
    <64 6f 67> : 'puppy'
    <64 6f 67 65> : 'coin'
    <68 6f 72 73 65> : 'stallion'

Now, we build such a trie with the following key/value pairs in the underlying DB:

    rootHash: [ <16>, hashA ]
    hashA:    [ <>, <>, <>, <>, hashB, <>, <>, <>, hashC, <>, <>, <>, <>, <>, <>, <>, <> ]
    hashC:    [ <20 6f 72 73 65>, 'stallion' ]
    hashB:    [ <00 6f>, hashD ]
    hashD:    [ <>, <>, <>, <>, <>, <>, hashE, <>, <>, <>, <>, <>, <>, <>, <>, <>, verb ]
    hashE:    [ <17>, hashF ]
    hashF:    [ <>, <>, <>, <>, <>, <>, hashG, <>, <>, <>, <>, <>, <>, <>, <>, <>, puppy ]
    hashG:    [ <35>, 'coin' ]


Where a node is referenced inside a node, what is included is H(rlp.encode(x)) where H(x) = sha3(x) if len(x) >= 32 else x and rlp.encode is the [RLP](https://github.com/ethereum/wiki/wiki/RLP) encoding function. Note that when updating a tree, you will need to store the key/value pair (sha3(x), x) in a persistent lookup table when you create a node with length >= 32, but if the node is shorter than that then you do not need to store anything when length < 32 for the obvious reason that the function f(x) = x is reversible.