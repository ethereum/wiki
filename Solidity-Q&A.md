**Q**: Is it possible to return an array of strings ( string[] ) from a solidity function?

**A**: TODO

---
**Q**: If you issue a call for an array, it is possible to retrieve the whole array? Or must you write a helper function for that?

**A**: TODO

---
**Q**: Is it possible for an external Solidity method to return a variable-length type (i.e. bytes, string, non-fixed array)?

**A**: Still unknown!  The [Ethereum contract ABI](https://github.com/ethereum/wiki/wiki/Ethereum-Contract-ABI) specifies encodings for all of these types (but *not* for structs or enums), but it seems impossible for the EVM to support this.  Namely, the opcodes that would support external functions (CALL, CALLCODE, and RETURN) all require the length of the return value to be passed in advance.  An internal function does not have this restriction, however, and can definitely return any kind of data.  Also, there is no such issue with passing these types as arguments.

---
**Q**.  What could have happened if an account has storage value/s but no code?  Example: http://test.ether.camp/account/5f740b3a43fbb99724ce93a879805f4dc89178b5

**A**.  Out of gas during the final step of storing the code: try increasing the gas.

https://github.com/ethereum/wiki/wiki/Subtleties

After a successful CREATE operation's sub-execution, if the operation returns x, 5 * len(x) gas is subtracted from the remaining gas before the contract is created. If the remaining gas is less than 5 * len(x), then no gas is subtracted, the code of the created contract becomes the empty string, but this is not treated as an exceptional condition - no reverts happen.

---
**Q**:  Your question

**A**:  Answer

---
**Q**:  Your question

**A**:  Answer

---
**Q**:  Your question

**A**:  Answer

---
**Q**:  Your question

**A**:  Answer

---
**Q**:  Your question

**A**:  Answer

