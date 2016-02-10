# Trie

*TODO* Separate benchmarks for populate->root usage (receipt & transaction tries).

Common Trie benchmarks. The point of this is to give a controlled test between clients that reflects typical on-chain situations. We do this by defining a common dataset of key/value pairs for insertion into the trie. The dataset is then inserted into the trie with root hashes being computed at specific intervals ("era_size"). This number represents the number of update operations per root calculation.

Sensible implementations may precompute the dataset to avoid the additional burden of SHA3 computation.

## Standard Dataset

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

##### CPython / PyPy
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

#### JS
```
git clone https://github.com/ethereumjs/merkle-patricia-tree.git`
cd merkle-patricia-tree
npm install
node ./benchmarks/random.js
```

## Results

Test ID is given as `pair_count`-`era_size`-`key_size`-`value_type`, where valid `value_type`s are `ran` (`SYMMETRIC = False`) and `mir` (`SYMMETRIC = True`).

The standard test is `1k-9-32-ran`. The 9 that is used as `era_size` is determined empirically from the Frontier mainnet: the mean number of insertions per commit on all secure tries from block #0 to #900,000 is 9.29.

| Client      | Time (ms) | SHA3s |
| ----------- | --------- | ----- |
| C++ | 23.8 | 8469 |
| CPython | 369 | 7079 |
| PyPy | 45 | |
| Go | 23.6 | |
| Pure JS | 374 | |
| Parity | 11.6 | 4221 |

### Other results (values other than 9 inserts/commit)

Note clients which do not do bulk insertion optimisations (C++, Python) will have the same time regardless of era_size.

All times in ms.

| Test ID      | C++  | CPython | PyPy | Go  | JS  |
| ------------ | ------ | ----- | --- | ---- | --- |
| 1k-3-32-ran  | 23.8   | 369   | 45 | 45.7  | 388 |
| 1k-5-32-ran  | 23.8   | 369   | 41 | 28.5  | 374 |
| 1k-1k-32-ran | 23.8   | 369   | 46 | 10.0  | 389 |
| 1k-1k-32-mir | 23.9   | 294   | 45 | 8.0   | 382 |

Note: PyPy times were measured on 1.7 GHz i7