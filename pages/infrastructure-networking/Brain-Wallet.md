---
name: Brain Wallet
category: 
---

Ethereum brain wallets are formed through applying the SHA3 to a seed to get a result `R`, then using `R` as an accumulator for 16384 repeat SHA3 operations. This process is continued until the result, when used as a private key, forms a valid Direct ICAP (34 digit) address, defined as the first byte of the address being 0.

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

See [C++ implementation](https://github.com/ethereum/cpp-ethereum/blob/develop/libethcore/KeyManager.cpp#L215-L225) for an example.


### **Comments (Gustav):**

Recent advancement in brain wallet cracking [1] show how vulnerable brain wallets are to weak passwords. Applying a KDF hardens brain wallets by reducing number of passwords an attacker can generate per second.

Even though the seed in AZ is generated for people and can thus be designed to have enough entropy, it is desirable to configure a KDF to be as strong as possible without impacting usability.

Benchmarking SHA3 in Go [2] on a i5-4278U CPU @ 2.60GHz gives 16384 hashes in 30ms. A C implementation on a high-end CPU would be significantly faster.

I would recommend we configure a KDF that is much harder (even up to 1-2s CPU time) and also has a memory cost. Scrypt comes to mind, which we already specify in for the key storage [3].

This would make our brainwallets much harder to crack, and perhaps even allowing the seed to be shorter, improving usability.

1. https://rya.nc/cracking_cryptocurrency_brainwallets.pdf
2. https://github.com/ethereum/go-ethereum/blob/master/crypto/crypto_test.go#L65
3. https://github.com/ethereum/wiki/wiki/Web3-Secret-Storage-Definition