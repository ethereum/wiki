# Trie

## Standard Mapping

```python
from ethereum import trie, db, utils
import time

MIN_COUNT = 32
ROUNDS = 1000
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
print time.time() - a 
print t.root_hash.encode('hex')
```

## Method

### C++

```
cd libweb3core
cmake -DCMAKE_BUILD_TYPE=Release
make -j8
./libweb3core/bench/bench trie
```

## Results

Test ID is given as `pair_count`-`key_size`-`value_type`, where valid `value_type`s are `ran` (random) and `mir` (same as key).

| Test ID   | Root | C++ time (ms) | SHA3s | Python time (ms) | | SHA3s | Go time (ms) |
| --------- | ---- | ---- | ----- | ------ | -----| ----- | ----- |
| 1k-32-ran | `36f6...93a3` | 34   | 8469  | 369    |      | 7079  | 1.98 |
| 1k-32-mir | `da8a...0ca4` | 22   | 8500  | 294    | 26   | 4228  | 2.76 |