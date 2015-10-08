Morden is the first Ethereum alternative testnet. It is expected to continue throughout the Frontier and Homestead era.

### Usage

#### TurboEthereum (C++)

This is supported natively on 0.9.93 and above. Pass the `--morden` argument in when starting any of the clients. e.g.:

```
> eth --morden
```

Or, for AlethZero

```
> alethzero --morden
```

#### PyEthApp (Python client)

PyEthApp supports the morden network from v1.0.5 onwards:

```
> pyethapp --profile morden run
```

### Details

- Network Identity: **2**
- All parameters same as Frontier except:
  - genesis.json (given below);
  - Initial Account Nonce (`IAN`) is 2^20 (instead of 0 in all previous networks).
    - All accounts in the state trie have nonce >= `IAN`.
    - Whenever an account is inserted into the state trie it is initialised with nonce = `IAN`.
- Genesis block hash: `0cd786a2425d16f152c658316c423e6ce1181e15c3295826d7c9904cba9ce303`
- Genesis state root: `f3f4696bbf3b3b07775128eb7a3763279a394e382130f27c21e70233e04946a9`

### Seed Nodes
- `enode://e58d5e26b3b630496ec640f2530f3e7fa8a8c7dfe79d9e9c4aac80e3730132b869c852d3125204ab35bb1b1951f6f2d40996c1034fd8c5a69b383ee337f02ddc@92.51.165.126:30303`

### genesis.json

```json
{
        "nonce": "0x00006d6f7264656e",
        "difficulty": "0x20000",
        "mixhash": "0x00000000000000000000000000000000000000647572616c65787365646c6578",
        "coinbase": "0x0000000000000000000000000000000000000000",
        "timestamp": "0x00",
        "parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
        "extraData": "0x",
        "gasLimit": "0x2FEFD8",
        "alloc": {
                "0000000000000000000000000000000000000001": { "balance": "1" },
                "0000000000000000000000000000000000000002": { "balance": "1" },
                "0000000000000000000000000000000000000003": { "balance": "1" },
                "0000000000000000000000000000000000000004": { "balance": "1" },
                "102e61f5d8f9bc71d0ad4a084df4e65e05ce0e1c": { "balance": "1606938044258990275541962092341162602522202993782792835301376" }
        }
}
```