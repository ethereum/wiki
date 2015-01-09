Dagger Hashimoto is a proposed spec for the mining algorithm for Ethereum 1.0. Dagger Hashimoto aims to simultaneously satisfy three goals:

1. **ASIC-resistance**: the benefit from creating specialized hardware for the algorithm should be as small as possible, ideally to the point that even in an economy where ASICs have been developed the speedup is sufficiently small that it is still marginally profitable for users on ordinary computers to mine with spare CPU power.
2. **Light client verifiability**: a block should be relatively efficiently verifiable by a light client.
3. **Full chain storage**: mining should require storage of the complete blockchain state (due to the irregular structure of the Ethereum state trie, we anticipate that some pruning will be possible, particularly of some often-used contracts, but we want to minimize this).

Dagger Hashimoto builds on two key pieces of previous work:

* [Hashimoto](http://vaurum.com/hashimoto.pdf), an algorithm by Thaddeus Dryja which intends to achieve ASIC resistance by being IO-bound, ie. making memory reads the limiting factor in the mining process. The theory is that RAM is in principle inherently a much more generic ingredient than computation, and billions of dollars of research already go into optimizing it for different use cases which often involve near-random access patterns (hence "random access memory"); hence, existing RAM is likely to be moderately close to optimal for evaluating the algorithm. Hashimoto uses the blockchain as a source of data, simultaneously satisfying (1) and (3) above.
* [Dagger](http://vitalik.ca/ethereum/dagger.html), an algorithm by Vitalik Buterin which uses directed acyclic graphs to simultaneously achieve memory-hard computation but memory-easy validation. The core principle is that each individual nonce only requires a small portion of a large total data tree, and recomputing the subtree for each nonce is prohibitive for mining - hence the need to store the tree - but okay for a single nonce's worth of verification. Dagger was meant to be an alternative to existing memory-hard algorithms like [Scrypt](http://en.wikipedia.org/wiki/Scrypt), which are memory-hard but are also very hard to verify when their memory-hardness is increased to genuinely secure levels. However, Dagger was proven to be vulnerable to shared memory hardware acceleration [by Sergio Lerner](https://bitslog.wordpress.com/2014/01/17/ethereum-dagger-pow-is-flawed/) and was then dropped in favor of other avenues of research.

Approaches that were tried between Dagger and Dagger Hashimoto but are currently not our primary focus include:

* "Blockchain-based proof of work" - a proof of work function that involves running contracts taken from the blockchain. The approach was abandoned because it was long-range attack vulnerabilities, since attackers can create forks and populate them with contracts that they have a secret fast "trapdoor" execution mechanism for.
* "Random circuit" - a proof of work function developed largely by Vlad Zamfir that involves generating a new program every 1000 nonces - essentially, choosing a new hash function each time, faster than even FPGAs can reconfigure. The approach was temporarily put aside because it was difficult to see what mechanism one can use to generate random programs that would be general enough so that specialization gains would be low; however, we see no fundamental reasons why the concept cannot be made to work.

The difference between Dagger Hashimoto and Hashimoto is that, instead of using the blockchain as a data source, Dagger Hashimoto uses a custom-generated 1 GB data set, which updates based on block data every N blocks. The data set is generated using the Dagger algorithm, allowing for the efficient calculation of a subset specific to every nonce for the light client verification algorithm. The difference between Dagger Hashimoto and Dagger is that, unlike in the original Dagger, the dataset used to query the block is semi-permanent, only being updated at occasional intervals (eg. once per week). This means that the portion of the effort that goes toward generating the dataset is close to zero, so Sergio Lerner's arguments regarding shared memory speedups become negligible.

## DAG Generation

The code for the algorithm will be defined in Python below. First, we give `encode_int` for marshaling unsigned ints of specified precision to strings. Its inverse is also given:

```python
NUM_BITS = 512

def encode_int(x):
    "Encode an integer x as a string of 64 characters using a big-endian scheme"
    o = ''
    for _ in range(NUM_BITS / 8):
        o = chr(x % 256) + o
        x //= 256
    return o

def decode_int(s):
    "Unencode an integer x from a string using a big-endian scheme"
    x = 0
    for c in s:
        x *= 256
        x += ord(c)
    return x
```

We next assume that `sha3` is a function that takes an integer and outputs an integer; if converting this reference code into an implementation use:

```python
from pyethereum import utils
def sha3(x):
    return decode_int(utils.sha3(encode_int(x)))
```

### Parameters

The parameters used for the algorithm are:

```python
SAFE_PRIME_512 = 2**512 - 38117     # Largest Safe Prime less than 2**512

params = {
      "n": 1000000000 * 8 / NUM_BITS,  # Size of the dataset 1 Gigabyte
      "cache_size": 2500,              # Size of the light client's cache
      "diff": 2**14,                   # Difficulty (adjusted during block evaluation)
      "epochtime": 1000,               # Length of an epoch in blocks (how often the dataset is updated)
      "k": 2,                          # Number of parents of a node
      "w": 3,                          # Used for modular exponentiation hashing
      "accesses": 100,                 # Number of dataset accesses during hashimoto
      "P": SAFE_PRIME_512              # Safe Prime for hashing and random number generation
}
```

`P` in this case is a prime chosen such that `log₂(P)` is just slightly less than 512, which corresponds to the 512 bits we have been using to represent our numbers.

### Dagger graph building

The dagger graph building primitive is defined as follows:

```python
def produce_dag(params, seed):
    P = params["P"]
    a = sha3(seed) + 2
    picker = init = pow(sha3(seed) + 2, params["w"], P)
    o = [init]
    for i in range(1, params["n"]):
        x = picker = (picker * init) % P
        for _ in range(params["k"]):
            x ^= o[x % i]
        o.append(pow(x, params["w"], P))
    return o
```

Essentially, it starts off a graph as a single node, `sha3(seed)`, and from there starts sequentially adding on other nodes based on random previous nodes. When a new node is created, a modular power of the seed is computed to randomly select some indices less than `i` (using `x % i` above), and the values of the nodes at those indices are used in a calculation to generate a new a value for `x`, which is then fed into a small proof of work function (based on XOR) to ultimately generate the value of the graph at index `i`.  The rationale behind this particular design is to force sequential access of the DAG; the next value of the DAG that will be accessed cannot be determined until the current value is known.  Finally, modular exponentiation is used to further hash the result.

Since it is inconvenient to mine using arbitrary precision arithmetic, the results are aliased to 64 bit numbers.

This algorithm relies on several results from number theory.  See the appendix below for a discussion.

----------------------------
The intent of the above graph construction is to allow each individual node in the graph can be reconstructed by computing a subtree of only a small number of nodes, and requiring only a small amount of auxiliary computation.  In particular, our choice of LCG allows us to compute the *i*th picker without computing all preceding pickers, in a fraction of the time. Combined with memoization, this allows for reasonable performance with little memory overhead.

The light client computing function for the DAG works as follows:

```python
def quick_calc(params, seed, pos):
    P = params["P"]
    init = sha3(seed)+1
    known = {0: init}
    def calc(p):
        if p not in known:
            picker = quick_pick(init, p, params)
            x = picker
            for _ in range(params["k"]):
                x ^= calc(picker % p)
                picker >>= 10
                picker = x % picker if picker else 0
            known[p] = pow(x, params["w"], P)
        return known[p]
    x = calc(pos)
    return x
```

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

Note that we also add an `is_serial` parameter to offer the option of making the hashimoto computation non-parallelizable; if activated, this works by adding the current "mix" from earlier indices into the calculation of which later indices to use.

Here is a light-client friendly version, using functions defined above:

    def light_hashimoto(seedset, params, header, nonce):
        rand = sha3(header+encode_int(nonce))
        mix = 0
        for i in range(40):
            shifted_A = (rand ^ mix * params["is_serial"]) >> i
            seed = seedset[shifted_A % params["numdags"]]
            # can further optimize with cross-round memoization
            mix ^= quick_calc(params, seed, (shifted_A // params["numdags"]) % params["n"])
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

# Appendix

As noted above, the RNG used for DAG generation relies on some results from number theory. First, we provide assurance that the Lehmer RNG that is the basis for the `picker` variable has a wide period.  Second, we show that `pow(x,3,P)` will not map `x` to `1` or `P-1` provided `x ∈ [2,P-2]` to start.  Finally, we show that `pow(x,3,P)` has a low collision rate when treated as a hashing function.

## Lehmer Random Number Generator

While the `produce_dag` function does not need to produce unbiased random numbers, a potential threat is that `seed**i % P` only takes on a handful of values. This could provide an advantage to miners recognizing the pattern over those that do not.

To avoid this, a result from number theory is appealed to. A [*Safe Prime*](https://en.wikipedia.org/wiki/Safe_prime) is defined to be a prime `P` such that `(P-1)/2` is also prime.  The *order* of a member `x` of the [multiplicative group](https://en.wikipedia.org/wiki/Multiplicative_group_of_integers_modulo_n) `ℤ/nℤ` is defined to be the minimal `m` such that <center><pre>xᵐ mod P ≡ 1</pre></center>
Given these definitions, we have:

> Observation 1. Let `x` be a member of the multiplicative group `ℤ/Pℤ` for a safe prime `P`.  If `x mod P ≠ 1 mod P` and `x mod P ≠ P-1 mod P`, then the order of `x` is either `P-1` or `(P-1)/2`.

*Proof*.  Since `P` is a safe prime, then by [Lagrange's Theorem][Lagrange] we have that the order of `x` is either `1`, `2`, `(P-1)/2`, or `P-1`.

The order of `x` cannot be `1`, since by Fermat's Little Theorem we have:
<center><pre>x<sup>P-1</sup> mod P ≡ 1</pre></center>
Hence `x` must be a multiplicative identity of `ℤ/nℤ`, which is unique.  Since we assumed that `x ≠ 1` by assumption, this is not possible.

The order of `x` cannot be `2` unless `x = P-1`, since this would violate that `P` is prime.
<div align="right">◻</div>

From the above proposition, we can recognize that iterating `(picker * init) % P` will have a cycle length of at least `(P-1)/2`.  This is because we selected `P` to be a safe prime approximately equal to be a higher power of two, and `init` is in the interval `[2,2**256+1]`.  Given the magnitude fo `P`, we should never expect a cycle from modular exponentiation.

When we are assigning the first cell in the DAG (the variable labeled `init`), we compute `pow(sha3(seed) + 2, 3, P)`.  At first glance, this does not guarantee that the result is neither `1` nor `P-1`.  However, since `P-1` is a safe prime, we have the following additional assuance, which is a corollary of Observation 1:

> Observation 2. Let `x` be a member of the multiplicative group `ℤ/Pℤ` for a safe prime `P`, and let `w` be a natural number. If `x mod P ≠ 1 mod P` and `x mod P ≠ P-1 mod P`, as well as `w mod P ≠ P-1 mod P` and `w mod P ≠ 0 mod P`, then `xʷ mod P ≠ 1 mod P` and `xʷ mod P ≠ P-1 mod P`

[Lagrange]: https://en.wikipedia.org/wiki/Lagrange%27s_theorem_(group_theory)

## Modular Exponentiation as a Hash Function

For certain values of `P` and `w`, the function `pow(x, w, P)` may have many collisions.  For instance, `pow(x,9,19)` only takes on values `{1,18}`. 

Given that `P` is prime, then an appropriate `w` for a modular exponentation hashing function can be chosen using the following result:

> Observation 3. Let `P` be a prime; `w` and `P-1` are relatively prime if and only if for all `a` and `b` in `ℤ/Pℤ`:<center>`aʷ mod P ≡ bʷ mod P` if and only if `a mod P ≡ b mod P`</center>

Thus, given that `P` is prime and `w` is relatively prime to `P-1`, we have that `|{pow(x, w, P) : x ∈ ℤ}| = P`, implying that the hashing function has the minimal collision rate possible.

In the special case that `P` is a safe prime as we have selected, then `P-1` only has factors 1, 2, `(P-1)/2` and `P-1`.  Since `P` > 7, we know that 3 is relatively prime to `P-1`, hence `w=3` satisfies the above proposition.

Special thanks to feedback from:

* Tim Hughes
* Matthew Wampler-Doty
* Thaddeus Dryja