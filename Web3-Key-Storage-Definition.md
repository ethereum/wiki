This proposes version 2 of the Web3 Key Storage Definition. It fixes several inconsistencies with the version 1 published [here](https://github.com/ethereum/go-ethereum/wiki/Passphrase-protected-key-store-spec). In brief these are:

- Capitalisation is unjustified and inconsistent (`scrypt` lowercase, `Kdf` mixed-case, `MAC` uppercase).
- `Address` unnecessary and compromises privacy.
- `Salt` is intrinsically a parameter of the key derivation function and deserves to be associated with it, not with the crypto in general.
- `SaltLen` unnecessary (just derive it from `Salt`).
- The key derivation function is given, yet the crypto algorithm is hard specified. 
- `Version` is intrinsically numeric yet is a string (structured versioning would be possible with a string, but can be considered out of scope for a rarely changing configuration file format).
- KDF and cipher are notionally sibling concepts yet are organised differently.

Changes have been made to the format to give the following file, functionally equivalent to the example given on the previously linked page:

```json
{
    "crypto": {
        "cipher": "aes-128-cbc",
        "ciphertext": "07533e172414bfa50e99dba4a0ce603f654ebfa1ff46277c3e0c577fdc87f6bb4e4fe16c5a94ce6ce14cfa069821ef9b",
        "cipherparams": {
            "iv": "16d67ba0ce5a339ff2f07951253e6ba8"
        },
        "kdf": "scrypt",
        "kdfparams": {
            "dklen": 32,
            "n": 262144,
            "p": 1,
            "r": 8,
            "salt": "06870e5e6a24e183a5c807bd1c43afd86d573f7db303ff4853d135cd0fd3fe91"
        },
        "mac": "8ccded24da2e99a11d48cda146f9cc8213eb423e2ea0d8427f41c3be414424dd",
        "version": 1
    },
    "id": "0498f19a-59db-4d54-ac95-33901b4f1870",
    "version": 2
}
```

The actual encoding and decoding of the file remains largely unchanged, except that the crypto algorithm is no longer fixed to AES-128-CBC.

Another example, using the PBKDF algorithm for key derivation is:


```json
{
    "crypto": {
        "cipher": "aes-128-cbc",
        "ciphertext": "07533e172414bfa50e99dba4a0ce603f654ebfa1ff46277c3e0c577fdc87f6bb4e4fe16c5a94ce6ce14cfa069821ef9b",
        "cipherparams": {
            "iv": "16d67ba0ce5a339ff2f07951253e6ba8"
        },
        "kdf": "pbkdf2",
        "kdfparams": {
            "prf": "hmac-sha256",
            "dklen": 32,
            "c": 262144,
            "salt": "06870e5e6a24e183a5c807bd1c43afd86d573f7db303ff4853d135cd0fd3fe91"
        },
        "mac": "8ccded24da2e99a11d48cda146f9cc8213eb423e2ea0d8427f41c3be414424dd",
        "version": 1
    },
    "id": "0498f19a-59db-4d54-ac95-33901b4f1870",
    "version": 2
}
```

Most of the meanings/algorithm are similar to the original spec, except `mac`, which is given as the SHA3 of the concatenation of the last 16 bytes of the derived key together with the full `ciphertext`.

## Test Vector

Test vector using AES-128-CBC and PBKDF2-SHA-256:

File contents of `~/.web3/keys/3198bc9c-6672-5ab3-d9954942343ae5b6.json`:
```json
{
    "crypto" : {
        "cipher" : "aes-128-cbc",
        "cipherparams" : {
            "iv" : "db76cba2e79171364c5d5378190062c8"
        },
        "ciphertext" : "e8ae879315e349d8804a779421b26d95d2d3195fcf6c4039ee5323b858f53ef3",
        "kdf" : "pbkdf2",
        "kdfparams" : {
            "c" : 262144,
            "dklen" : 16,
            "prf" : "hmac-sha256",
            "salt" : "de5742f1f1045c402296422cee5a8a9ecf0ac5bf594deca1170d22aef33a79cf"
        },
        "mac" : "340aed34a9229210bfb7a5731268d78c68dba5e78a5a4eb87ff6e28bf2952735"
    },
    "id" : "3198bc9c-6672-5ab3-d995-4942343ae5b6",
    "version" : 2
}
```

Details:

- Address: `008aeeda4d805471df9b2a5b0f38a0c3bcba786b`
- ICAP: `XE542A5PZHH8PYIZUBEJEO0MFWRAPPIL67`
- UUID: `3198bc9c-6672-5ab3-d9954942343ae5b6`
- Password: `testpassword`
- Secret: `7a28b5ba57c53603b0b07b56bba752f7784bf506fa95edc395f5cf6c7514fe9d`

