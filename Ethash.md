Ethash is the planned PoW algorithm for Ethereum 1.0. It is the latest version of Dagger-Hashimoto, although it can no longer appropriately be called that since many of the original features of both algorithms have been drastically changed in the last month of research and development. See [https://github.com/ethereum/wiki/wiki/Dagger-Hashimoto](https://github.com/ethereum/wiki/wiki/Dagger-Hashimoto) for the original version.

The specification for the algorithm is written in python to give a balance between clarity and exactness. If you are interested in actually running the spec as code, then you can; you simply need to install the `python_sha3` and `pyethereum` libraries, and add the following lines to your top of the file:

```python
import sha3
from pyethereum.utils import encode_int, zpad

# Decode a string of (unsigned) chars as an int
# Assumes little endian bit ordering (same as Intel architectures)
def decode_int(s):
   return int(s[::-1].encode('hex'), 16)

# sha3 hash function, outputs 64 bytes
def sha3_512(x):
    return sha3.sha3_512(x).digest()

def sha3_256(x):
    return sha3.sha3_256(x).digest()
```

### Goals

Ethash is intended to satisfy the following goals:

1. **IO saturation**: the algorithm should consume nearly the entire available memory access bandwidth (this is a strategy toward achieving ASIC resistance)
2. **Light client verifiability**: a light client should be able to verify a round of mining in under 0.01 seconds on a desktop in C, and under 1 second in Python or Javascript, with at most 32 MB of memory
3. **Light client slowdown**: the process of running the algorithm with a light client should be much slower than the process with a full client, to the point that the light client algorithm is not an economically viable route toward making an ASIC implementation.
4. **Light client fast startup**: a light client should be able to become fully operational and able to verify blocks within 40 seconds in Python or Javascript.

The general route that the algorithm takes is as follows:

1. There exists a `seed` which can be computed for each block by scanning through the block headers up until that point.
2. From the seed, one can compute a 32 MB pseudorandom `cache`
3. From the seed and cache, there exists a function `calc_dag_item(seed, cache, i)` which calculates the value of any element in a larger 1 GB dataset. The function for computing each element involves many elements in the cache.
4. The actual algorithm is a loop that involves combining together many items from the DAG and taking a hash of the output. Each individual read from the DAG is 4KB wide in order to fetch a full page from memory.

First, we define the parameters:

    params = {
        "cache_bytes": 33554432,      # bytes in cache
        "dag_bytes_init": 1073741824, # bytes in dag
        "dag_bytes_growth": 131072,   # growth per epoch (~345 MB per year)
        "epoch_length": 1000,         # blocks per epoch
        "k": 64,                      # number of parents of each dag element
        "cache_rounds": 2,            # number of processing rounds in cache production
        "mix_bytes": 4096,            # width of mix
        "accesses": 64,               # number of accesses in hashimoto loop
        "hash_bytes": 64              # hash length in bytes
    }

### Cache generation

Now, we specify the function for producing a cache:

```python
def mkcache(params, seed):
    n = params["cache_bytes"] / params["hash_bytes"]

    # Sequentially produce the initial dataset
    o = [sha3_512(seed)]
    for i in range(1, n):
        o.append(sha3_512(o[-1]))

    for _ in range(params["cache_rounds"]):
        for i in range(n):
            v = (decode_int(o[i]) % 2**64) % n
            o[i] = sha3_512(o[(i-1+n)%n] + o[v])

    # Output integers so we can more easily xor
    return [decode_int(v) for v in o]
```

[Sergio2014]: http://www.hashcash.org/papers/memohash.pdf

The cache production process involves first sequentially filling up 32 MB of memory, then performing two passes of Sergio Demian Lerner's *RandMemoHash* algorithm from [*Strict Memory Hard Hashing Functions* (2014)](http://www.hashcash.org/papers/memohash.pdf).

### Pseudo Random Number Generation

Now, we specify some auxiliary methods for running a variant of the [Blum Blum Shub](https://en.wikipedia.org/wiki/Blum_Blum_Shub) pseudorandom number generator on a 32-bit prime modulus; this is a very quick way of generating pseudorandom data. We use two BBS generators with different periods in parallel, providing 64 bits of entropy:

```python
# two safe primes approximately equal to 2**32
P1 = 4294963787
P2 = 4294967087

# Clamp a value to between the specified minimum and maximum
def clamp(minimum, x, maximum):
    return max(minimum, min(maximum, x))
    
# Initializes the RNG state from a seed
def rng_init(seed):
    n1 = (seed % 2**64) // 2**32
    n2 = seed % 2**32
    return (clamp(2, n1, P1 - 2), clamp(2, n2, P1 - 2))
    
# Computes one step
def rng_step(r):
    o1 = (r[0] * r[0] * r[0]) % P1
    o2 = (r[1] * r[1] * r[1]) % P2
    return (o1, o2)
    
# Provides an output value from an RNG state
def rng_output(r):
    a,b = r
    return a << 32 | a ^ b 
    
# Quickly computes i steps of the BBS generator
def rng_quick(seed, i):
    n1, n2 = rng_init(n)
    o1 = pow(n1, pow(3, i, P1-1), P1)
    o2 = pow(n2, pow(3, i, P2-1), P2)
    return (o1,o2)
```

When choosing a safe prime *p* for our random number generator, we wish to find ones where the [multiplicative order](http://en.wikipedia.org/wiki/Multiplicative_order) of 3 in ℤ/(*p* - 1) is *high*, since this determines the cycle length of the corresponding random number generator.  The multiplicative order of 3 in ℤ/(4294963786) is 2147481892, and the multiplicative order of 3 in ℤ/(4294967086) is 1073741771; hence their combined period (computed using their *least common multiple*) is given by 2305841009906510732 or roughly 2<sup>61</sup>. Note that cryptographic security is NOT required of the RNG here; we only need it to provide values which are roughly even across the entire output space `[0 ... 2**32 - 1]` and can be relied on to pass the [Diehard Tests](http://en.wikipedia.org/wiki/Diehard_tests).

This particular pseudo random number generator may exhibit bias when taking its output modulo a value which is not a prime, so we will choose our memory sizes in terms of primes in order to remove any bias.

### Full dataset calculation

Each item in the full dataset is computed as follows:

    def calc_dag_item(params, seed, cache, i):
        n = params["cache_bytes"] / params["hash_bytes"]
        rand = rng_init(rng_quick(decode_int(seed) % 2**32), i) + (2**32 + 1))
        o = 0
        for i in range(params["k"]):
            o ^= cache[rng_output(rand) % n]
            rand = rng_step(rand)
        return o

Essentially, we use our RNG to generate a seed for the specific item, and use that (plus 2**32 + 1 in order to throw off commutativity) as the seed for another RNG which picks 64 indices from the cache. Note that if we want to compute the dataset in series, as a miner would do, we can replace the `rng_quick` with a round of `rng_step` per nonce.

### Main loop

Now, we specify the main "hashimoto"-like loop, where we aggregate data from the full dataset in order to produce our final value for a particular header and nonce:

    def hashimoto(params, seed, cache, header, nonce, sz):
        w = params["mix_bytes"] / params["hash_bytes"]
        n = sz / params["hash_bytes"]
        o = [0 for _ in range(w)]
        s = sha3_512(header + nonce)
        rand = rng_init(decode_int(s))
        for i in range(params["accesses"]):
            p = rng_out(rand) % (n // w) * w
            for j in range(w):
                o[j] ^= calc_dag_item(params, seed, cache, p + j)
            rand = rng_step(rand)
        acc = s + ''.join([zpad(encode_int(x), 64) for x in o])
        return sha3_256(s+sha3_256(acc))

If the output of this is below the desired target, then the nonce is valid. Note that the double application of sha3_256 ensures that there exists an intermediate nonce which can be provided to prove that at least a small amount of work was done; this quick outer PoW verification can be used for anti-DDoS purposes.

### Defining the seed

In order to get the 

    def get_prevhash(n):
        from pyethereum.blocks import GENESIS_PREVHASH 
        from pyethreum import chain_manager
        if num <= 0:
            return hash_to_int(GENESIS_PREVHASH)
        else:
            prevhash = chain_manager.index.get_block_by_number(n - 1)
            return decode_int(prevhash)

    def get_seedset(params, block):
        seedset = {}
        seedset["back_number"] = block.number - (block.number % params["epochtime"])
        seedset["back_hash"] = get_prevhash(seedset["back_number"])
        seedset["front_number"] = max(seedset["back_number"] - params["epochtime"], 0)
        seedset["front_hash"] = get_prevhash(seedset["front_number"])
        return seedset

    def get_dagsize(params, block):
        return params["n"] + (block.number // params["epochtime"]) * params["n_inc"]

    def get_daggerset(params, block):
        dagsz = get_dagsize(params, block)
    seedset = get_seedset(params, block)
    if seedset["front_hash"] <= 0:
        # No back buffer is possible, just make front buffer
        return {"front": {"dag": produce_dag(params, seedset["front_hash"], dagsz), 
                          "block_number": 0}}
    else:
        return {"front": {"dag": produce_dag(params, seedset["front_hash"], dagsz),
                          "block_number": seedset["front_number"]},
                "back": {"dag": produce_dag(params, seedset["back_hash"], dagsz),
                         "block_number": seedset["back_number"]}}