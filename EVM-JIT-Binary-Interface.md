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
ReturnCode   | i32  | out    | 
