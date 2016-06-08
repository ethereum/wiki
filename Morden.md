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
> pyethapp --profile testnet run
```

#### geth (Go client)

```
> geth --testnet
```

#### Parity

```
> parity --chain=morden
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

### Chain Specification (ECS)

```json
{
	"name": "Morden",
	"engineName": "Ethash",
	"params": {
		"accountStartNonce": "0x0100000",
		"frontierCompatibilityModeLimit": "0x789b0",
		"maximumExtraDataSize": "0x20",
		"tieBreakingGas": false,
		"minGasLimit": "0x1388",
		"gasLimitBoundDivisor": "0x0400",
		"minimumDifficulty": "0x020000",
		"difficultyBoundDivisor": "0x0800",
		"durationLimit": "0x0d",
		"blockReward": "0x4563918244F40000",
		"registrar": "",
		"networkID" : "0x2"
	},
	"genesis": {
		"nonce": "0x00006d6f7264656e",
		"difficulty": "0x20000",
		"mixHash": "0x00000000000000000000000000000000000000647572616c65787365646c6578",
		"author": "0x0000000000000000000000000000000000000000",
		"timestamp": "0x00",
		"parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
		"extraData": "0x",
		"gasLimit": "0x2fefd8"
	},
	"nodes": [
		"enode://b1217cbaa440e35ed471157123fe468e19e8b5ad5bedb4b1fdbcbdab6fb2f5ed3e95dd9c24a22a79fdb2352204cea207df27d92bfd21bfd41545e8b16f637499@104.44.138.37:30303"
	],
	"accounts": {
		"0000000000000000000000000000000000000001": { "balance": "1", "nonce": "1048576", "builtin": { "name": "ecrecover", "linear": { "base": 3000, "word": 0 } } },
		"0000000000000000000000000000000000000002": { "balance": "1", "nonce": "1048576", "builtin": { "name": "sha256", "linear": { "base": 60, "word": 12 } } },
		"0000000000000000000000000000000000000003": { "balance": "1", "nonce": "1048576", "builtin": { "name": "ripemd160", "linear": { "base": 600, "word": 120 } } },
		"0000000000000000000000000000000000000004": { "balance": "1", "nonce": "1048576", "builtin": { "name": "identity", "linear": { "base": 15, "word": 3 } } },
		"102e61f5d8f9bc71d0ad4a084df4e65e05ce0e1c": { "balance": "1606938044258990275541962092341162602522202993782792835301376", "nonce": "1048576" }
	}
}
```

### Getting Ether
One way to get Ether is by using the [Ethereum wei faucet](http://faucet.ma.cx:3000/). Just type in your account address and enjoy some free ether.