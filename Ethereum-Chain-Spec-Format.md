**Please note: this document is a suggested standard and has not yet been accepted.**

This is a format to describe any Ethereum-like chain. It is derived from the `genesis.json` format but includes parameters to change and configure the consensus algorithm, to specify infrastructure information, to specify boot nodes and to specify any built-in contracts together with their cost.

### Format

It is JSON, with the top level being an object with six keys:

- `name`: A string value specifying the chain name. e.g. "Frontier/Homestead", "Morden", "Olympic".
- `forkName`: An optional string value specifying a sub-identifier, in case two different chains have equivalent genesis blocks.
- `engine`: A enum value specifying the consensus engine. e.g. "Ethash", "Null".
- `params`: An object specifying various attributes of the consensus engine, allowing configuration.
- `genesis`: An object specifying the header of the genesis block.
- `nodes`: An array of strings, each one a node address in enode format.
- `accounts`: An object specifying accounts of the genesis block. This includes builtin contracts and premines.

#### Subformat: engine

There are two valid engines, `Ethash` and `Null`.

- `Null`: Nonoperative engine.
- `Ethash`: Sealing engine used by ethereum.
  - `params`: Engine specific params.
    - `minimumDifficulty`: Integer specifying the minimum difficulty a block may have.
    - `gasLimitBoundDivisor`: Integer specifying the according value in the Yellow Paper.
    - `difficultyBoundDivisor`: Integer specifying the according value in the Yellow Paper.
    - `durationLimit`: Integer specifying the boundary point at which difficulty is increased.
    - `blockReward`: Integer specifying the reward given for authoring a block.
    - `registrar`: `0x`-prefixed, 40-nibble datum of the address of the registrar contract on this chain.

#### Subformat: params

Different consensus engines may allow different keys in the `params` object, however there exist a few common to all:

- `accountStartNonce`: Integer specifying what nonce all newly created accounts should have.
- `frontierCompatibilityModeLimit`: Integer specifying the number of the block that Frontier-compatibility mode finishes and Homestead mode begins.
- `maximumExtraDataSize`: Integer specifying the maximum size in bytes of the extra_data field of the header.
- `minGasLimit`: Integer specifying the minimum amount of gas a block may be limited at.
- `networkID`: Integer specifying the index of this chain on the network.

Note: all integers are `0x`-prefixed, hex-encoded strings.

#### Subformat: genesis

The keys in genesis specify the according fields in the chain's genesis block. All are hex-encoded, `0x` prefixed. The fields required are:

- `seal`
- `difficulty`
- `author`
- `timestamp`
- `parentHash`
- `extraData`
- `gasLimit`

#### Subformat: accounts

The `accounts` object maps from addresses (40-nibble strings without the `0x` prefix) to objects, each with a number of allowed keys:

- `balance`: Integer to specify the balance of the account at genesis in wei.
- `nonce`: Integer to specify the nonce of the account at genesis.
- `code`: `0x`-prefixed hex specifying the code of the account at genesis.
- `storage`: Object mapping hex-encoded integers for the account's storage at genesis.
- `builtin`: Alternative to `code`, used to specify that the account's code is natively implemented. Value is an object with further fields:
  - "name": The name of the builtin code to execute as a string. e.g. `"identity"`, `"ecrecover"`.
  - "pricing": Enum to specify the cost of calling this contract.
    - "linear": Specify a linear cost to calling this contract. Value is an object with two fields: `base` which is the basic cost in Wei and is always paid; and `word` which is the cost per word of input, rounded up.

### Example

This is the Morden ECS JSON file

```json
{
	"name": "Morden",
	"engine": {
		"Ethash": {
			"params": {
				"tieBreakingGas": false,
				"gasLimitBoundDivisor": "0x0400",
				"minimumDifficulty": "0x020000",
				"difficultyBoundDivisor": "0x0800",
				"durationLimit": "0x0d",
				"blockReward": "0x4563918244F40000",
				"registrar" : "0xc6d9d2cd449a754c494264e1809c50e34d64562b"
			}
		}
	},
	"params": {
		"accountStartNonce": "0x0100000",
		"frontierCompatibilityModeLimit": "0x789b0",
		"maximumExtraDataSize": "0x20",
		"tieBreakingGas": false,
		"minGasLimit": "0x1388",
		"networkID" : "0x2"
	},
	"genesis": {
		"seal": {
			"ethereum": {
				"nonce": "0x0000000000000042",
				"mixHash": "0x0000000000000000000000000000000000000000000000000000000000000000"
			}
		},
		"difficulty": "0x20000",
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
		"0000000000000000000000000000000000000001": { "balance": "1", "nonce": "1048576", "builtin": { "name": "ecrecover", "pricing": { "linear": { "base": 3000, "word": 0 } } } },
		"0000000000000000000000000000000000000002": { "balance": "1", "nonce": "1048576", "builtin": { "name": "sha256", "pricing": { "linear": { "base": 60, "word": 12 } } } },
		"0000000000000000000000000000000000000003": { "balance": "1", "nonce": "1048576", "builtin": { "name": "ripemd160", "pricing": { "linear": { "base": 600, "word": 120 } } } },
		"0000000000000000000000000000000000000004": { "balance": "1", "nonce": "1048576", "builtin": { "name": "identity", "pricing": { "linear": { "base": 15, "word": 3 } } } },
		"102e61f5d8f9bc71d0ad4a084df4e65e05ce0e1c": { "balance": "1606938044258990275541962092341162602522202993782792835301376", "nonce": "1048576" }
	}
}
```
Note: the builtin accounts enable usage of Solidity language.  Running without them included in the chain definition file may result in unexpected behavior.  