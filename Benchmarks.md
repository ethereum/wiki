# Trie

## Block Processing

The point of this benchmark is to give a controlled time trial for processing blocks including all of the guff that goes with block processing including PoW verification, transaction signature checking, EVM code execution, receipt verification, uncle validation and database population. In order to mitigate biases one way or another for each of those aspects in the benchmark, we use the first 1,000,000 blocks of the Frontier mainnet chain.

### Method

There are probably several ways of effecting this benchmark for each client (e.g. cpp-ethereum has the `--import` feature), but for now we're settling on a simple "sync from localhost" method.

This involves setting up a client synced up to block 1,000,000 of the Frontier network (we used `eth`, specifically, but in principle it shouldn't matter which client is used). Following this, the client to be benchmarked is executed on the same machine with a clean database in the full block processing mode. It is initialised with the former client's address and allowed to synchronise. The process is timed until it eventually imports block 1,000,000.

We did all tests on a standard Digital Ocean 4GB droplet running Ubuntu 14.04.3 x64.

### Code

To automate the method described above, the following library has been created:

https://github.com/ethcore/blockchain-sync

To benchmark a client, first, a "master" node (e.g. geth) has to be installed and synced up from the network up to a certain point (e.g. 1m blocks). Then, the master client should be restarted in "no-discovery" mode, e.g.:

```
geth --datadir /tmp/geth-master --nodiscover --port 30305 console
```

Next, the client to be benchmarked has to be installed and configured, if necessary. 

The benchmarking script has to be passed the "master" node's enode address along with other optional arguments (run `bin/run-bench -h` for more info). 

Command for benchmarking Parity:
```
bin/run-bench --rm -e <master enode> parity
```

*Note:* since the main purpose of this utility is to benchmark full blockchain processing times, certain optimisation options like `geth --fast` have been intentionally turned off.

### Results

- | Eth | EthereumJ | Geth | Parity
--- |--- | --- | --- | ---
Time | 4h 33m | 7h 7m | 8h 43m | 2h 31m
CPU (avg) | 123% | 90% | 70% | 107%
Memory (avg) | 921MB | 3.168GB | 1.5GB | 365MB

## The Trie

The point of this is to give a controlled test between clients that reflects typical on-chain situations. We do this by defining a common dataset of key/value pairs for insertion into the trie and root-calculation. There are two modes to reflect the two use cases of the trie in Ethereum:

- Persistent Tries: The secure tries for storage and state. These have their nodes stored permanently in a backing store and, periodically, are updated a number of times before a new root is taken and used in either the receipt or the account's storage_root field.
- Ephemeral Tries: The receipt and transaction tries. These tries do not require that their nodes be stored permanently, but rather are used primarily to determine their root, which is placed in the header under receipts_root and transactions_root fields.

### Persistent Tries

For this benchmark, the dataset is inserted into the trie with root hashes being computed at specific intervals ("era_size"). This number represents the number of update operations per root calculation.

### Ephemeral Tries

For this benchmark, the important thing is to take all data and determine the root; no roots need be computed along the way and there is no assertion that any state used when determining the root be required. In this sense era_size is equivalent to the dataset size.

## Standard Dataset

Sensible implementations may precompute the dataset to avoid the additional burden of SHA3 computation at benchmark time.

```python
from ethereum import trie, db, utils
import time

MIN_COUNT = 32
ROUNDS = 1000
ERA_SIZE = 4
MAX_COUNT = 32
SYMMETRIC = True
a = time.time()
t = trie.Trie(db.EphemDB())
seed = '\x00' * 32

for i in range(ROUNDS):
    seed = utils.sha3(seed)
    mykey = seed[:MIN_COUNT + ord(seed[-1]) % (MAX_COUNT + 1 - MIN_COUNT)]
    if SYMMETRIC:
        t.update(mykey, mykey)
    else:
        seed = utils.sha3(seed)
        myval = seed[-1] if ord(seed[0]) % 2 else seed
        t.update(mykey, myval)
    if i % ERA_SIZE == 0
        seed = t.root()
print time.time() - a 
print t.root_hash.encode('hex')
```

The final trie root after 1000 rounds should be `36f6...93a3` for `SYMMETRIC = True` and `da8a...0ca4` for `False`.


## Method

Tests run on:
```
Linux gav-MacBookPro 4.4.0-040400rc7-lowlatency #201512272230 SMP PREEMPT Mon Dec 28 03:36:57 UTC 2015 x86_64 x86_64 x86_64 GNU/Linux
```

CPU:
```
model name	: Intel(R) Core(TM) i7-4980HQ CPU @ 2.80GHz
cpu MHz		: 3195.609
cache size	: 6144 KB
bogomips	: 5587.06
```

### C++

```
cd libweb3core
cmake -DCMAKE_BUILD_TYPE=Release
make -j8
sudo nice -n -19 ./libweb3core/bench/bench trie
```

### Parity

```
cd util && cargo bench
```

### CPython / PyPy
```
pip install ethereum
wget https://gist.github.com/heikoheiko/0fa2b322560ba7794f22
python trie_benchmark.py
pypy trie_benchmark.py
```

### Go

```
sudo nice -n -19 godep go test  -run=- -bench=Std ./trie
```

### JS
```
git clone https://github.com/ethereumjs/merkle-patricia-tree.git`
cd merkle-patricia-tree
npm install
node ./benchmarks/random.js
```

## Results

All times in ms.

Test ID is given as `pair_count`-`era_size`-`key_size`-`value_type`, where valid `value_type`s are `ran` (`SYMMETRIC = False`) and `mir` (`SYMMETRIC = True`).

### Persistent Trie Benchmark

The standard secure-trie test is `1k-9-32-ran`. `ran` is used as `value_type` as it better reflects the kinds of values that are typically written in the blockchain. The `9` that is used as `era_size` is determined empirically from the Frontier mainnet: the mean number of insertions per commit on all secure tries from block #0 to #900,000 is 9.35 (TODO: determine median & quartiles - they're likely to be better indicators on real world performance).

| Client      | Time | SHA3s |
| ----------- | --------- | ----- |
| C++ | 23.8 | 8469 |
| CPython | 369 | 7079 |
| PyPy | 45 | |
| Go | 23.6 | |
| Pure JS | 374 | |
| Parity | 11.6 | 4221 |

#### Other results (values other than 9 inserts/commit)

| Test ID      | C++  | CPython | PyPy | Go  | JS  |
| ------------ | ------ | ----- | --- | ---- | --- |
| 1k-3-32-ran  | 23.8   | 369   | 45 | 45.7  | 388 |
| 1k-5-32-ran  | 23.8   | 369   | 41 | 28.5  | 374 |

## Ephemeral Trie Benchmark

For this benchmark, 1k inserts is quite reasonable; we naturally avoid doing any "partially-complete" root determination. TODO: redo benchmarks with two byte key size & 32-byte value size to better reflect the insecure trie contents.

| Test ID      | C++  | CPython | PyPy | Go  | JS  | Parity |
| ------------ | ------ | ----- | --- | ---- | --- | ------ |
| 1k-1k-32-ran | 23.8   | 369   | 46 | 10.0  | 389 | 1.634  |
| 1k-1k-32-mir | 23.9   | 294   | 45 | 8.0   | 382 | 1.529  |

Note: PyPy times were measured on 1.7 GHz i7