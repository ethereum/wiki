Dagger Hashimoto is a proposed spec for the mining algorithm for Ethereum 1.0. Dagger Hashimoto aims to simultaneously satisfy three goals:

1. **ASIC-resistance**: the benefit from creating specialized hardware for the algorithm should be as small as possible, ideally to the point that even in an economy where ASICs have been developed the speedup us sufficiently small that it is still marginally profitable for users on ordinary computers to mine with spare CPU power.
2. **Light client verifiability**: a block should be relatively efficiently verifiable by a light client.
3. **Full chain storage**: mining should require ownership of the complete blockchain state.

Dagger Hashimoto builds on two key pieces of previous work:

* [Hashimoto](http://vaurum.com/hashimoto.pdf), an algorithm by Thaddeus Dryja which intends to achieve ASIC resistance by being IO-bound, ie. making memory reads the limiting factor in the mining process. The theory is that RAM is in principle inherently a much more generic ingredient than computation, and billions of dollars of research already go into optimizing it for different use cases which often involve near-random access patterns (hence "random access memory"); hence, existing RAM is likely to be moderately close to optimal for evaluating the algorithm. Hashimoto uses the blockchain as a source of data, simultaneously satisfying (1) and (3) above.
* [Dagger](http://vitalik.ca/ethereum/dagger.html), an algorithm by Vitalik Buterin which uses directed acyclic graphs to simultaneously achieve memory-hard computation but memory-easy validation. The core principle is that each individual nonce only requires a small portion of a large total data tree, and recomputing the subtree for each nonce is prohibitive for mining - hence the need to store the tree - but okay for a single nonce's worth of verification. Dagger was proven to be vulnerable to shared memory hardware acceleration [by Sergio Lerner](https://bitslog.wordpress.com/2014/01/17/ethereum-dagger-pow-is-flawed/) and was then dropped in favor of other avenues of research.

Approaches that were tried between Dagger and Dagger Hashimoto but were abandoned include:

* "Blockchain-based proof of work" - a proof of work function that involved running contracts on the blockchain. The approach was abandoned because it was long-range attack vulnerabilities, since attackers can create forks and populate them with contracts that they have a secret fast "trapdoor" execution mechanism for.
* "Random circuit" - a proof of work function that involved generating a new program every 1000 nonces - essentially, choosing a new hash function each time, faster than even FPGAs can reconfigure. The approach was temporarily put aside because it was difficult to see what mechanism one can use to generate random programs that would be general enough so that specialization gains would be low; however, it is still quite viable in theory.

The difference between Dagger Hashimoto and Hashimoto is that, instead of using the blockchain as a data source, Dagger Hashimoto uses a custom-generated 1 GB data set, which updates based on block data every N blocks. The data set is generated using the Dagger algorithm, allowing for the efficient calculation of a subset specific to every nonce for the light client verification algorithm. The difference with Dagger is that, unlike in the original Dagger, the dataset used to query the block is semi-permanent, only being updated at occasional intervals (eg. once per week). This means that the portion of the effort that goes toward generating the dataset is close to zero, so Sergio Lerner's arguments regarding shared memory speedups become negligible.

The code for the algorithm will be defined in Python below. `encode_int`, `cantor_pair` and `P` represent the following algorithms and value:

    def encode_int(x):
        o = ''
        for _ in range(64):
            o = chr(x % 256) + o
            x //= 256 
        return o

    P = (2**256 - 4294968273) ** 2

    def cantor_pair(x,y):
        return ((x+y) * (x+y+1) / 2 + y) % P 

The Cantor pairing function provides a non-commutative, non-associative alternative to functions like OR and XOR, making it more difficult to perform graph-theoretic optimizations.

### Parameters

The parameters used for the algorithm are:

    params = {
      "numdags": 40,         # Number of dags in the dataset
      "n": 250000,           # Size of the dataset
      “h_threshold”: 100000, # Index threshold at which a complex child evaluation mode turns on
      "diff": 2**14,         # Difficulty (adjusted during block exaluation)
      "epochtime": 1000,     # Length of an epoch in blocks (how often the dataset is updated)
      "k": 2,                # Number of parents of a node
      "hk": 8,           # Number of parents during complex child evaluation
      "w": 2,                # Work factor for proof of work during nonce calculations
      "hw": 8,               # Work factor for proof of work during complex evaluation
      "is_serial": 0,        # Is hashimoto modified to be serial?
      "P": (2**256 - 4294968273) ** 2  # Number to modulo everything by (determines
                                       # byte length and maybe some moduli are more secure than others)
    }   

### Dagger graph building

The Dagger graph building primitive is defined as follows:

def produce_dag(params, seed):
      o = [sha3(seed) % params[“P”]]
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

Essentially, it starts off a graph as a single node, `sha3(seed) % P`, and from there starts sequentially adding on other nodes. When a new node is created, `sha3(seed) ** i % P`, where `i` is the index of the node being created and `P` is a large number (in our case slightly under 2^512), is used as a seed to randomly select some indices less than `i` (using `curpicker % i` above), and the values of the nodes at those indices are used in a calculation to generate a value `x`, which is then fed into a small proof of work function to ultimately generate the value of the graph at index `i`.

The graph as a whole has `n` indices; for indices higher than `h_threshold`, we deliberately make the values harder to generate by (1) increasing the number of children, and (2) increasing the strength of the proof of work.

The intent of this construction is to construct a graph in such a way that each individual node in the graph can be reconstructed by computing a subtree of only a small number of nodes, and making a small amount of auxiliary computation. This subtree should be large enough so that it does not open up an efficient memory-free mining implementation, but small enough so that it is practical for a light client, and ideally even a protocol on the blockchain, to verify it. The `h_threshold` parameter is an adaptation of a mechanism found in the original Dagger; its purpose is to make generating the last "row" of the DAG artificially hard, so as to put a high cost premium on any strategies that store less than the entire dataset, thereby eliminating the potential for middle-of-the-road implementations that store some of the DAG and calculate the rest. The question of whether this increased complexity is best achieved through a higher number of parents or increased work is undecided; both options are left tunable as parameters (`hw` and `hk`).

The light client computing function for the DAG works as follows:

    def quick_calc(params, seed, pos):
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

TODO: continue