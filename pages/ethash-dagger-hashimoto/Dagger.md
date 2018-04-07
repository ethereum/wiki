---
name: Dagger
category: 
---

**Note: we will not be using Dagger as our Proof of Work algorithm. This page is pending deletion**

Over the past five years of experience with Bitcoin and alternative cryptocurrencies, one important property for proof of work functions that has been discovered is that of &quot;memory-hardness&quot; - computing a valid proof of work should require not only a large number of computations, but also a large amount of memory. Currently, there are three major categories of memory-hard functions used in mining: scrypt, Primecoin mining and the birthday problem. However, both are imperfect: neither require nearly as much memory as an ideal memory-hard function could require, and both suffer from time-memory tradeoff attacks, where the function can be computed with significantly less memory than intended at the cost of sacrificing some computational efficiency.

Dagger is a proof of work algorithm intended to solve these problems, providing a memory-hard proof of work based on moderately connected directed acyclic graphs (DAGs, hence the name), which, while far from optimal, has much stronger memory-hardness properties than anything else in use today.

## Why Be Memory-Hard?

The main reason why memory hardness is important is to make the proof of work function resistant to specialized hardware. With Bitcoin, whose mining algorithm requires only a simple SHA256 computation, companies have already existed for over a year that create specialized &quot;application-specific integrated circuits&quot; (ASICs) designed and configured in silicon for the sole purpose of computing billions of SHA256 hashes in an attempt to &quot;mine&quot; a valid Bitcoin block. These chips have no legitimate applications outside of Bitcoin mining and password cracking, and the presence of these chips, which are thousands of times more efficient per dollar and kilowatt hour at computing hashes than generic CPUs, makes it impossible for ordinary users with generic CPU and GPU hardware to compete.

This dominance of specialized hardware has several detrimental effects:

1. **It negates the democratic distribution aspect of cryptocurrency mining**. In a generic hardware-dominated ecosystem, the fact that everyone has a computer guarantees that everyone will have an equal opportunity to earn at least some of the initial money supply. With specialized hardware, this factor does not exist; each economic actor's mining potential is linear (in fact, slightly superlinear) in their quantity of pre-existing capital, potentially exacerbating existing wealth inequalities.
2. **It increases resource waste**. In an efficient market, marginal revenue approaches marginal cost. Since mining revenue is a linear function of money spent on mining hardware and electricity, this also implies that total revenue approaches total cost. Hence, in a specialized hardware dominated ecosystem, the quantity of resources wasted is close to 100% of the security level of the network. In a CPU and GPU-dominated ecosystem, because everyone already has a computer, people do not need to buy specialized hardware for the first few hashes per second worth of mining power. Hence, revenue is sublinear in cost - everyone gets a bit of revenue &quot;for free&quot;. This implies that the quantity of resources wasted by the network is potentially considerably lower than its security parameter.
3. **It centralizes mining in the hands of a few actors** (ie. ASIC manufacturers), making 51% attacks much more likely and potentially opening the network up to regulatory pressure.

Specialized hardware is so powerful because it includes many thousands of circuits specifically designed for computing the proof of work function, allowing the hardware to compute the function thousands of times in parallel. Memory hardness alleviates this problem by making the main limiting factor not CPU power, but memory. One can also make a modest improvement by targeting CPU clock speed, but the extent to which such optimizations can be made is fundamentally limited to a very low value by technological considerations. Thus, improvement through parallelization hits a roadblock: running ten memory-hard computations in parallel requires ten times as much memory. Specialized hardware menufacturers can certainly pack terabytes of memory into their devices, but the effect of this is mitigated by two factors. First, hobbyists can achieve the same effect by simply buying many off-the-shelf memory cards. Second, memory is much more expensive to produce (if measured in laptop equivalents) than SHA256 hashing chips; the RAM used in ordinary computers is already essentially optimal.

## Objections to Memory-Hardness

1. **All algorithms will be vulnerable to ASICs eventually**. This point has become more popular especially after the recent announcement from a company producing ASICs for Scrypt, and essentially states that theoretically any algorithm can be better done by specialized hardware than general purpose hardware, and so there is no point in trying to make algorithms targeted to general purpose hardware. What the argument misses, however, is that the speedup from applying specialized hardware to the moderately memory-hard Scrypt has been demonstrated to be much lower than for Bitcoin. Furthermore, there are indeed reasons to suggest the trend will continue - as described above, the memory found in commodity hardware is much closer to optimal than the computing circuits.
2. **Botnets will take over the mining process**. This point can also be addressed in two ways: empirically and theoretically. Empirically, botnets so far have not been substantially involved in mounting 51% attacks in existing altcoins, generally, they are content simply to extract revenue as "legitimate" miners. Theoretically, the current public prominence of botnets as a threat is backed largely by their performance in network flooding / denial of service attacks - situations where each computer in a botnet is only used for the number of connections that it can make. In the context of mining, however, botnet-infected computers tend to be running older operating systems and have weaker specifications, meaning that they will have a much lower performance and impact on the network than their size might initially suggest.

## Existing alternatives

The existence of every new algorithm, protocol of cryptographic primitive as opposed to existing and more well-tested technologies must always be strongly justified; in as delicate an area as cryptocurrency, caution is very important both because the system will handle millions of dollars of capital and because if a mistake is made there is no way to simply upgrade to a new version with a bugfix. The purpose of this section will be to go through the three existing alternatives and show why they are inadequate.

### Scrypt

Scrypt is the algorithm used by Litecoin, Dogecoin and most other new cryptocurrencies coming out today. A very simplified description of Scrypt-like algorithms is as follows:

1. Let `H[0] = D+N` where D is the underlying data and N is the nonce.
2. For `i` from `1` to `N` let `H[i] = f(H[0] ... H[i-1])` where f is some hash-like function that takes multiple inputs.
3. For `i` from `1` to `M` let `R[i] = random([1 ... M])`
4. `SCRYPT(D,N) = f(H[R[0]] ... H[R[M]]`. If `SCRYPT(D,N)` is low enough, then `N` is a valid nonce.

The idea is that there is no way to practically compute `SCRYPT(D)` without building the entire `H` array and keeping it in memory for the last step. However, Scrypt-like algorithms have a property that inherently limits just how memory-hard they can be: verification requires computing one round of the entire function, so the function is as memory-hard to verify as it is memory-hard to compute. Thus, while scrypt may be viable for achieving memory hardness around a few megabytes per thread, it is a non-starter for memory hardness in the area of hundreds of megabytes per thread. In fact, hundreds of megabytes per thread is arguably necessary; there are already companies [http://www.cryptocoinsnews.com/2013/11/22/alpha-technologies-offer-scrypt-mining-asics/ working on ASICs for scrypt] today.

### Primecoin

Primecoin's algorithm is not designed to be memory-hard, but it does have that property to some extent. The algorithm requires miners to find so-called Cunningham chains of prime numbers - chains of the form `[ n+1, 2n+1, 4n+1 ... n*2^k+1 ]` for a sufficiently large `k`. As it turns out, it is very difficult to do this for any significant length without first filling in a data structure known as a sieve. The advantage of Primecoin is that verification is nearly instant and very memory-cheap; all that one needs to do is run the Fermat primality test to make sure that all of the values are prime. However, the Primecoin algorithm has two weaknesses in this regard: 

1. '''Time-memory tradeoff''' - an ASIC has the option of removing much of the memory required by sacrificing some computational efficiency; even with only 100 KB per thread a miner can be fairly efficient.
2. '''All clear effect''' - as it turns out, it is possible for GPUs and ASICs to have multiple threads share the same memory. Since Primecoin mining only requires the sieve to be filled once, an ASIC can calculate a sieve first and then run thousands of threads through it.

### Birthday attack

The birthday attack algorithm is ingenious, and works as follows:

1. Let `H[i] = sha256(D + N + i)` for `i` from 0 to 2<sup>32</sup> - 1 given data `D` and nonce `N`.
2. If `abs(H[i] - H[j])` is sufficiently low, then `(N,i,j)` is a valid solution triple.

The ingenuity of the protocol comes from the fact that while efficiently computing the birthday attack requires storing the hashes in a data structure so that every new hash can be checked against all previous ones, verifying the solution only requires checking two hashes. The algorithm is resistant against the all-clear effect because memory must be flushed every 2<sup>32</sup> rounds. However, there are several shortcuts and time-memory tradeoff attacks. First, one can optimize by storing only the first few bytes of each hash instead of the entire hash. Second, one can only store hashes with the first two bits being 00; this takes 75% of possible solutions out of consideration, reducing time efficiency by 4x, but it also increases space efficiency by 4x. Finally, there is potential for optimization by examining the various options between storing hashes in a large array, a binary or red-black tree, and other more complex structures; the optimal algorithm may be quite complicated.

Dagger intends to solve all of these issues, having a currency that is memory-hard to computer but memory-easy to verify, has no all clear effect, no time-memory tradeoff even at a 1:1 ratio, and for which a close-to-optimal algorithm is the naive one. 

## Algorithm specification:

Essentially, the Dagger algorithm works by creating a directed acyclic graph (the technical term for a tree where each node is allowed to have multiple parents) with a total of 2<sup>23</sup> - 1 nodes in sequence. Each node depends on 3-15 randomly selected nodes before it. If the miner finds a node between index 2<sup>22</sup> and 2<sup>23</sup> such that this resulting hash is below 2<sup>256</sup> divided by the difficulty parameter, the result is a valid proof of work.

Let `D` be the underlying data (eg. in Bitcoin's case the block header), `N` be the nonce and `||` be the string concatenation operator (ie. `'foo' || 'bar' == 'foobar'`) . The entire code for the algorithm is as follows:



    D(data,xn,0) = sha3(data)
    D(data,xn,n) =
        with v = sha3(data + xn + n)
             L = 2 if n < 2^21 else 11 if n < 2^22 else 3
             a[k] = floor(v/n^k) mod n for 0 <= k < 2
             a[k] = floor(v/n^k) mod 2^22 for 2 <= k < L
        sha3(v ++ D(data,xn,a[0]) ++ D(data,xn,a[1]) ++ ... ++ D(data,xn,a[L-1]))



## Properties:
Objective: find `xn`, `n` such that `n > 2^22` and `D(data,xn,n) &lt; 2^256 / diff`

1. With 2<sup>28</sup> bytes (256 MB) of memory, the optimal algorithm is to run `D` all the way through from start to finish. 
2. One potential problem is lazy evaluation; parts of the tree can be evaluated only as needed in order to reduce the number of hashes required. However, because a (pseudo-) random node out of 2<sup>25</sup> is taken 2<sup>28</sup> times, we can statistically estimate that each node has a 1 / e<sup>8</sup> change of remaining unused - only about 0.03%. Hence, the benefit from lazy evaluation is insubstantial.
3. It is possible to run the algorithm with much less memory using lazy evaluation. However, empirical tests show that computing one nonce requires 3000 - 25000 hashes.
4. Verification also requires 3000 - 25000 hashes.
5. Because the 2^21 to 2^22 phase requires 11 parents, the time-memory tradeoff attack is severely weakened - attempting to store 2^21 nodes instead of 2^23 reduces memory usage by a factor of 4 but slows down computation by a factor about 20. Thus, no practical time-memory tradeoff attack exists; close to the full 256 MB is required for any reasonable level of efficiency.

## Conclusion 

This algorithm provides a proof of work mining function with memory hardness properties that are not ideal, but that are nevertheless a massive improvement over anything available previously. It takes 512 MB to evaluate, 112 KB memory and 4078 hashes to verify, and even the tinest time-memory tradeoff is not worthwhile to implement because of the bottom-level branching adjustment. These parameters allow Dagger to be much more daring in its memory requirements than Primecoin or scrypt, asking for 512 MB of RAM for a single thread. Because the primary determinant of hardness is memory, and not computation, specialized hardware has only a tiny advantage; even an optimal Dagger mining ASIC would have little to offer over a hobbyist purchasing hundreds of gigabytes of memory cards off the shelf and plugging them into a medium-power GPU. And even in such an equilibrium, mining with ordinary CPUs will likely continue to be practical.

## Acknowledgements

* Thanks to Adam Back and Charles Hoskinson, for discussion and critique of earlier drafts of the protocol
* See also: [Fabien Coelho's paper](http://www.cri.ensmp.fr/classement/doc/A-370-v1.pdf), in which Fabien Coelho had independently discovered a similar algorithm in 2005.