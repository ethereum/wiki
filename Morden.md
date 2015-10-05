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

For earlier versions, `eth` can still be made to work by starting with arguments `--network-id 2 --genesis-json morden.json`, making sure to make a file `morden.json` containing the **Genesis JSON** contents given below.

### Details

- Network Identity: **2**
- All parameters except genesis are same as Frontier
- Genesis block hash: `4957c918...`
- Genesis state root: `74f349dc...`

### Seed Nodes
- `enode://5374c1bff8df923d3706357eeb4983cd29a63be40a269aaa2296ee5f3b2119a8978c0ed68b8f6fc84aad0df18790417daadf91a4bfbb786a16c9b0a199fa254a@92.51.165.126:30303`

### Genesis.json

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
                "0000000000000000000000000000000000000001": { "wei": "1" },
                "0000000000000000000000000000000000000002": { "wei": "1" },
                "0000000000000000000000000000000000000003": { "wei": "1" },
                "0000000000000000000000000000000000000004": { "wei": "1" },
                "5ed8cee6b63b1c6afce3ad7c92f4fd7e1b8fad9f": { "wei": "1606938044258990275541962092341162602522202993782792835301376" },
                "0037a6b811ffeb6e072da21179d11b1406371c63": { "wei": "1606938044258990275541962092341162602522202993782792835301376" },
                "02e816afc1b5c0f39852131959d946eb3b07b5ad": { "wei": "1606938044258990275541962092341162602522202993782792835301376" },
                "775e18be7a50a0abb8a4e82b1bd697d79f31fe04": { "wei": "1606938044258990275541962092341162602522202993782792835301376" },
                "063dd253c8da4ea9b12105781c9611b8297f5d14": { "wei": "1606938044258990275541962092341162602522202993782792835301376" },
                "c5a96db085dda36ffbe390f455315d30d6d3dc52": { "wei": "1606938044258990275541962092341162602522202993782792835301376" },
                "c2c2c26961e5560081003bb157549916b21744db": { "wei": "1606938044258990275541962092341162602522202993782792835301376" }
        }
}
```