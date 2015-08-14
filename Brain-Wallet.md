Ethereum brain wallets are formed through applying the SHA3 to a seed to get a result `R`, then using `R` as an accumulator for 16384 repeat SHA3 operations. This process is continued until the result, when used as a private key, forms a valid Direct ICAP (34 digit) address, defined as the first byte of the address being 0.

See [C++ implementation](https://github.com/ethereum/cpp-ethereum/blob/develop/libethcore/KeyManager.cpp#L215-L225) for an example.

```
FUNCTION toBrain(STRING seed) RETURNS SECRET
	A = SHA3(seed)
	REPEAT 16384 TIMES
		A = SHA3(A)
	END REPEAT
	WHILE SECRET_TO_ADDRESS(A)[0] != 0 DO
		A = SHA3(A)
	END WHILE
	RETURN A
END FUNCTION
```