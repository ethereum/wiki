---
name: Subtleties
category: 
---

## Memory

* Storage is a key/value store where keys and values are both 32 bytes
* Values on the stack are 32 bytes
* Memory is a byte-array. Memory starts off zero-size, but can be expanded in 32-byte chunks by simply accessing or storing memory at indices greater than its current size.
* The fee for expanding memory is determined via a subtract-the-integrals method. Specifically, `TOTALFEE(SZ) = SZ * 3 + floor(SZ**2 / 512)` is the total fee for expanding the memory to `SZ` 32-byte chunks (note: partially filled chunks are counted, so 33 bytes = 2 chunks), and if a particular operation expands memory from size `x` to `y`, the additional gas cost is `TOTALFEE(y) - TOTALFEE(x)`
* If an operation writes a slice of data zero bytes wide to memory, even if the start index of the slice exceeds the current memory size, memory is NOT expanded.

## Nonces

* If an externally-owned account sends a transaction, its nonce is incremented before execution
* If an externally-owned account creates a contract, its nonce is incremented before execution
* If a contract sends a message, no nonce increments happen
* If a contract creates a contract, the nonce is incremented before the rest of the sub-execution
* The pre-increment nonce is used to determine the contract address
* Nonce increments are never reverted

## Exceptional conditions

* The following count as exceptions:
    * Execution running out of gas
    * An operation trying to take more slots off the stack than are available on the stack
    * Jumping to a bad jump destination
    * An invalid opcode (note: the code of an account is assumed to be followed by an infinite tail of STOP instructions, so the program counter "walking off" the end of the code is not an invalid opcode exception. However, jumping outside the code is an exception, because STOP is not a valid jump destination)
* If a transaction triggers an exception, then:
    * The value transfer from sender to recipient still takes place
    * The fee transfer from sender to miner still takes place
    * The entire `STARTGAS` is added to `gas_used`
    * All other execution is reverted
* If a contract-producing transaction triggers an exception, then:
    * The fee transfer from sender to miner still takes place
    * The endowment is subtracted from the sender's account
    * The account that would have been created gets created anyway, keeps its original endowment, but has the empty string as its code
    * The entire `STARTGAS` is added to `gas_used`
    * All other execution is reverted
* If a `CALL` operation's sub-execution triggers an exception, then:
    * Ether value is still sent
    * All gas is consumed
    * `0` is appended onto the stack
    * All other execution is reverted
* If a `CREATE` operation's sub-execution triggers an exception, then:
    * Ether value is lost
    * All gas is consumed
    * All other execution is reverted
    * The current implementations add `0` onto the stack, but it does not matter, since with 0 gas remaining the parent execution will instaquit anyway
* After a successful `CREATE` operation's sub-execution, if the operation returns `x`, `5 * len(x)` gas is subtracted from the remaining gas before the contract is created. If the remaining gas is less than `5 * len(x)`, then no gas is subtracted, the code of the created contract becomes the empty string, but this is not treated as an exceptional condition - no reverts happen.
* If a contract tries to `CALL` or `CREATE` a contract with either (i) insufficient balance, or (ii) stack depth already at maximum (1024), the sub-execution and transfer do not occur at all, no gas gets consumed, and 0 is added to the stack.
* Because of the depth limit, a contract may not be aware that a call that it is about to make is going to fail. Contract programmers should be very careful about this, and either use the 0 pushed to the stack to catch errors or use, eg, the identity contract to "test the waters" before making a substantial number of calls.

## Arithmetic

* All arithmetic is modulo 2<sup>256</sup>. For division, modulo and comparison, both signed and unsigned operators exist (eg. `(0 - 27) / 3` returns -9 if `SDIV` is used, but `38597363079105398474523661669562635951089994888546854679819194669304376546636` if `DIV` is used.
* Truncation and modulo operations with negative operators in the `SDIV`/`SMOD` case are handled as in Python (eg. , )
* `DIV`, `SDIV`, `MOD` and `SMOD` with dividend (second argument) equal to 0 push 0 to the stack.

### Other operations

* The `CREATE` opcode takes three values: endowment (ie. initial amount of ether), memory start and memory length, and pushes onto the stack the address of the new contract. `CREATE` gives the initializing sub-execution all the gas that you have (and if gas remains then it gets refunded back to the parent execution)
* The `CALL` opcode takes seven values: gas, recipient, ether value, memory location of start of input data, length of input data, memory location to put start of output data, length of output data. It puts onto the stack either 1 for success (ie. did not run out of gas) or 0 for failure.
* When a contract calls `SUICIDE`, its ether is immediately sent to the desired address, but the contract continues existing until the end of transaction execution. Note that this leads to the interesting effect that, unlike Bitcoin where funds can be locked away forever but never destroyed, if a contract either SUICIDEs into itself or receives ether in the context of the same transaction execution after it has SUICIDED that ether is actually destroyed.
* If contract A calls contract B calls contract A, then the inner execution of A will have its own, fresh, memory, stack and PC, but it will modify and read the same balance and storage.
* If contract initialization returns an empty array, then no contract will be created. This allows you to "abuse" contract initialization as an atomic multi-operation, which might be useful in some protocols where you want to do multiple things but you don't want some of them to be able to process without others.
* `JUMP` and `JUMPI` instructions are only allowed to jump onto destinations that are (1) occupied by a `JUMPDEST` opcode, and (2) are not inside `PUSH` data. Note that properly processing these conditions requires preprocessing the code; a particularly pathological use case is `PUSH2 JUMPDEST PUSH1 PUSH2 JUMPDEST PUSH1 PUSH2 JUMPDEST PUSH1 ...`, as this code has all `JUMPDEST`s invalid but an alternative piece of code equivalent to this but only with the leading `PUSH2` replaced with another op (eg. `BALANCE`) will have all `JUMPDESTS`s valid.
* `CALL` has a multi-part gas cost:
    * 40 base
    * 9000 additional if the value is nonzero
    * 25000 additional if the destination account does not yet exist (note: there is a difference between zero-balance and nonexistent!)
* `CALLCODE` operates similarly to call, except without the potential for a 25000 gas surcharge.
* The child message of a nonzero-value `CALL` operation (NOT the top-level message arising from a transaction!) gains an additional 2300 gas on top of the gas supplied by the calling account; this stipend can be considered to be paid out of the 9000 mandatory additional fee for nonzero-value calls. This ensures that a call recipient will always have enough gas to log that it received funds.