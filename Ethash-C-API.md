This is just a documentation of the request of the C API described in [this PR](https://github.com/ethereum/ethash/pull/11).

```c
typedef int(*Callback)(unsigned);
typedef /*...*/ ethash_light_t;
typedef /*...*/ ethash_full_t;
typedef struct ethash_h256 { uint8_t b[32]; } ethash_h256_t;

ethash_light_t ethash_new_light(ethash_h256_t seed);
ethash_return_value ethash_compute_light(ethash_light_t light, ethash_h256_t header_hash, uint64_t nonce);
void ethash_delete_light(ethash_light_t light);

ethash_full_t ethash_new_full(ethash_light_t cache, CallBack c);
uint64_t ethash_dag_size(ethash_full_t full);
void const* ethash_dag(ethash_full_t full);
ethash_return_value ethash_compute_full(ethash_full_t full, ethash_h256_t header_hash, uint64_t nonce);
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
  ethash_light_t light;
  ethash_h256_t seed;
  // TODO: populate p, seed, light
  ethash_full_t dag = ethash_new_full(light, seed, &callback);
  if (!dag)
  {
    printf("Failed generating DAG :-(\n");
    exit(-1);
  }
  printf("DAG Generated OK!\n");

  ethash_h256_t headerHash;
  // TODO: populate headerHash
  uint64_t nonce = time(0);
  ethash_return_value ret;
  for (; !isWinner(ret); nonce++)
    ret = ethash_compute_full(dag, headerHash, nonce);
  printf("Got winner! nonce is %d\n", nonce);
  ethash_delete_full(dag);
}
```