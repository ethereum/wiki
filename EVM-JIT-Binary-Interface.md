---
name: EVM JIT Binary Interface
category: 
---

# JIT C Interface

``` C
#include <stdint.h>

// JIT object opaque type
typedef struct evm_jit evm_jit;

// Contract execution return code
typedef int evm_jit_return_code;

// Host-endian 256-bit integer type
typedef struct i256 i256;

// Big-endian right aligned 256-bit hash
typedef struct h256 h256;

// Runtime data struct - must be provided by external language (Go, C++, Python)
typedef struct evm_jit_rt evm_jit_rt;

// Runtime callback functions  - implementations must be provided by external language (Go, C++, Python)
void evm_jit_rt_sload(evm_jit_rt* _rt, i256* _index, i256* _ret);
void evm_jit_rt_sstore(evm_jit_rt* _rt, i256* _index, i256* _value);
void evm_jit_rt_balance(evm_jit_rt* _rt, h256* _address, i256* _ret);
// And so on...

evm_jit* evm_jit_create(evm_jit_rt* _runtime_data);

evm_jit_return_code evm_jit_execute(evm_jit* _jit);

void evm_jit_get_return_data(evm_jit* _jit, char* _return_data_offset, size_t* _return_data_size);

void evm_jit_destroy(evm_jit* _jit);
```

# Return Code

Informs user about contract exit status:

Code| Exit Status
----|-----
0   | Stop
1   | Return
2   | Suicide
101 | Bad Jump Destination
102 | Out Of Gas
103 | Stack Underflow
104 | Bad Instruction



# JIT Runtime Data

A set of data possibly needed by running contract, sometimes by the compiler itself. 
Can be called ExtVM data in C++, VMEnv data in Go.

Name         | Type | Access | Description
-----        |------|--------|------------
Gas          | i256 | inout  | Gas counter.
Address      | h256 | in     | Account address.
Caller       | h256 | in     | 
Origin       | h256 | in     | 
CallValue    | i256 | in     | 
CallDataSize | i256 | in     | 
GasPrice     | i256 | in     | 
PrevHash     | i256 | in     | 
CoinBase     | h256 | in     | 
TimeStamp    | i256 | in     | 
Number       | i256 | in     | 
Difficulty   | i256 | in     | 
GasLimit     | i256 | in     | 
CodeSize     | i256 | const  | 
CallData     | byte*| in     |
Code         | byte*| const  |
