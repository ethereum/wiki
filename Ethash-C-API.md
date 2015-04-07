This is just a documentation of the request of the C API described in [this PR](https://github.com/ethereum/ethash/pull/11).

```c
typedef int(*Callback)(unsigned);
typedef void const* ethash_light_t;
typedef void const* ethash_full_t;
typedef struct ethash_blockhash { uint8_t b[32]; } ethash_blockhash_t;

ethash_light_t ethash_new_light(ethash_params const* params, ethash_blockhash_t *seed);
void ethash_compute_light(ethash_return_value *ret, ethash_light_t light, ethash_params const *params, const uint8_t header_hash[32], const uint64_t nonce);
void ethash_delete_light(ethash_light_t light);

ethash_full_t ethash_new_full(ethash_params const* params, void const* cache, const ethash_blockhash_t *seed, CallBack c);
void ethash_compute_full(ethash_return_value *ret, ethash_full_t full, ethash_params const *params, const uint8_t header_hash[32], const uint64_t nonce);
void ethash_delete_full(ethash_full_t full);
```

non-zero return from Callback means "cancel DAG creation" - this should cause an immediate return of ethash_new_full with 0.

an object of type ethash_full_t may be tested for validity with != 0

### Example usage:
```c
int callback(unsigned _progress)
{
  printf("\rGenerating DAG. %d%% done...", _progress);
  return 0;
}
void main()
{
  ethash_params p;
  ethash_light_t light;
  ethash_blockhash_t seedl
  // TODO: populate p, seed, light
  ethash_full_t dag = ethash_new_full(&p, cache, &seed, &callback);
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