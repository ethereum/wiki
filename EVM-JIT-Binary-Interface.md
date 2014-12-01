# Runtime Data

A set of data possibly needed by running contract, sometimes by compiler itself. 
Can be called ExtVM data in C++, VMEnv data in Go.

Name    | Type | Access | Description
-----   |------|--------|------------
Gas     | i256 | rw     | Gas counter.
Address | h256 | r      | Account address.