Ethereum brain wallets are formed through applying the SHA3 to a seed to get a result `R`, then using `R` as an accumulator for 16384 repeat SHA3 operations. This process is continued until the result, when used as a private key, forms a valid Direct ICAP (34 digit) address, defined as the first byte of the address being 0.

See [C++ implementation](https://github.com/ethereum/cpp-ethereum/blob/develop/libethcore/KeyManager.cpp#L215-L225) for an example.

```c++
bytes32 toBrain(string seed)
{
	bytes32 r = sha3(seed)
	for (auto i = 0; i < 16384; ++i)
		r = sha3(r)
	bytes32 ret = r
	while (toAddress(ret)[0])
		ret = sha3(ret)
	return ret
}
```