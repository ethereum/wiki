A "brain wallet" is the name given to the combination of a seed phrase and a means of deriving a secret key from that phrase alone, without reference to e.g. an additional file. Brain wallets have two main usages; firstly for using a simple password/passphrase to define and protect their account. Secondly for backing up the secret key of an account by using a mnemonic string.

In the first case, the security of the secret key, and therefore the account and identity it protects, is fairly small since the typical [entropy](https://xkcd.com/936/) of a password tends to be far less than 160-bit, the entropy of an address. Recent advancement in brain wallet cracking [1] show how vulnerable brain wallets are to weak passwords. This use-case is strongly discouraged. Applying a specialised KDF hardens brain wallets by reducing number of passwords an attacker can generate per second.

In the second case, the security of the secret key is already provided through the entropy present in the phrase, and as such the KDF can be relatively simple.

# Standard Method

Ethereum brain wallets are formed through applying a predefined KDF to a seed to get a result `R`, then using `R` as an accumulator for 16384 repeat KDF operations. This process is continued until the result, when used as a private key, forms a valid Direct ICAP (34 digit) address, defined as the first byte of the address being 0.

```
FUNCTION toBrain(STRING seed) RETURNS SECRET
	A = KDF(seed)
	REPEAT 16384 TIMES
		A = KDF(A)
	END REPEAT
	WHILE SECRET_TO_ADDRESS(A)[0] != 0 DO
		A = KDF(A)
	END WHILE
	RETURN A
END FUNCTION
```

There are two proposed variants of this method; one is the original method, already implemented in the tools of cpp-ethereum and Parity, which features only basic safeguards against securing low-entropy seed phrases. The second is the enhanced method, which features a specialised _memory-hard_ KDF.

## Original KDF

In this variant, the Standard Method is used with a KDF of the Keccak-256 operation.

Benchmarking SHA3 in Go [2] on a i5-4278U CPU @ 2.60GHz gives 16384 hashes in 30ms. A C implementation on a high-end CPU would be significantly faster.


## Enhanced KDF

In this variant, the Standard Method is used with a KDF of the Scrypt operation.

---

### **Comments (Gustav):**

Even though the seed in AZ is generated for people and can thus be designed to have enough entropy, it is desirable to configure a KDF to be as strong as possible without impacting usability.

I would recommend we configure a KDF that is much harder (even up to 1-2s CPU time) and also has a memory cost. Scrypt comes to mind, which we already specify in for the key storage [3].

This would make our brainwallets much harder to crack, and perhaps even allowing the seed to be shorter, improving usability.

1. https://rya.nc/cracking_cryptocurrency_brainwallets.pdf
2. https://github.com/ethereum/go-ethereum/blob/master/crypto/crypto_test.go#L65
3. https://github.com/ethereum/wiki/wiki/Web3-Secret-Storage-Definition