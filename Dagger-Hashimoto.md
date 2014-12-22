Dagger Hashimoto is a proposed spec for the mining algorithm for Ethereum 1.0. Dagger Hashimoto aims to simultaneously satisfy three goals:

1. **ASIC-resistance**: the benefit from creating specialized hardware for the algorithm should be as small as possible, ideally to the point that even in an economy where ASICs have been developed the speedup us sufficiently small that it is still marginally profitable for users on ordinary computers to mine with spare CPU power.
2. **Light client verifiability**: a block should be relatively efficiently verifiable by a light client.
3. **Full chain storage**: mining should require storage of the complete blockchain state (due to the irregular structure of the Ethereum state trie, we anticipate that some pruning will be possible, particularly of some often-used contracts, but we want to minimize this).

Dagger Hashimoto builds on two key pieces of previous work:

* [Hashimoto](http://vaurum.com/hashimoto.pdf), an algorithm by Thaddeus Dryja which intends to achieve ASIC resistance by being IO-bound, ie. making memory reads the limiting factor in the mining process. The theory is that RAM is in principle inherently a much more generic ingredient than computation, and billions of dollars of research already go into optimizing it for different use cases which often involve near-random access patterns (hence "random access memory"); hence, existing RAM is likely to be moderately close to optimal for evaluating the algorithm. Hashimoto uses the blockchain as a source of data, simultaneously satisfying (1) and (3) above.
* [Dagger](http://vitalik.ca/ethereum/dagger.html), an algorithm by Vitalik Buterin which uses directed acyclic graphs to simultaneously achieve memory-hard computation but memory-easy validation. The core principle is that each individual nonce only requires a small portion of a large total data tree, and recomputing the subtree for each nonce is prohibitive for mining - hence the need to store the tree - but okay for a single nonce's worth of verification. Dagger was meant to be an alternative to existing memory-hard algorithms like [Scrypt](http://en.wikipedia.org/wiki/Scrypt), which are memory-hard but are also very hard to verify when their memory-hardness is increased to genuinely secure levels. However, Dagger was proven to be vulnerable to shared memory hardware acceleration [by Sergio Lerner](https://bitslog.wordpress.com/2014/01/17/ethereum-dagger-pow-is-flawed/) and was then dropped in favor of other avenues of research.

Approaches that were tried between Dagger and Dagger Hashimoto but are currently not our primary focus include:

* "Blockchain-based proof of work" - a proof of work function that involves running contracts taken from the blockchain. The approach was abandoned because it was long-range attack vulnerabilities, since attackers can create forks and populate them with contracts that they have a secret fast "trapdoor" execution mechanism for.
* "Random circuit" - a proof of work function developed largely by Vlad Zamfir that involves generating a new program every 1000 nonces - essentially, choosing a new hash function each time, faster than even FPGAs can reconfigure. The approach was temporarily put aside because it was difficult to see what mechanism one can use to generate random programs that would be general enough so that specialization gains would be low; however, we see no fundamental reasons why the concept cannot be made to work.

The difference between Dagger Hashimoto and Hashimoto is that, instead of using the blockchain as a data source, Dagger Hashimoto uses a custom-generated 1 GB data set, which updates based on block data every N blocks. The data set is generated using the Dagger algorithm, allowing for the efficient calculation of a subset specific to every nonce for the light client verification algorithm. The difference between Dagger Hashimoto and Dagger is that, unlike in the original Dagger, the dataset used to query the block is semi-permanent, only being updated at occasional intervals (eg. once per week). This means that the portion of the effort that goes toward generating the dataset is close to zero, so Sergio Lerner's arguments regarding shared memory speedups become negligible.

The code for the algorithm will be defined in Python below. `encode_int`, `cantor_pair` and `P` represent the following algorithms and value:

    def encode_int(x):
        "Encode an integer x as a string of 64 characters using a little-endian scheme"
        o = ''
        for _ in range(64):
            o += chr(x % 256)
            x //= 256 
        return o

    P = (2**256 - 4294968273) ** 2

    def cantor_pair(x,y,P):
        return ((x+y) * (x+y+1) / 2 + y) % P

The Cantor pairing function provides a non-commutative, non-associative alternative to functions like OR and XOR, making it more difficult to perform graph-theoretic optimizations. We also assume that `sha3` is a function that takes an integer and outputs an integer; if converting this reference code into an implementation use:

    from pyethereum import utils
    def sha3(x):
        return utils.decode_int(utils.sha3(utils.encode_int(x)))

### Parameters

The parameters used for the algorithm are:

    params = {
      "numdags": 40,          # Number of dags in the dataset
      "n": 250000,            # Size of the dataset
      “h_threshold”: 100000,  # Index threshold at which a complex child evaluation mode turns on
      "diff": 2**14,          # Difficulty (adjusted during block exaluation)
      "epochtime": 1000,      # Length of an epoch in blocks (how often the dataset is updated)
      "k": 2,                 # Number of parents of a node
      "hk": 8,                # Number of parents during complex child evaluation
      "w": 2,                 # Work factor for proof of work during nonce calculations
      "hw": 8,                # Work factor for proof of work during complex evaluation
      "is_serial": 0,         # Is hashimoto modified to be serial?
      "accesses": 40,         # Number of dataset accesses during hashimoto
      "P": (2**256 - 
            4294968273) ** 2, # Number to modulo everything by (determines
                              # byte length and maybe some moduli are more secure than others)
    }   

### Dagger graph building

The Dagger graph building primitive is defined as follows:

    def produce_dag(params, seed):
         P = params["P"]
         o = [sha3(seed) % P]
         init = o[0]
         picker = o[0]
         for i in range(1, params["n"]):
             x = 0
             picker = (picker * init) % P
             curpicker = picker
             for j in range(params["k"]):
                 x = cantor_pair(x, o[curpicker % i], P)
                 curpicker >>= 10
             if pos >= params["h_threshold"]:
                 for j in range(params["hk"]):
                     x = cantor_pair(x, o[picker % params["h_threshold"]], P)
                     curpicker >>= 10
             w = params[“w” if x < params["h_threshold"] else “hw”]
             o.append(pow(x, w, P))  # use any "hash function" here

Essentially, it starts off a graph as a single node, `sha3(seed) % P`, and from there starts sequentially adding on other nodes. When a new node is created, `sha3(seed) ** i % P` (where `i` is the index of the node being created and `P` is a large number, in our case slightly under 2**512) is used as a seed to randomly select some indices less than `i` (using `curpicker % i` above), and the values of the nodes at those indices are used in a calculation to generate a value `x`, which is then fed into a small proof of work function to ultimately generate the value of the graph at index `i`.

The graph as a whole has `n` indices; for indices higher than `h_threshold`, we deliberately make the values harder to generate by (1) increasing the number of children, and (2) increasing the strength of the proof of work.

The intent of this construction is to construct a graph in such a way that each individual node in the graph can be reconstructed by computing a subtree of only a small number of nodes, and making a small amount of auxiliary computation. This subtree should be large enough so that it does not open up an efficient memory-free mining implementation, but small enough so that it is practical for a light client, and ideally even a protocol on the blockchain, to verify it. The `h_threshold` parameter is an adaptation of a mechanism found in the original Dagger; its purpose is to make generating the last "row" of the DAG artificially hard, so as to put a high cost premium on any strategies that store less than the entire dataset, thereby eliminating the potential for middle-of-the-road implementations that store some of the DAG and calculate the rest. The question of whether this increased complexity is best achieved through a higher number of parents or increased work is undecided; both options are left tunable as parameters (`hw` and `hk`).

The light client computing function for the DAG works as follows:

    def quick_calc(params, seed, pos):
      P = params["p"]
      init = sha3(seed)
      known = {0: init}
  
      def calc(p):
          if p not in known:
              picker = pow(init, p, P)
              x = 0
              for j in range(params["k"]):
                  x = cantor_pair(x, calc(picker % p), P)
                  picker >>= 10
              if pos >= params["h_threshold"]:
                  for j in range(params["hk"]):
                      x = cantor_pair(x, calc(picker % params["h_threshold"]), P)
                      picker >>= 10
              w = params["w" if x < params["h_threshold"] else "hw"]
              known[p] = pow(x, w, P)
          return known[p]
  
      return calc(pos)

Essentially, it is simply a rewrite of the above algorithm that removes the loop of computing the values for the entire DAG and replaces the earlier node lookup with a recursive memoized call.

For our dataset, we will use a collection of multiple DAGs produced by the above formula; the benefit of this is that it allows the DAGs to be replaced slowly over time without needing to incorporate a step where miners must suddenly recompute all of the data, leading to an abrupt temporary slowdown in chain processing at regular intervals and dramatically increasing centralization and thus 51% attack risks within those few minutes before all data is recomputed.

The algorithm used to generate the actual set of DAGs used to compute the work for a block is as follows:

    def get_daggerset(params, block):
        if block.number == 0:
            return [produce_dag(params, i) for i in range(params["numdags"])]
        elif block.number % params["epochtime"]:
            return get_daggerset(block.parent)
        else:
            o = get_daggerset(block.parent)
            o[sha3(block.parent.nonce) % params["numdags"]] = \
                produce_dag(params, sha3(block.parent.nonce))
            return o

The above code contains no caching or memoization that would obviously speed up the runtime from `O(n)` to `O(1)`; in a live implementation keeping track of daggersets for efficient access is the correct approach and is easy to do.

Essentially, every time we hit a multiple of `epochtime`, we modify one DAG. We take the nonce of the parent block as a selector for which DAG to modify, and then also use it as a seed to determine the new DAG to replace it with. Light clients can keep up easily, as they need only keep track of the set of seeds corresponding to the DAGs. For this, we have:

    def get_seedset(params, block):
        if block.number == 0:
            return range(params["numdags"])
        elif block.number % params["epochtime"]:
            return get_seedset(block.parent)
        else:
            o = get_seedset(block.parent)
            o[sha3(block.parent.nonce) % params["numdags"]] = \
                sha3(block.parent.nonce)
            return o

Repeatedly modifying the dataset is important; otherwise read-only memory with the data per-shipped in hardware becomes a powerful ASIC. The modification time should be sufficiently large that the DAG production requirement does not lead to centralization risk, but minimally small so that specialized "mining farm with built-in factory" operations that print out a new ROM every time the DAG changes do not become viable.

**The goal with all of the above is to create a data set which is self-updating and light-client verifiable, without the light client verification algorithm opening up any new specialization vulnerabilities that are not present in the original Hashimoto.**

### Hashimoto

The idea behind the original Hashimoto is to use the blockchain as a dataset, performing a computation which selects N indices from the blockchain, gathers the transactions at those indices, performs an XOR of this data, and returns the hash of the result. Thaddeus Dryja's original algorithm, translated to Python for consistency, is as follows:

    def orig_hashimoto(prev_hash, merkle_root, list_of_transactions, nonce):
        hash_output_A = sha256(prev_hash + merkle_root + nonce) 
        txid_mix = 0
        for i in range(64):
            shifted_A = hash_output_A >> i 
            transaction = shifted_A % len(list_of_transactions) 
            txid_mix ^= list_of_transactions[transaction] << i 
        return txid_max ^ (nonce << 192)

(Reminding non-mathematicians that ^ in programming languages is, confusingly enough, XOR, not exponentiation)

We modify the algorithm to use the Dagger dataset:

    def hashimoto(daggerset, params, header, nonce):
        rand = sha3(header+encode_int(nonce))
        mix = 0
        for i in range(params["accesses"]):
            shifted_A = (rand ^ mix * params["is_serial"]) >> i
            dag = daggerset[shifted_A % params["numdags"]]
            mix ^= dag[(shifted_A // params["numdags"]) % params["n"]]
        return mix ^ rand

Note that we also add an `is_serial` parameter to offer the option of making the hashimoto computation non-parallelizable; if activated, this works by adding the current "mix" from earlier indices into the calculation of which later indices to use. Another possible modification is to use the Cantor pairing function in place of XOR, although we note that this heavily increases the computational load of the algorithm.

Here is a light-client friendly version, using functions defined above:

    def light_hashimoto(seedset, params, header, nonce):
        rand = sha3(header+encode_int(nonce))
        mix = 0
        for i in range(40):
            shifted_A = (rand ^ mix * params["is_serial"]) >> i
            seed = seedset[shifted_A % params["numdags"]]
            # can further optimize with cross-round memoization
            mix ^= quick_calc(params, seed,
                               (shifted_A // params["numdags"]) % params["n"])
        return mix ^ rand

To make the algorithm require blockchain storage, we simply add one additional round of access which uses the current state as a database. The reason why the original hashimoto does not suffice for light client friendliness is that the validation process would require 64 Merkle tree proofs, amounting to up to 50kb of data for each block - perhaps light enough for a smartphone, but not an internet-of-things device. Here, we intend to achieve simultaneous light client friendliness and blockchain storage requirement by pursuing the two goals separately - once with a Dagger-generated dataset, and the second time by making a single blockchain access.

First, let's define the primitive; code described here should work as is with pyethereum.

    def get_state(block, header, nonce):
        # Generate a random position
        pos = encode_int(sha3(header+encode_int(nonce)) % 2**160)

        # Locate next address in state with index after that position
        # and get the account binary data from that address.
        # If there is nothing, we set i to the maximum address
        i = block.state.next(pos) or encode_int(2**160 - 1)
        acct = block.state.get(i) or ''

        # Generate another random position
        pos2 = encode_int(sha3(header+encode_int(nonce+1)))

        # Get a trie object for the account we already discovered
        t = block.get_storage(acct.encode('hex'))

        # Next key and value in state after the second position
        j = t.next(pos2) or encode_int(2**256 - 1)
        val = block.state.get(j) or ''

        return utils.decode_int(sha3(i + acct + j + val))

Now, let us put it all together into the mining algo:

    def mine(daggerset, params, block):
        nonce = random.random(2**50)
        while 1:
            h1 = hashimoto(daggerset, params, 
                           block.serialize_header_without_nonce(), nonce)
            h2 = get_state(block, block.serialize_header_without_nonce(), nonce)
            if sha3(h1 + h2) * params["diff"] < 2**256:
                return nonce
            nonce += 1

And the verification algo:

    def verify(daggerset, params, block, nonce):
        h1 = hashimoto(daggerset, params, block, nonce)
        h2 = get_state(block, block.serialize_header_without_nonce(), nonce)
        return sha3(h1 + h2) * params["diff"] < 2**256

Light-client friendly verification:

    def light_verify(seedset, params, header, nonce):
        h1 = light_hashimoto(seedset, params, header, nonce)
        h2 = get_state(block, block.serialize_header_without_nonce(), nonce)
        return sha3(h1 + h2) * params["diff"] < 2**256

Note that the light client verification algorithm requires "pre-seeding" the light-client's database with nodes from a Merkle tree proof of the block's validity. Also note that the SHA3 wrapper creates a "two-layered PoW" where the outer layer is very fast and "info-free" to verify (ie. you can verify the outer PoW by just computing SHA3; no need to know the daggerset state).

Also, note that Dagger Hashimoto imposes additional requirements on the block header:

* For two-layer verification to work, a block header must have both the nonce and the middle value pre-sha3
* Somewhere, a block header must store the sha3 of the current seedset

Special thanks to feedback from:

* Tim Hughes
* Matthew Wampler-Doty
* Thaddeus Dryja