This is just a documentation of the request of the C API described in [this PR](https://github.com/ethereum/ethash/pull/11).

```
typedef int(*Callback)(unsigned);
typedef void const* ethash_full_t;
ethash_full_t ethash_new_full(ethash_params const* params, void const* cache, const uint8_t seed[32]), CallBack c);
void ethash_compute_full(ethash_return_value *ret, ethash_full_t full, ethash_params const *params, const uint8_t header_hash[32], const uint64_t nonce);
void ethash_delete_full(ethash_full_t full);
```

non-zero return from Callback means "cancel DAG creation" - this should cause an immediate return of ethash_new_full with 0.

an object of type ethash_full_t may be tested for validity with != 0

### Example usage:
```
int callback(unsigned _progress)
{
  printf("\rGenerating DAG. %d%% done...", _progress);
  return 0;
}
void main()
{
  ethash_params p;
  uint8_t const* cache;
  uint8_t seed[32];
  // TODO: populate p, seed, cache
  ethash_full_t dag = ethash_new_full(&p, cache, seed, &callback);
  if (!dag)
  {
    printf("Failed generating DAG :-(\n");
    exit(-1);
  }
  printf("DAG Generated OK!\n");

  uint8_t headerHash[32];
  // TODO: populate headerHash
  uint64_t nonce = time(0);
  ethash_return_value ret;
  for (; !isWinner(ret); nonce++)
    ethash_compute_full(&ret, dag, &p, headerHash, nonce);
  printf("Got winner! nonce is %d\n", nonce);
  ethash_delete_full(dag);
}
```