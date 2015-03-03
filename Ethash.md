**This spec is REVISION 18. Whenever you substantively (ie. not clarifications) update the algorithm, please update the revision number in this sentence. Also, in all implementations please include a spec revision number**

Ethash is the planned PoW algorithm for Ethereum 1.0. It is the latest version of Dagger-Hashimoto, although it can no longer appropriately be called that since many of the original features of both algorithms have been drastically changed in the last month of research and development. See [https://github.com/ethereum/wiki/wiki/Dagger-Hashimoto](https://github.com/ethereum/wiki/wiki/Dagger-Hashimoto) for the original version.

The general route that the algorithm takes is as follows:

1. There exists a **seed** which can be computed for each block by scanning through the block headers up until that point.
2. From the seed, one can compute a **1 MB pseudorandom cache**. Light clients store the cache.
3. From the cache, we can generate a **1 GB dataset** ("the DAG"), with the property that each item in the dataset depends on only a small number of items from the cache. Full clients and miners store the DAG.  The DAG grows exponentially with time.
4. Mining involves grabbing random slices of the DAG and hashing them together. Verification can be done with low memory by using the cache to regenerate the specific pieces of the DAG that you need, so you only need to store the cache.

The large dataset is updated once every 1000 blocks, so the vast majority of a miner's effort will be reading the dataset, not making changes to it. The dataset also grows over time; it starts off at 1 GB, is 8GB by December 2015, and rises linearly 15GB by December 2016.

The specification for the algorithm is written in python to give a balance between clarity and exactness. If you are interested in actually running the spec as code, then you can; simply prepend the code given at the start of the appendix.

See [https://github.com/ethereum/wiki/wiki/Ethash-Design-Rationale](https://github.com/ethereum/wiki/wiki/Ethash-Design-Rationale) for design rationale considerations for this algorithm.

### Definitions

We employ the following definitions:

```
WORD_BYTES=4               # bytes in word
DAG_COEFF=0.0316465        # the DAG coefficient
CACHE_BYTES_INIT=2**25     # bytes in cache at genesis
CACHE_BYTES_GROWTH=2**19   # growth per epoch (~1 MB per year)
EPOCH_LENGTH=30000         # blocks per epoch
MIX_BYTES=128              # width of mix
HASH_BYTES=64              # hash length in bytes
DAG_PARENTS=1024           # number of parents of each dag element
CACHE_ROUNDS=3             # number of processing rounds in cache production
ACCESSES=64               # number of accesses in hashimoto loop
```

### Parameters

The parameters for Ethash's cache and DAG depend on the block number. In order to compute the size of the dataset and the cache at a given block number, we use the following functions:

```python
from math import exp
def get_datasize(block_number):
    datasize_in_bytes = DAG_BYTES_INIT * int(exp(DAG_COEFF * block_number // EPOCH_LENGTH))
    while not _isprime(datasize_in_bytes // MIX_BYTES):
        datasize_in_bytes -= MIX_BYTES
    return datasize_in_bytes

def get_cachesize(block):
    cachesize_in_bytes = DAG_BYTES_INIT * int(exp(DAG_COEFF * block_number // EPOCH_LENGTH)) // DAG_PARENTS
    while not _isprime(cachesize_in_bytes // HASH_BYTES):
        cachesize_in_bytes -= HASH_BYTES
    return cachesize_in_bytes

def _isprime(n):
    for i in range(2, int(n ** 0.5 + 1)):
        if n % i == 0:
            return False
    return True
```

Essentially, we are keeping the size of the dataset to always be equal to the highest prime below a linearly growing function, so on average in the long term the dataset will grow roughly linearly.  Tabulated version of ``get_datasize` and `get_cachesize` have been provided in the appendix.

`sha3_256` and `sha3_512` are assumed to accept word arrays or strings as input and output a word array. Many operations inside of the ethash spec operate on word arrays.

We can now get the parameters:

```python
def get_params(block):
    params = {
        "full_size": get_datasize(block),      # bytes in DAG
        "cache_size": get_cachesize(block)      # bytes in cache        
    }
    return params
```

### Cache Generation

Now, we specify the function for producing a cache:

```python
def mkcache(params, seed):
    n = params["cache_size"] // HASH_BYTES

    # Sequentially produce the initial dataset
    o = [sha3_512(seed)]
    for i in range(1, n):
        o.append(sha3_512(o[-1]))

    for _ in range(CACHE_ROUNDS):
        for i in range(n):
            v = o[i][0] % n
            o[i] = sha3_512(map(xor, o[(i-1+n)%n], o[v]))

    return o
```

[Sergio2014]: http://www.hashcash.org/papers/memohash.pdf

The cache production process involves first sequentially filling up 32 MB of memory, then performing two passes of Sergio Demian Lerner's *RandMemoHash* algorithm from [*Strict Memory Hard Hashing Functions* (2014)](http://www.hashcash.org/papers/memohash.pdf). The output is a set of 524288 64-byte values.

### Data aggregation function

We use an algorithm inspired by the [FNV hash](https://en.wikipedia.org/wiki/Fowler%E2%80%93Noll%E2%80%93Vo_hash_function) in some cases as a non-associative substitute for XOR.

```python
FNV_PRIME = 0x01000193

def fnv(v1, v2):
    return (v1 * FNV_PRIME ^ v2) % 2**32
```

### Full dataset calculation

Each 64-byte item in the full 1 GB dataset is computed as follows:

```python
def calc_dag_item(params, cache, i):
    n = params["cache_size"] // HASH_BYTES
    r = HASH_BYTES // WORD_BYTES
    mix = copy.copy(cache[i % n])
    mix[0] ^= i
    mix = sha3_512(mix)
    for j in range(DAG_PARENTS):
        cache_index = fnv(i ^ j, mix[j % r])
        mix = map(fnv, mix, cache[cache_index % n])
    return sha3_512(mix)
```

Essentially, we use our RNG to generate a seed for the specific item, and use that as the seed for another RNG which picks 64 indices from the cache. We initialize a mix to equal the 512-bit big-endian representation of the index (the purpose of this is to make sure all of the entropy from the index, and not just the first 32 bytes as does the RNG, in computing the final DAG value), and then repeatedly run through random parts of the cache and use our aggregation function to combine the data together.

The entire DAG is then generated by:

```python
def calc_dag(params, cache):
    return [calc_dag_item(params, cache, i) for i in range(params["full_size"] // MIX_BYTES)]
```

### Main Loop

Now, we specify the main "hashimoto"-like loop, where we aggregate data from the full dataset in order to produce our final value for a particular header and nonce:

```python
def hashimoto(params, header, nonce, dagsize, dag_lookup):
    n = dagsize / HASH_BYTES
    w = MIX_BYTES / WORD_BYTES
    s = sha3_512(header + nonce)
    mix = []
    for _ in range(MIX_BYTES / HASH_BYTES):
        mix.extend(s)
    for i in range(ACCESSES):
        p = fnv(i ^ s[0], mix[i % w]) % (n // w) * w
        newdata = []
        for j in range(MIX_BYTES / HASH_BYTES):
            newdata.extend(dag_lookup(p + j))
        mix = map(fnv, mix, newdata)
    return sha3_256(s+sha3_256(mix))

def hashimoto_light(params, cache, header, nonce):
    return hashimoto(params, header, nonce, params["full_size"], lambda x: calc_dag_item(params, cache, x))

def hashimoto_full(params, dag, header, nonce):
    return hashimoto(params, header, nonce, len(dag), lambda x: dag[x])
```

Essentially, we maintain a "mix" 128 bytes wide, and repeatedly sequentially fetch 128 bytes from the full dataset and use the `fnv` function to combine it with the mix. 128 bytes of sequential access are used so that each round of the algorithm always fetches a full page from RAM, minimizing translation lookaside buffer misses which ASICs would theoretically be able to avoid.

If the output of this algorithm is below the desired target, then the nonce is valid. Note that the double application of `sha3_256` ensures that there exists an intermediate nonce which can be provided to prove that at least a small amount of work was done; this quick outer PoW verification can be used for anti-DDoS purposes.  It also serves to provide statistical assurance that the result is an unbiased, 256 bit number.

### Mining

The mining algorithm is defined as follows:

```python
def mine(params, dag, header, difficulty):
    from random import randint
    nonce = randint(0, 2**64)
    while decode_int(hashimoto_full(params, dag, header, nonce)) < difficulty:
        nonce += 1
        nonce %= 2**64
    return nonce
```

### Defining the Seed Hash

In order to compute the seed hash for a given block, we use the following algorithm:

```python
 def get_seedhash(block):
     seedBlockNum = max(0, block.number // EPOCH_LENGTH - 1) * EPOCH_LENGTH
     return ChainManager.getBlockByNumber(seedBlockNum).hash()
```

Since the seed block number for a particular block is a little more than an EPOCH_LENGTH in the past, a *double buffer* can be used to store DAGs (similar to the pattern used in computer graphics, see [wikipedia](https://en.wikipedia.org/wiki/Multiple_buffering#Double_buffering_in_computer_graphics)).

### Appendix

The following code should be prepended if you are interested in running the above python spec as code.

```python
import sha3, copy

# Assumes little endian bit ordering (same as Intel architectures)
def decode_int(s):
   return int(s[::-1].encode('hex'), 16) if s else 0

def encode_int(s):
   a = "%x" % s
   return '' if s == 0 else ('0' * (len(a) % 2) + a).decode('hex')[::-1]

def zpad(s, length):
   return s + '\x00' * max(0, length - len(s))

def hash_words(h, sz, x):
    if isinstance(x, list):
        x = ''.join([zpad(encode_int(a), WORD_BYTES) for a in x])
    y = h(x)
    return [decode_int(y[i:i+WORD_BYTES]) for i in range(0, sz, WORD_BYTES)]

# sha3 hash function, outputs 64 bytes
def sha3_512(x):
    return hash_words(lambda v: sha3.sha3_512(v).digest(), 64, x)

def sha3_256(x):
    return hash_words(lambda v: sha3.sha3_256(v).digest(), 32, x)

def xor(a, b):
    return a ^ b

test_params = {
    "cache_size": CACHE_BYTES_INIT // 100,
    "full_size": DAG_BYTES_INIT // 100
}
```

The following lookup tables provide approximately 7 years of tabulated DAG and cache sizes.  They were generated with the following *Mathematica* functions:

```mathematica
GetDataSizes[n_] := With[
    {DAGSizeBytesInit = 2^30, 
     DAGSizeBytesGrowth = 2^17, 
          MixBytes = 2^12,
     DAGCoeff = 0.00263721}, 
    Module[{j = 0, i = 100}, 
           Reap[
      While[j < n, 
       While[Prime[i]*MixBytes < DAGSizeBytesInit * E^(DAGCoeff*j), 
        i++]; 
       Sow[Prime[i - 1]*MixBytes]; j++]]]][[2]][[1]]
            
GetCacheSizes[n_]:= 
With[{ CacheSizeBytesInit=2^25,
       DAGSizeBytesGrowth=2^17,
       HashBytes=2^12},
   Module[{j=0,i=100},
      Reap[
         While[j<n,
            While[Prime[i]*HashBytes < 
                  (DAGSizeBytesInit + DAGSizeBytesGrowth * j) / 32, i++];
            Sow[Prime[i-1]*HashBytes];
            j++]]]][[2]][[1]]
```

```python
def get_datasize(block_number):
    return data_sizes[block_number // EPOCH_LENGTH]

def get_cachesize(block_number):
    return cache_sizes[block_number // EPOCH_LENGTH]

data_sizes = [1073721344U, 1108226048U, 1143885824U, 1180659712U, 1218596864U,
        1257795584U, 1298255872U, 1339936768U, 1383092224U, 1427402752U,
        1473458176U, 1520807936U, 1569673216U, 1620144128U, 1672278016U,
        1726042112U, 1781542912U, 1838804992U, 1897934848U, 1958998016U,
        2021961728U, 2086998016U, 2154057728U, 2223296512U, 2294779904U,
        2368581632U, 2444750848U, 2523344896U, 2604511232U, 2688249856U,
        2774577152U, 2863910912U, 2955931648U, 3050942464U, 3149049856U,
        3250393088U, 3354873856U, 3462705152U, 3574116352U, 3688951808U,
        3807588352U, 3930066944U, 4056395776U, 4186845184U, 4321439744U,
        4460376064U, 4603817984U, 4751814656U, 4904587264U, 5062283264U,
        5225107456U, 5393108992U, 5566435328U, 5745455104U, 5930217472U,
        6120894464U, 6317600768U, 6520754176U, 6730461184U, 6946820096U,
        7170248704U, 7400665088U, 7638716416U, 7884345344U, 8137854976U,
        8399417344U, 8669556736U, 8948322304U, 9235959808U, 9532960768U,
        9839448064U, 10155855872U, 10482405376U, 10819457024U, 11167338496U,
        11526311936U, 11896999936U, 12279476224U, 12674289664U, 13081759744U,
        13502451712U, 13936529408U, 14384705536U, 14847127552U, 15324565504U,
        15817142272U, 16325742592U, 16850776064U, 17392603136U, 17951731712U,
        18528980992U, 19124678656U, 19739537408U, 20374319104U, 21029343232U,
        21705601024U, 22403362816U, 23123750912U, 23867256832U, 24634658816U,
        25426694144U, 26244337664U, 27088154624U, 27959103488U, 28858068992U,
        29785862144U, 30743621632U, 31732092928U, 32752381952U, 33805438976U,
        34892345344U, 36014280704U, 37172006912U, 38367342592U, 39601033216U,
        40874233856U, 42188460032U, 43544932352U, 44945076224U, 46390145024U,
        47881662464U, 49421283328U, 51010260992U, 52650266624U, 54343241728U,
        56090497024U, 57894006784U, 59755409408U, 61676736512U, 63659511808U,
        65706610688U, 67819245568U, 69999824896U, 72250519552U, 74573484032U,
        76971298816U, 79446052864U, 82000531456U, 84637036544U, 87358369792U,
        90166980608U, 93066285056U, 96058585088U, 99146928128U, 102334885888U,
        105625317376U, 109021425664U, 112526741504U, 116144672768U, 119879143424U,
        123733594112U, 127711956992U, 131818123264U, 136056516608U, 140431028224U,
        144946270208U, 149606723584U, 154416951296U, 159381901312U, 164506415104U,
        169795760128U, 175255072768U, 180890046464U, 186706137088U, 192709193728U,
        198905311232U, 205300649984U, 211901517824U, 218714796032U, 225747021824U,
        233005371392U, 240497053696U, 248229711872U, 256210939904U, 264448864256U,
        272951578624U, 281727684608U, 290785980416U, 300135354368U, 309785612288U,
        319745953792U, 330026725376U, 340637888512U, 351590354944U, 362894921728U,
        374562910208U, 386606043136U, 399036362752U, 411866435584U, 425109090304U,
        438777516032U, 452885245952U, 467446829056U, 482476470272U, 497989357568U,
        514000949248U, 530527522816U, 547585273856U, 565191675904U, 583363907584U,
        602120753152U, 621480316928U, 641462669312U, 662087438336U, 683375218688U,
        705347547136U, 728026353664U, 751434305536U, 775594799104U, 800532262912U,
        826271510528U, 852838223872U, 880259182592U, 908561936384U, 937774575616U,
        967926444032U, 999047794688U, 1031169691648U, 1064324599808U,
        1098545475584U, 1133866594304U, 1170323329024U, 1207952257024U,
        1246791102464U, 1286878785536U, 1328255209472U, 1370962178048U,
        1415042166784U, 1460539469824U, 1507499659264U, 1555969748992U,
        1605998252032U, 1657635254272U, 1710932455424U, 1765943422976U,
        1822723272704U, 1881328365568U, 1941818208256U, 2004252577792U,
        2068694798336U, 2135208710144U, 2203861274624U, 2274721206272U,
        2347859439616U, 2423349243904U, 2501266272256U, 2581688520704U,
        2664696614912U, 2750373597184U, 2838805311488U, 2930080329728U,
        3024290091008U, 3121528877056U, 3221894033408U, 3325486600192U,
        3432409526272U, 3542770561024U, 3656679968768U, 3774251831296U,
        3895603761152U, 4020857606144U, 4150138867712U, 4283576651776U,
        4421304930304U, 4563461476352U, 4710188552192U, 4861633589248U,
        5017947828224U, 5179287924736U, 5345815588864U, 5517697593344U,
        5695106060288U, 5878218674176U, 6067218796544U, 6262295769088U,
        6463644971008U, 6671468056576U, 6885973250048U, 7107375296512U,
        7335895986176U, 7571764170752U, 7815216287744U, 8066496008192U,
        8325854973952U, 8593552879616U, 8869858168832U, 9155047411712U,
        9449406132224U, 9753229348864U, 10066821197824U, 10390495842304U,
        10724577513472U, 11069400764416U, 11425310928896U, 11792664629248U,
        12171829686272U, 12563185823744U, 12967125200896U, 13384052076544U,
        13814384488448U, 14258553131008U, 14717002993664U, 15190193139712U,
        15678597689344U, 16182705614848U, 16703022002176U, 17240067780608U,
        17794381164544U, 18366517121024U, 18957048598528U, 19566567452672U,
        20195683856384U, 20845028036608U, 21515250249728U, 22207021846528U,
        22921035747328U, 23658007097344U, 24418673840128U, 25203798274048U,
        26014166429696U, 26850589929472U, 27713906765824U, 28604981202944U,
        29524706480128U, 30474003042304U, 31453821964288U, 32465144762368U,
        33508984188928U, 34586385682432U, 35698428669952U, 36846226542592U,
        38030929235968U, 39253723131904U, 40515833212928U, 41818523291648U,
        43163098427392U, 44550905049088U, 45983333249024U, 47461817888768U,
        48987839451136U, 50562926710784U, 52188657307648U, 53866659377152U,
        55598613450752U, 57386254544896U, 59231372750848U, 61135816609792U,
        63101493096448U, 65130371411968U, 67224483401728U, 69385926479872U,
        71616865783808U, 73919535271936U, 76296241983488U, 78749366013952U,
        81281364422656U, 83894773116928U, 86592209432576U, 89376376016896U,
        92250060541952U, 95216141398016U, 98277589528576U, 101437471633408U,
        104698951921664U, 108065297395712U, 111539879653376U, 115126178787328U,
        118827786948608U, 122648411410432U, 126591878975488U, 130662139326464U,
        134863269343232U, 139199476576256U, 143675104202752U, 148294634983424U,
        153062695800832U, 157984062263296U, 163063663464448U, 168306587250688U,
        173718084718592U, 179303575998464U, 185068655415296U, 191019097182208U,
        197160861495296U, 203500099457024U, 210043160489984U, 216796597694464U,
        223767176327168U, 230961877004288U, 238387906064384U, 246052701343744U,
        253963940114432U, 262129545539584U, 270557696872448U, 279256835411968U,
        288235673636864U, 297503205453824U, 307068712546304U, 316941775556608U,
        327132283629568U, 337650443128832U, 348506788777984U, 359712194686976U,
        371277883559936U, 383215439540224U, 395536819613696U, 408254363693056U,
        421380810723328U, 434929307070464U, 448913423224832U, 463347165097984U,
        478244989751296U, 493621818437632U, 509493052346368U, 525874588110848U,
        542782832963584U, 560234722373632U, 578247735660544U, 596839914450944U,
        616029880315904U, 635836854112256U, 656280673816576U, 677381815635968U,
        699161414979584U, 721641285111808U, 744843941859328U, 768792624959488U,
        793511320907776U, 819024787509248U, 845358578987008U, 872539070869504U,
        900593486508032U, 929549925036032U, 959437389320192U, 990285813673984U,
        1022126095495168U, 1054990125740032U, 1088910820765696U,
        1123922154582016U, 1160059194896384U, 1197358135291904U,
        1235856334876672U, 1275592352002048U, 1316605986050048U,
        1358938315870208U, 1402631741059072U, 1447730024132608U,
        1494278335172608U, 1542323296301056U, 1591913028448256U,
        1643097200644096U, 1695927078105088U, 1750455574237184U,
        1806737304326144U, 1864828639203328U, 1924787763064832U,
        1986674729529344U, 2050551523741696U, 2116482124623872U,
        2184532566536192U, 2254771008679936U, 2327267800543232U,
        2402095553744896U, 2479329215746048U, 2559046141825024U,
        2641326175858688U, 2726251728490496U, 2813907860058112U,
        2904382365544448U, 2997765863305216U, 3094151885369344U,
        3193636970254336U, 3296320761032704U, 3402306104324096U,
        3511699154440192U, 3624609477087232U, 3741150162661376U,
        3861437936070656U, 3985593276403712U, 4113740536311808U,
        4246008065929216U, 4382528343003136U, 4523438104440832U,
        4668878483664896U, 4818995151884288U, 4973938464034816U,
        5133863609430016U, 5298930766876672U, 5469305265762304U,
        5645157750910976U, 5826664353869824U, 6014006869159936U,
        6207372937326592U, 6406956230586368U, 6612956649140224U,
        6825580520886272U, 7045040806703104U, 7271557315457024U,
        7505356922851328U, 7746673799991296U]

cache_sizes = [1048384U, 1081792U, 1116736U, 1152832U, 1189952U, 1227712U, 1267264U, 1308352U,
        1350464U, 1393472U, 1438912U, 1484992U, 1531456U, 1581376U, 1630144U, 1684544U,
        1739456U, 1795648U, 1852736U, 1912384U, 1974592U, 2036288U, 2101952U, 2171072U,
        2238784U, 2312768U, 2385728U, 2464064U, 2542912U, 2625088U, 2709568U, 2796224U,
        2885312U, 2979136U, 3075136U, 3174208U, 3274816U, 3381568U, 3489344U, 3601216U,
        3718336U, 3837248U, 3960256U, 4087232U, 4219456U, 4355776U, 4495936U, 4640192U,
        4789184U, 4943552U, 5100736U, 5265856U, 5434816U, 5609536U, 5790784U, 5976512U,
        6168128U, 6367808U, 6571456U, 6783808U, 7001408U, 7227328U, 7459136U, 7699136U,
        7946944U, 8202176U, 8466368U, 8738368U, 9019456U, 9309376U, 9608384U, 9916352U,
        10235968U, 10565696U, 10905152U, 11255872U, 11617472U, 11991488U, 12377152U,
        12774592U, 13185728U, 13609664U, 14047424U, 14499136U, 14961728U, 15445952U,
        15942848U, 16454848U, 16984384U, 17530816U, 18093632U, 18676288U, 19275712U,
        19896512U, 20535488U, 21195712U, 21877696U, 22581824U, 23307712U, 24054848U,
        24830528U, 25628224U, 26451136U, 27303104U, 28181696U, 29086912U, 30022336U,
        30988352U, 31983808U, 33012032U, 34073792U, 35170112U, 36299968U, 37467968U,
        38672576U, 39915712U, 41198656U, 42524224U, 43890752U, 45302848U, 46758592U,
        48262336U, 49813312U, 51414976U, 53069504U, 54775232U, 56537024U, 58354112U,
        60230336U, 62167744U, 64165312U, 66229568U, 68358848U, 70556608U, 72824896U,
        75167296U, 77584064U, 80078528U, 82652992U, 85310656U, 88052672U, 90884672U,
        93806656U, 96822848U, 99935936U, 103149248U, 106465472U, 109888448U, 113422528U,
        117069376U, 120829888U, 124718656U, 128728384U, 132866752U, 137138752U, 141548096U,
        146099648U, 150797632U, 155645888U, 160650176U, 165816128U, 171146816U, 176649664U,
        182329408U, 188188736U, 194243264U, 200488768U, 206934976U, 213588416U, 220455616U,
        227544256U, 234860224U, 242410048U, 250206016U, 258249536U, 266554048U, 275124544U,
        283968704U, 293100352U, 302523328U, 312250816U, 322289728U, 332654144U, 343349696U,
        354388288U, 365783872U, 377542336U, 389683648U, 402212416U, 415145408U, 428493248U,
        442270784U, 456490304U, 471167936U, 486317248U, 501953728U, 518092864U, 534750016U,
        551944768U, 569690432U, 588006976U, 606914368U, 626427712U, 646568896U, 667358656U,
        688815808U, 710961088U, 733822528U, 757416256U, 781769536U, 806904128U, 832849216U,
        859627456U, 887266496U, 915795008U, 945239872U, 975632192U, 1007000768U,
        1039379392U, 1072796992U, 1107290432U, 1142892992U, 1179640256U, 1217569088U,
        1256716096U, 1297123904U, 1338829504U, 1381875904U, 1426308032U, 1472166848U,
        1519500992U, 1568355136U, 1618783552U, 1670831936U, 1724553664U, 1780000064U,
        1837233472U, 1896305984U, 1957277632U, 2020209344U, 2085164608U, 2152207808U,
        2221406272U, 2292828992U, 2366551744U, 2442640576U, 2521179712U, 2602242752U,
        2685907648U, 2772270272U, 2861404736U, 2953406912U, 3048365888U, 3146380352U,
        3247544512U, 3351961664U, 3459736256U, 3570975424U, 3685791808U, 3804300608U,
        3926618944U, 4052869696U, 4183179584U, 4317679936U, 4456503872U, 4599793216U,
        4747688896U, 4900339648U, 5057897408U, 5220520768U, 5388374848U, 5561624896U,
        5740447168U, 5925013568U, 6115522496U, 6312153152U, 6515104576U, 6724582208U,
        6940795712U, 7163958976U, 7394300608U, 7632045376U, 7877436352U, 8130717632U,
        8392141504U, 8661969728U, 8940475072U, 9227933248U, 9524636864U, 9830879936U,
        10146967744U, 10473218752U, 10809961664U, 11157527488U, 11516273728U, 11886552256U,
        12268733888U, 12663207104U, 13070362304U, 13490609344U, 13924367936U, 14372072128U,
        14834172224U, 15311128384U, 15803422784U, 16311543616U, 16836003136U, 17377324352U,
        17936049728U, 18512742592U, 19107975616U, 19722347584U, 20356467776U, 21010986176U,
        21686544832U, 22383823552U, 23103522496U, 23846361152U, 24613082048U, 25404458176U,
        26221278784U, 27064360384U, 27934551872U, 28832719424U, 29759768512U, 30716622656U,
        31704242368U, 32723615296U, 33775766848U, 34861746112U, 35982642112U, 37139578816U,
        38333713472U, 39566243008U, 40838399936U, 42151462336U, 43506741056U, 44905597504U,
        46349428672U, 47839686976U, 49377854912U, 50965484992U, 52604158528U, 54295520192U,
        56041262272U, 57843136192U, 59702945216U, 61622550592U, 63603877952U, 65648909504U,
        67759693504U, 69938343616U, 72187045312U, 74508048448U, 76903677632U, 79376331968U,
        81928488896U, 84562703936U, 87281616832U, 90087944768U, 92984512576U, 95974208576U,
        99060028736U, 102245069504U, 105532515904U, 108925663424U, 112427908928U,
        116042759744U, 119773838656U, 123624879808U, 127599743552U, 131702411072U,
        135936988736U, 140307714496U, 144818979392U, 149475285184U, 154281308992U,
        159241857856U, 164361897536U, 169646566976U, 175101145792U, 180731107904U,
        186542083136U, 192539902784U, 198730563776U, 205120272832U, 211715425088U,
        218522633024U, 225548706752U, 232800687424U, 240285840832U, 248011659328U,
        255985883968U, 264216500032U, 272711751232U, 281480149568U, 290530473536U,
        299871788992U, 309513448256U, 319465120064U, 329736756416U, 340338660416U,
        351281440192U, 362576058176U, 374233824704U, 386266424512U, 398685900608U,
        411504695488U, 424735651264U, 438392013632U, 452487464768U, 467036122304U,
        482052556096U, 497551806016U, 513549401536U, 530061357376U, 547104213184U,
        564695054272U, 582851477696U, 601591680064U, 620934427712U, 640899095488U,
        661505678528U, 682774819136U, 704727817408U, 727386659264U, 750774047552U,
        774913398848U, 799828893632U, 825545485888U, 852088935872U, 879485824064U,
        907763597504U, 936950569792U, 967075989184U, 998170014272U, 1030263793088U,
        1063389472576U, 1097580228928U, 1132870307648U, 1169295053504U, 1206890951872U,
        1245695655488U, 1285748030912U, 1327088198336U, 1369757557312U, 1413798847936U,
        1459256185792U, 1506175093312U, 1554602566592U, 1604587107008U, 1656178785088U,
        1709429271488U, 1764391895744U, 1821121717952U, 1879675548992U, 1940112038848U,
        2002491722048U, 2066877073216U, 2133332582464U, 2201924810944U, 2272722460864U,
        2345796438976U, 2421219937216U, 2499068496832U, 2579420086976U, 2662355203264U,
        2747956894528U, 2836310903744U, 2927505724736U, 3021632698432U, 3118786103744U,
        3219063239872U, 3322564554944U, 3429393704768U, 3539657690944U, 3653466955456U,
        3770935484224U, 3892180931776U, 4017324741952U, 4146492251456U, 4279812834496U,
        4417420023488U, 4559451643328U, 4706049952064U, 4857361781056U, 5013538677568U,
        5174737076672U, 5341118423488U, 5512849365568U, 5690101908032U, 5873053583168U,
        6061887630272U, 6256793192128U, 6457965476672U, 6665605976896U, 6879922662208U,
        7101130188608U, 7329450120128U, 7565111132608U]
```