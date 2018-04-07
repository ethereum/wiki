---
name: Ethash Revision 11
category: 
---

**This spec is REVISION 11. Whenever you substantively (ie. not clarifications) update the algorithm, please update the revision number in this sentence. Also, in all implementations please include a spec revision number**

Ethash is the planned PoW algorithm for Ethereum 1.0. It is the latest version of Dagger-Hashimoto, although it can no longer appropriately be called that since many of the original features of both algorithms have been drastically changed in the last month of research and development. See [https://github.com/ethereum/wiki/wiki/Dagger-Hashimoto](https://github.com/ethereum/wiki/wiki/Dagger-Hashimoto) for the original version.

The specification for the algorithm is written in python to give a balance between clarity and exactness. If you are interested in actually running the spec as code, then you can; you simply need to install the `python_sha3` and `pyethereum` libraries, and add the following lines to your top of the file:

```python
import sha3

# Assumes little endian bit ordering (same as Intel architectures)
def decode_int(s):
   return int(s[::-1].encode('hex'), 16) if s else 0

def encode_int(s):
   a = "%x" % s
   return '' if s == 0 else ('0' * (len(a) % 2) + a).decode('hex')[::-1]

def zpad(s, length):
   return s + '\x00' * max(0, length - len(s))

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
3. From the seed and cache, there exists a function `calc_dag_item(seed, cache, i)` which calculates the value of any element in a larger 1 GB dataset. The function for computing each element involves many elements in the cache. Miners are meant to store the 1 GB dataset in memory.
4. The actual algorithm is a loop that involves combining together many items from the DAG and taking a hash of the output. Each individual read from the DAG is 4KB wide in order to fetch a full page from memory.

The large dataset is updated once every 1000 blocks, so the vast majority of a miner's effort will be reading the dataset, not making changes to it. The dataset also grows over time; it starts off at 1 GB and grows by about 345 MB per year.

First, we define the parameters:

```python
params = {
    "cache_bytes": 33554432,      # bytes in cache
    "dag_bytes_init": 1073741824, # bytes in dag
    "dag_bytes_growth": 131072,   # growth per epoch (~345 MB per year)
    "epoch_length": 1000,         # blocks per epoch
    "dag_parents": 64,            # number of parents of each dag element
    "cache_rounds": 2,            # number of processing rounds in cache production
    "mix_bytes": 4096,            # width of mix
    "accesses": 32,               # number of accesses in hashimoto loop
    "hash_bytes": 64              # hash length in bytes
}
```

### Cache Generation

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

    return o
```

[Sergio2014]: http://www.hashcash.org/papers/memohash.pdf

The cache production process involves first sequentially filling up 32 MB of memory, then performing two passes of Sergio Demian Lerner's *RandMemoHash* algorithm from [*Strict Memory Hard Hashing Functions* (2014)](http://www.hashcash.org/papers/memohash.pdf). The output is a set of 524288 64-byte values.

### Pseudo Random Number Generation

Now, we specify some auxiliary methods for running a variant of the [Blum Blum Shub](https://en.wikipedia.org/wiki/Blum_Blum_Shub) pseudorandom number generator on a 32-bit prime modulus; this is a very quick way of generating pseudorandom data.

```python
# two safe primes approximately equal to 2**32
P1 = 4294967087
P2 = 4294963787

# Clamp a value to between the specified minimum and maximum
def clamp(minimum, x, maximum):
    return max(minimum, min(maximum, x))

# Many steps of the BBS RNG in logtime
def quick_bbs(seed, i, P):
    return pow(seed, pow(3, i, P-1), P)
    
# One step of the BBS RNG
def step_bbs(n, P):
    return (n * n * n) % P
```

When choosing a safe prime *p* for our random number generator, we wish to find ones where the [multiplicative order](http://en.wikipedia.org/wiki/Multiplicative_order) of 3 in ℤ/(*p* - 1) is *high*, since this determines the cycle length of the corresponding random number generator.  The multiplicative order of 3 in ℤ/(4294967086) is 1073741771. Note that cryptographic security is NOT required of the RNG here; we only need it to provide values which are roughly even across the entire output space `[0 ... 2**32 - 1]` and can be relied on to pass the [Diehard Tests](http://en.wikipedia.org/wiki/Diehard_tests).

This particular pseudo random number generator may exhibit bias when taking its output modulo a value which is not a prime, so we will choose our memory sizes in terms of primes in order to remove any bias.

### Data aggregation function

We use an algorithm inspired by the [FNV hash](https://en.wikipedia.org/wiki/Fowler%E2%80%93Noll%E2%80%93Vo_hash_function) in order to combine large blocks of data together. This is essentially meant to be a non-associative substitute for XOR.

```python
FNV_PRIME = 0x01000193

def fnv(a, b):
    o = ''
    for pos in range(0, len(a), 4):
        v1 = decode_int(a[pos:pos+4])
        v2 = decode_int(b[pos:pos+4])
        o += zpad(encode_int((v1 * FNV_PRIME ^ v2) % 2**32), 4)
    return o
```

### Full dataset calculation

Each 64-byte item in the full 1 GB dataset is computed as follows:

```python
def calc_dag_item(params, cache, i):
    n = params["cache_bytes"] / params["hash_bytes"]
    L = params["hash_bytes"]
    rand_seed = clamp(2, decode_int(cache[0][:4]), P1 - 2)
    rand = clamp(2, quick_bbs(rand_seed, i, P1), P2 - 2)
    mix = cache[i % n]
    for j in range(params["dag_parents"]):
        mix_value = decode_int(mix[(j*4)%L: (j*4+3)%L])
        mix = fnv(mix, cache[(rand ^ mix_value) % n])
        rand = step_bbs(rand, P2)
    return mix
```

Essentially, we use our RNG to generate a seed for the specific item, and use that as the seed for another RNG which picks 64 indices from the cache. We initialize a mix to equal the 512-bit big-endian representation of the index (the purpose of this is to make sure all of the entropy from the index, and not just the first 32 bytes as does the RNG, in computing the final DAG value), and then repeatedly run through random parts of the cache and use our aggregation function to combine the data together.

### Main Loop

Now, we specify the main "hashimoto"-like loop, where we aggregate data from the full dataset in order to produce our final value for a particular header and nonce:

```python
def hashimoto(params, header, nonce, dagsize, dag_lookup):
    L = params["mix_bytes"]
    w = params["mix_bytes"] / params["hash_bytes"]
    n = dagsize / params["hash_bytes"]
    s = sha3_512(header + nonce)
    mix = [s for _ in range(w)]
    rand = clamp(2, decode_int(s[-4:]), P2 - 2)
    for i in range(params["accesses"]):
        mix_value = decode_int(mix[0][(i*4) % L: (i*4+3) % L])
        p = (rand ^ mix_value) % (n // w) * w
        for j in range(w):
            mix[j] = fnv(mix[j], dag_lookup(p + j))
        rand = step_bbs(rand, P2)
    return sha3_256(s+sha3_256(s + ''.join(mix)))

def hashimoto_light(params, cache, header, nonce, dagsize):
    return hashimoto(params, header, nonce, dagsize, lambda x: calc_dag_item(params, cache, x))

def hashimoto_full(params, dag, header, nonce):
    return hashimoto(params, cache, header, nonce, len(dag), lambda x: dag[x])
```

Essentially, we maintain a "mix" 4096 bytes wide, and repeatedly sequentially fetch 4096 bytes from the full dataset and use the `fnv` function to combine it with the mix. 4096 bytes of sequential access are used so that each round of the algorithm always fetches a full page from RAM, minimizing translation lookaside buffer misses which ASICs would theoretically be able to avoid.

If the output of this algorithm is below the desired target, then the nonce is valid. Note that the double application of `sha3_256` ensures that there exists an intermediate nonce which can be provided to prove that at least a small amount of work was done; this quick outer PoW verification can be used for anti-DDoS purposes.  It also serves to provide statistical assurance that the result is an unbiased, 256 bit number.

### Mining

The mining algorithm is defined as follows:

```python
def mine(params, dag, header, difficulty):
    from random import randint
    nonce = randint(0,2**64)
    while decode_int(hashimoto_full(params, dag, header, nonce)) < difficulty:
        nonce += 1
        nonce %= 2**64
    return nonce
```

### Defining the seed

In order to compute the seed for a given block, we use the following algorithm:

```python
 def get_seedset(params, block):
     if block.number == 0:
         return ('\x42' * 32, '\x43' * 32)
     elif (block.number % params["epoch_length"] == 0):
         x, y = get_seedset(params, block.parent)
         if (block.number // params["epoch_length"]) % 2:
             y = sha3_256(y + block.prevhash)
         else:
             x = sha3_256(x + block.prevhash)
         return (x, y)
     else:
         return get_seedset(params, block.parent)
```

Note the this function outputs two seeds. During even-numbered epochs, the PoW uses the first seed, and during odd-numbered epochs the PoW uses the second seed; this allows a miner to rebuild one DAG while simultaneously still using the other DAG, ensuring a smooth transition as the dataset gets updated.

In order to compute the size of the dataset and the cache at a given block number, we use the following function:

```python
def get_datasize(params, block):
    datasize_in_bytes = params["dag_bytes_init"] + \
                        params["dag_bytes_growth"] * (block.number // params["epoch_length"])
    while not isprime(datasize_in_bytes // params["mix_bytes"]):
        datasize_in_bytes -= params["mix_bytes"]
    return datasize_in_bytes

def get_cachesize(params, block):
    datasize_in_bytes = get_datasize(params, block)
    cachesize_in_bytes = (datasize_in_bytes // 32)
    while not isprime(cachesize_in_bytes // params["hash_bytes"]):
        cachesize_in_bytes -= params["hash_bytes"]
    return cachesize_in_bytes
```

Where `isprime` is of course:

```python
 def isprime(n):
      for i in range(2, int(n ** 0.5 + 1)):
          if n % i == 0:
              return False
      return True
```

For an optimization, one can add the line `if pow(2, n, n) != 2: return False` as an initial check, or use the Sieve of Erasthothenes to precompute the list of primes that the dataset will increase to all at once. Essentially, we are keeping the size of the dataset to always be equal to the highest prime below a linearly growing function, so on average in the long term the dataset will grow roughly linearly.