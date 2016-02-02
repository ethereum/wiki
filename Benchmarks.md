# Trie

Common Trie benchmarks. The point of this is to give a controlled test between clients that reflects typical on-chain situations. We do this by defining a common dataset of key/value pairs for insertion into the trie. The dataset is then inserted into the trie with root hashes being computed at specific intervals ("era_size"). This number represents the number of update operations per root calculation; three standard values are provided to model a simple transaction (3), a contract transaction making several `SSTORE`s (5) and a more complex contract transaction (9).

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

### Go

```
sudo nice -n -19 godep go test  -run=- -bench=Std ./trie
```

## Results

Test ID is given as `pair_count`-`era_size`-`key_size`-`value_type`, where valid `value_type`s are `ran` (`SYMMETRIC = False`) and `mir` (`SYMMETRIC = True`). Note clients which do not do bulk insertion optimisations (C++, Python) will have the same time for each test.


| Test ID      | C++ time (ms) | SHA3s | Python time (ms) | | SHA3s | Go time (ms) |
| ------------ | ---- | ----- | ------ | ----- | ----- | ----- |
| 1k-3-32-ran  | 34   | 8469  | 369    |       | 7079  | 45.7  |
| 1k-5-32-ran  | 34   | 8469  | 369    |       | 7079  | 28.5  |
| 1k-9-32-ran  | 34   | 8469  | 369    |       | 7079  | 23.6 |
| 1k-1k-32-ran | 34   | 8469  | 369    |       | 7079  | 10.0  |
| 1k-1k-32-mir | 22   | 8500  | 294    | 26    | 4228  | 8.0  |