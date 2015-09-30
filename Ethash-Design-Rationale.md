---
name: Ethash Design Rationale
category: 
---

Ethash is intended to satisfy the following goals:

1. **IO saturation**: The algorithm should consume nearly the entire available memory access bandwidth (this is a strategy toward achieving ASIC resistance, the argument being that commodity RAM, especially in GPUs, is much closer to the theoretical optimum than commodity computing capacity)
2. **GPU friendliness**: We try to make it as easy as possible to mine with GPUs. Targeting CPUs is almost certainly impossible, as potential specialization gains are too great, and there do exist criticisms of CPU-friendly algorithms that they are vulnerable to botnets, so we target GPUs as a compromise.
3. **Light client verifiability**: a light client should be able to verify a round of mining in under 0.01 seconds on a desktop in C, and under 0.1 seconds in Python or Javascript, with at most 1 MB of memory (but exponentially increasing)
4. **Light client slowdown**: the process of running the algorithm with a light client should be much slower than the process with a full client, to the point that the light client algorithm is not an economically viable route toward making a mining implementation, including via specialized hardware.
5. **Light client fast startup**: a light client should be able to become fully operational and able to verify blocks within 40 seconds in Javascript.

### FNV

FNV was used to provide a data aggregation function which is (i) non-associative, and (ii) easy to compute. A commutative and associative alternative to FNV would be XOR.

### Parameters

* A 16 MB cache was chosen because a smaller cache would allow for an ASIC to be produced far too easily using the light-evaluation method. The 16 MB cache still requires a very high bandwidth of cache reading, whereas a smaller cache could be much more easily optimized. A larger cache would lead to the algorithm being too hard to evaluate with a light client.
* 256 parents per DAG item was chosen in order to ensure that time-memory tradeoffs can only be made at a worse-than-1:1 ratio.
* The 1 GB DAG size was chosen in order to require a level of memory larger than the size at which most specialized memories and caches are built, but still small enough for ordinary computers to be able to mine with it.
* The ~0.73x per year growth level was chosen to roughly be balanced with Moore's law increases at least initially (exponential growth has a risk of overshooting Moore's law, leading to a situation where mining requires very large amounts of memory and ordinary GPUs are no longer usable for mining).
* 64 accesses was chosen because a larger number of accesses would lead to light verification taking too long, and a smaller number would mean that the bulk of the time consumption is the SHA3 at the end, not the memory reads, making the algorithm not so strongly IO-bound.
* The epoch length cannot be infinite (ie. constant dataset) because then the algorithm could be optimized via ROM, and very long epoch lengths make it easier to create memory which is designed to be updated very infrequently and only read often. Excessively short epochs would increase barriers to entry as weak machines would need to spend much of their time on a fixed cost of updating the dataset. The epoch length can probably be reduced or increased substantially if design considerations require it.
* The cache size and dataset size is prime in order to help mitigate the risk of cycles appearing in dataset item generation or mining.