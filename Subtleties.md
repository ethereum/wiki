* Storage is a key/value store where keys and values are both 32 bytes
* Values on the stack are 32 bytes
* Memory is a byte-array. Memory starts off zero-size, but can be expanded in 32-byte chunks by simply accessing or storing memory at indices greater than its current size. There is a fee of 1 gas per 32 bytes for expanding memory.
* All arithmetic is modulo 2<sup>256</sup>. For division, modulo and comparison, both signed and unsigned operators exist (eg. `(0 - 27) / 3` returns -9 if `SDIV` is used, but `38597363079105398474523661669562635951089994888546854679819194669304376546636` if `DIV` is used.
* Truncation and modulo operations with negative operators in the `SDIV`/`SMOD` case are handled as in Python (eg. , )
* `DIV`, `SDIV`, `MOD` and `SMOD` with dividend (second argument) equal to 0 push 0 to the stack.
* If an operation tries to take more values off the stack than are on the stack, that halts execution
* The `CREATE` opcode takes three values: endowment (ie. initial amount of ether), memory start and memory length, and pushes onto the stack the address of the new contract. `CREATE` gives the initializing sub-execution all the gas that you have (and if gas remains then it gets refunded back to the parent execution)
* The `CALL` opcode takes seven values: gas, recipient, ether value, memory location of start of input data, length of input data, memory location to put start of output data, length of output data. It puts onto the stack either 1 for success (ie. did not run out of gas) or 0 for failure.
* When a contract calls `SUICIDE`, its ether is immediately sent to the desired address, but the contract continues existing until the end of transaction execution. Note that this leads to the interesting effect that, unlike Bitcoin where funds can be locked away forever but never destroyed, if a contract either SUICIDEs into itself or receives ether in the context of the same transaction execution after it has SUICIDED that ether is actually destroyed.
* If contract A calls contract B calls contract A, then the inner execution of A will have its own, fresh, memory, stack and PC, but it will modify and read the same balance and storage.
* If contract initialization returns an empty array, then no contract will be created. This allows you to "abuse" contract initialization as an atomic multi-operation, which might be useful in some protocols where you want to do multiple things but you don't want some of them to be able to process without others.
* If a transaction runs out of gas, then:
    * The value transfer from sender to recipient still takes place
    * The fee transfer from sender to miner still takes place
    * The entire `STARTGAS` is added to `gas_used`
    * All other execution is reverted
* If a contract-producing transaction runs out of gas, then:
    * The fee transfer from sender to miner still takes place
    * The endowment is still subtracted from the sender's account, but is destroyed and not sent to anywhere
    * The entire `STARTGAS` is added to `gas_used`
    * All other execution is reverted
* If a `CALL` operation's sub-execution runs out of gas, then:
    * Ether value is still sent
    * Entire gas is consumed
    * `0` is appended onto the stack
    * All other execution is reverted
* If a `CREATE` operation's sub-execution runs out of gas, then:
    * Ether value is lost
    * All gas is consumed
    * All other execution is reverted
    * The current implementations add `0` onto the stack, but it does not matter, since with 0 gas remaining the parent execution will instaquit anyway
* When an externally-owned account creates a new contract, the pre-increment nonce is used to determine the contract address.
* When a contract creates a new contract, the pre-increment nonce is used to determine the contract address, and the nonce is incremented before execution begins.