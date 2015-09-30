---
name: Bad Block Reporting
category: 
---

Send a JSONRPC request to `https://badblocks.ethdev.com`:

Call `eth_badBlock(BADBLOCK)`, with `BADBLOCK` the object described below:

## BADBLOCK Object Format

NOTE: All hex is lower-case.

#### Types
- `DATA`: freeform byte array as a string of hex, *no 0x prefix*.
- `DATA_20`: like DATA, always of length 40.
- `HEX`: string of hex-encoded big-endian integer, used for VM stack/memory/storage items, *no 0x prefix*, *no leading zeroes*.
- `INT`: simple JS integer.
- `BIGINT`: string of decimal-encoded integer, used for potentially bigints.
- `TAG_ERROR`: described later.
- `TAG_INST`: string of an EVM instruction mnemonic, uppercase e.g. `"PUSH4"` or `"STOP"`.
- `VMTRACE`: described later.

#### Type modifiers
- `SOMETIMES`: field is omitted under certain circumstances.
- `NONSTANDARD`: field may safely be omitted.

#### BADBLOCKS object

```json
{
	"block": DATA
	"errortype": TAG_ERROR
	"hints": { (all items OPTIONAL)
		"receipts": [ DATA, ... ], OPTIONAL
		"vmtrace": VMTRACE, OPTIONAL
	}, OPTIONAL
}
```

Where:

`receipts` is simply the array of RLP-encoded receipts.

`TAG_ERROR` is a string containing one of (specified roughly in order of ability to detect):

#### Generic:
- `RLPError`: One of the following:
  - `BadRLP`: Generally invalid RLP (e.g. 0x8100).
  - `BadCast`: Given RLP is of an incorrect type (e.g. 0x00 being interpreted as an integer).
  - `OversizeRLP`: Additional bytes trailing an otherwise valid RLP fragment (e.g. 0x8000).
  - `UndersizeRLP`: Bytes missing from the end of an otherwise valid RLP fragment (e.g. 0x81).

#### Block-specific:
- `InvalidBlock`: One of the following:
  - `InvalidBlockFormat`: Block format is wrong (!= array, != 3 items &c.).
  - `TooManyUncles`: More than 2 uncles mentioned.
  - `InvalidTransactionsRoot`: Transactions root is different to that derived from transactions given in block.
  - `InvalidUnclesHash`: Uncles hash is different to that derived from uncles given in block.
  - `InvalidGasUsed`: Gas used is not equal to the gasUsed in the last receipt (or previous block if no transactions).
  - `InvalidStateRoot`: State root mentioned is different to that calculated (i.e. reward application is incorrect).
  - `InvalidReceiptsRoot`: Receipts root mentioned is different to that calculated (i.e. appliaction of a transaction resulted in different logs, gas-used or state-root). "receipts" and "vmtrace" should be hinted.

#### Block-header-specific
- `InvalidHeader`: One of the following:
  - `InvalidBlockHeaderItemCount`: Wrong item count in header.
  - `TooMuchGasUsed`: Header states gas used as bigger than gas limit.
  - `ExtraDataTooBig`: Header's extra data is greater than limit.
  - `InvalidDifficulty`: Difficulty is incorrect given previous block's difficulty and timestamp.
  - `InvalidGasLimit`: Gas limit does not fall within bounds given previous block's gas limit.
  - `InvalidBlockNonce`: Nonce does not result in a proof of work which satisfies the given difficulty.
  - `InvalidNumber`: Number is not equal to parent number + 1.
  - `InvalidTimestamp`: Timestamp is not greater than parent's.
  - `InvalidLogBloom`: LogBloom is not equal to the bitwise-OR of all receipts' LogBlooms.

#### Transaction-specific
- `InvalidTransaction`: One of the following:
  - `OutOfGasIntrinsic`: GAS below amount required for any transaction.
  - `BlockGasLimitReached`: Too much gas being used for the transaction within this block.
  - `InvalidSignature`: Transaction's signature is invalid.
  - `OutOfGasBase`: GAS below amount required for this transaction.
  - `NotEnoughCash`: Balance of sender too low.
  - `InvalidNonce`: Transaction nonce is wrong.

#### Uncle-specific
- `InvalidUncle`: One of the following:
  - `UncleInChain`: Uncle has already been included in the current chain (either as a direct ancestor or one of its included uncles).
  - `UncleTooOld`: Uncle is older than the 6th generation uncle.
  - `UncleIsBrother`: Uncle is newer than the 1st generation uncle.


and `VMTRACE` is the object:

```json
[
	{
		"stack": [ HEX, ... ]
		"memory": HEX, SOMETIMES
		"sha3memory": DATA_32, SOMETIMES
		"storage": { HEX: HEX }, SOMETIMES
		"gas": BIGINT
		"pc": BIGINT
		"inst": INT
		"depth": INT, OPTIONAL
		"steps": INT
		"address": DATA_20, SOMETIMES
		"memexpand": BIGINT, NONSTANDARD
		"gascost": BIGINT, NONSTANDARD
		"instname": STRING, NONSTANDARD
	},
	...
]
```

- `stack`: The stack, prior to execution.
- `memory`: The memory, prior to execution. Omitted when previous operation was not memory-dependent (MLOAD/MSTORE/MSTORE8/SHA3/CALL/CALLCODE/CREATE/CALLDATACOPY/CODECOPY/EXTCODECOPY), not first operation of CALL/CREATE context or when memory >= 1024 bytes large.
- `sha3memory`: The Keccak hash of the memory, prior to execution. Omitted when previous operation was not memory-dependent (MLOAD/MSTORE/MSTORE8/SHA3/CALL/CALLCODE/CREATE/CALLDATACOPY/CODECOPY/EXTCODECOPY), not first operation of CALL/CREATE context or when memory < 1024 bytes large.
- `storage`: The contents of storage that SSTOREs operate on (RE-READ THAT!), prior to execution. Omitted when previous operation is not storage-dependent (SLOAD/SSTORE) and not first operation of CALL/CREATE context.
- `gas`: The amount of gas available prior to this instruction.
- `pc`: The program counter, immediately prior to execution.
- `inst`: The instruction opcode index that is to be executed (e.g. STOP would be 0).
- `depth`: The depth of in present context in CALL/CREATE stack. Omitted when no change since previous operation and not first operation of CALL/CREATE context.
- `steps`: The number of steps taken so far in present CALL/CREATE context prior to executing the current instruction.
- `address`: The address of account that would be returned by `MYADDRESS` opcode. Omitted when no change since previous operation and not first operation of CALL/CREATE context.
- `memexpand`: The size that memory is to be expanded by in words for this operation. Omitted when zero.
- `gascost`: The total cost of gas for executing this instruction (technically the /maximum/ total cost of gas - CALL/CREATE may return gas).