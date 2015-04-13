The following document describes the long-term vision and roadmap for future Ethereum development toward Serenity and beyond.

The basic design is the culmination of a year of ongoing thinking and evolution of ideas proposed by Vitalik Buterin, Gavin Wood and Vlad Zamfir, and follows the principles of [https://github.com/vbuterin/scalability_paper/raw/master/scalability.pdf](https://github.com/vbuterin/scalability_paper/raw/master/scalability.pdf), with a two-level design where the top level deals only with consensus objects and the second level deals with transactions. The precise specifics will continue to be worked out over the next year, and the release will likely be in multiple stages; however, the general ideas are described below.

### Consensus by Contract

One of the major design goals for the final product of this roadmap is for as much of the consensus logic as possible to be abstracted away and itself written in EVM code; this makes it much easier to deliver upgrades to specific features such as the consensus algorithm in the future, and also means that the amount of work needed to implement Ethereum in multiple languages will decrease drastically as only one implementation is effectively required. The goal is to take the fundamental primitive of an Ethereum execution environment, consisting of the EVM and calling semantics, as a given, and use it as the building block of all other features of Ethereum, including logs, events, and even block execution and validation, including in a scalable context. Each feature will generally be implemented via a specific contract.

### Summary and EVM Alterations

With the introduction of the notion of the EVM (prior to February 2014 it had been only through its instruction set: "Ethereum Script") and its evolution early 2014, it had always been designed towards three specific directions:

- domain specific for crypto, and specifically ECDSA and SHA3-256 (word-size 256-bit);
- ease of implementation: simple, minimalist architecture (e.g. stack over registers);
- no duplication of functionality (e.g. reuse DIV & MUL over bit-shifts).

This work proposes to take the EVM in a wholly new direction. Roughly speaking, our goals are:

- maximal generality;
- maximal efficiency;
- ability to be utilised as the only fundamentally irreplaceable part of a consensus system.

As such, the specific EVM architecture is likely to take a very different shape: SSTORE, SLOAD and CREATE will be gone, as will SHA3, and CALL will be stripped to essentials, essentially providing only a basic ability for call stacks. Interrupt handlers will be introduced and a number of base-level permissions will be introduced to the EVM itself, loosely following from CPU "protection rings".

Hard-wired "virtual peripherals" will manage all crypto operations and database (trie/leveldb) I/O. Interrupt handlers in validated base-level consensus code will allow the highest-level "user" contracts to, through specific "system-level consensus APIs", access these services and live in an environment very similar to that provided by the 1.0 EVM.

The specific instruction set will be modelled closely following a real hardware CPU, possibly to the point of being exactly equivalent; this will depend on our research and how easily our "virtual peripheral" I/O mechanism can be made to work with a piece of real hardware.

#### Anatomy of EVM∞ Architecture

The EVM can be modelled as a CPU:

- **ALU**: standard ALU component of a CPU.
- **CMU**: the CPU should have call management capabilities, effectively allowing it to efficiently switch between hardware "call frames"; each frame is its own execution environment and is otherwise isolated. The CMU is able to report exceptional behaviour in callees to the caller. An exception may be synthesised through a privileged opcode and a non-exceptional return contain data. 
- **MMU**: the execution environment includes a portion of pre-allocated RAM, also a code and input ROM.  Access outside protected bounds are considered exceptional behaviour.
- **Gas Counter**: the CPU maintains an accumulator of all computation done; this will be done in terms most efficiently implementable in hardware without sacrificing determinism. Continued processing on 0 gas is considered exceptional behaviour.
- **Interrupt manager**: the CPU may create an interrupt which immediately executes code in the caller's frame with the caller's permissions. Data may be passed into and out of this piece of code. After non-exceptional execution, control is returned to the interrupter's execution environment. The base level code's interrupt handler includes *real* I/O like storage resource management and time, and depending on the potential efficiency of the EVM∞, precompiled but deterministic algorithms for Trie management, SHA3 hashing, RLP encoding.
- **Permission set**: the set of permissions a CPU has; this may affect the instructions available for usage.

#### Permissions

At present there is only one permission that will likely make it to the final specification of the EVM∞ architecture:

- **Alter Gas Table** The ability to alter the gas pricing table.

## The Microkernel

The microkernel is the code that each individual Ethereum implementation will actually have to implement. Everything other than the microkernel code can be written in EVM code.

The microkernel will have the following components.

### Virtual machine

The VM will have memory, stack, static code and program counter as before, except that special-purpose opcodes like `PREVHASH`, `GASPRICE`, `SLOAD`, etc, will be gone; instead, there will be:

* An `INTERRUPT` opcode which takes a memory start and end index, and creates an interrupt event with that data (see below)
* A set of opcodes for dealing with data structures: `INVSHA3`, `MKTRIE`, `TRIE_SETROOT`, `TRIE_GETROOT`, `TRIE_SET`, `TRIE_GET`, `TRIE_MKREVERT_POINT`, `TRIE_REVERT`, `MKHEAP`, `HEAP_SETROOT`, `HEAP_GETROOT`, `HEAP_SET`, `HEAP_GET`, `HEAP_MKREVERT_POINT`, `HEAP_REVERT`
* Other subjective externality opcodes (eg. `CURTIME`, which returns the current time as viewed from the executing machine's point of view)

There is no first-class notion of accounts, addresses or state, all that exists are [VM execution instances and void](http://en.wikipedia.org/wiki/Democritus). Note that all VM instances have permission sets (eg. hard drive modification, subjective information access); doing anything that one does not have permission to do leads to an immediate zero response.

### The Hard Drive

The hard drive is the temporary name for the part of the system which maintains tries and heaps and reverse hash lookups. The hard drive has the ability to force a `DATA UNAVAILABLE` exception that propagates up to the top and cannot be caught if data is not found. Note that this is subjective: it may well be that one node has the data to process a particular execution and another node does not, but this is not different from the Ethereum 1.0 status quo once light client technology begins to appear.

An example partial python implementation of a hard drive would look as follows:

```python
from ethereum import trie

class HardDrive():
    def __init__(self, db):
        self.db = db
        self.objs = []

    def invsha3(x):
        if x not in db:
            raise Exception("Data unavailable")
        return db.get(x)

    def mktrie(root):
        t = trie.Trie(db)
        t.root_hash = root
        self.objs.append(t)
        return len(self.objs) - 1

    def trie_get_root(id):
        if k >= len(self.objs):
            return 0
        return self.objs[id].root_hash      

    def trie_get(id, k):
        if k >= len(self.objs):
            return 0
        t = self.objs[id]
        try:
            return t.get(k)
        except KeyError:
            raise Exception("Data unavailable")

    def trie_set(id, k, v):
        if k >= len(self.objs):
            return 0
        t = self.objs[id]
        try:
            t.update(k, v)
        except KeyError:
            raise Exception("Data unavailable")
```

A more complete implementation would of course include journaling caches and revert functionality. The VM has the ability to make calls to the hard drive if it has the appropriate permissions.

### Calling and Interrupts

An `EVAL` opcode exists in the VM, and it has the following arguments:

* `PSET` - sets permissions (using a bit mask, eg. 19 = 00010011 = subjective + hard drive + infinite gas (or whatever other permissions)). Note that the permission set must be a subset of the current execution's permissions
* `CSTART`, `CSIZE` - the start memory index and slice length for where to get the code of the child instance
* `ISTART`, `ISIZE` - the start memory index and slice length for where to get the input (equivalent of calldata) for the child instance
* `OSTART`, `OSIZE` - the start memory index and slice length for where to place the output of the child instance
* `GAS` limit
* `NSTART`, `NSIZE` - the start memory index and slice length for where to get the interrupt code

It then creates and runs a child VM instance with the desired code, input, output, interrupt code and gas limit.

The `INTERRUPT` opcode takes a start memory index and slice length for where to get interrupt data and a start memory index and slice length for output. It creates a VM instance with the interrupt code supplied into the VM execution instance, empty input and _the parent's permissions_ and returns output into the provided slice length.

## Wire protocol block structure

We can establish a (potentially mutable) convention that the wire protocol-level block structure will look like the following, in RLP form:

    [ header, obj1, obj2 ... ]

Where `header` is an RLP list and each `obj` is of one of three forms:

* `[v1, v2, v3 ... ]` ("trie list")
* `[[k1, v1], [k2, v2], [k3, v3] ... ]` ("trie")
* `[1, [k1, v1], [k2, v2], [k3, v3] ... ]` ("heap")
* `v` (hashed value)

The first step upon receiving a block will always be to load the hashed values and the tries and heaps into the database. Light clients will have their own logic for fetching data that will typically involve DHT queries.

For collations, we use the same format, except that we only add hashed values which represent trie/heap nodes.

## INTERPRET_BLOCK

`INTERPRET_BLOCK` is the "master contract" of the top-level consensus. `INTERPRET_BLOCK` takes a block header root as input, and returns 1 if the header is valid and 0 if the header is invalid. This is the only immutable component of the top-level consensus; every "full" validator node will run it.

A block header will (for now) consist of an RLP list of four components:

* `prevhash` (the hash of the previous header)
* `valroot` (a trie root consisting of validation data, in the PoS case signatures)
* `objroot` (a trie root consisting of collations, fishermen, evidence and other objects)
* `endstate` (a trie root consisting of the end state)

`INTERPRET_BLOCK` will follow the following steps:

1. Use `INVSHA3` to grab the header from the header root.
2. Use `INVSHA3` to grab the previous header from the prevhash.
3. Initialize a "top-level state" trie with the state root being the previous header's endstate.
4. Initialize a "sig" tree with the state root being `valroot`
5. Check each signature in the sig tree against the validator (found in the main state tree). If this is false, return 0
6. Initialize an "object" tree with the state root being `objroot`
7. Check the signatures on each object, and check the validity of each object, and while processing each object update the state tree with the new root of each substate as needed. Make sure that no two collations have intersecting area, etc. If any check fails, return 0
8. Check that the state trie root is equal to the `endstate`. If not, return 0
9. Return 1

## INTERPRET_COLLATION

`INTERPRET_COLLATION` is in fact more complicated, as it handles actual transaction logic at the bottom level. A collation will be in the following format:

    [ [ [i1, prev1, post1], [i2, prev2, post2] ... ], proposer, proposer_sig, [sig1, sig2 ... ] ]

Where:

* `i[k]`, `prev[k]`, `post[k]` are substate indices and the starting and final state roots for those indices
* `proposer` is the ID of the proposer, `proposer_sig` is the signature from the proposer
* `sig[k]` is the signature from a randomly chosen subset

1. Use `INVSHA3` to grab the header from the header root.
2. For every substate index, create a trie, and initialize it to the `prev` value.
3. For every transaction, first check that the signature is valid. If not, return 0. Then, run the `MSG` subroutine on the transaction.

The `MSG` subroutine consists of (i) creating a revert point for every trie, and (ii) creating and running a VM execution instance with the desired destination, source, code, gas, etc. `SLOAD`, `SSTORE`, `CALL` and `CREATE` are replaced with formatted calls to `INTERRUPT`:

* `SLOAD`: [0, k]
* `SSTORE`: [1, k, v]
* `CALL`: [2, to, gas, data]
* `CREATE`: [3, code]
* `TIMESTAMP`, `DIFFICULTY`, etc

The `INTERRUPT` code handles `SLOAD` and `SSTORE` with queries to the hard drive. It replies to `TIMESTAMP`, `DIFFICULTY`, etc, with queries to the block header, and it forwards `CALL` and `CREATE` instances right back to the `MSG` subroutine. If the VM execution instance exits with an exception, then the `MSG` subroutine finishes off by reverting the tries back to the revert point produced before initializing the instance.

Finally, `INTERPRET_COLLATION` checks to see that the final state roots match up with the `post` values provided, and returns 0 if they are not and 1 if they are.

Addresses become 224 bits long: the bottom 192 bits represent the address as it currently is, and the top 32 represent the subspace index. The code for `SLOAD` and `SSTORE`, as well as grabbing the code for the destination of a call, uses the top 32 bits to determine which trie to use; if the trie is not in the collation then it returns 0.