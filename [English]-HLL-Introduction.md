### High-Level Language

In order to make contract script programming easier, we have also added a high-level language in which users can write contracts. The HLL includes the following basic features:

* Arithmetic in standard BEDMAS infix notation (eg. `(3 + 4) * 5 + 6 = 41`)
* Variables
* Pesudo-variables for message, transaction and block data
* Arrays of 32-byte values
* Storage as a "pseudo-array" (ie. an object that looks like an array but actually uses SSTORE and SLOAD in the backend)
* `mkcall`, `create` and `return` as pseudo-functions, and `stop` as a pseudo-statement
* If/else statements
* While loops
* Python-like syntax

We have provided a compiler to convert from HLL to an abstract-syntax-tree (AST) representation of HLL to finally the underlying EVM-code. Here is an example of what HLL looks like aesthetically:

    if !contract.storage[1000]:
        contract.storage[0x1031c7fdfca1df11b05840557be68ae3c8ceb7cb] = 10^18
        contract.storage[1000] = 1
    else:
        from = msg.sender
        to = msg.data[0]
        value = msg.data[1]
        if to <= 1000:
            stop
        if contract.storage[from] < value:
            stop
        contract.storage[from] = contract.storage[from] - value
        contract.storage[to] = contract.storage[to] + value

## Code examples

## Sub-currency

Full code (13 lines):

    if !contract.storage[1000]:
        contract.storage[0x1031c7fdfca1df11b05840557be68ae3c8ceb7cb] = 10^18
        contract.storage[1000] = 1
    else:
        from = msg.sender
        to = msg.data[0]
        value = msg.data[1]
        if to <= 1000:
            stop
        if contract.storage[from] < value:
            stop
        contract.storage[from] = contract.storage[from] - value
        contract.storage[to] = contract.storage[to] + value

This is a sub-currency on top of the Ethereum platform; the purpose of the contract is to take in message call with 64 bytes of data, and treat them as messages with two 32 byte values: `[to, value]`. A `from` value is determined from the `CALLER` opcode (rendered in HLL as `msg.sender`), and then this is taken as a request transferring the desired number of currency units from the sender to the receiver. The contract's purpose is to process the request.

Now, let's look through the code. First, `contract.storage[1000]`, or the value at index 1000 of the contract's internal storage, is used as a flag to determine whether or not the contract has been called yet. If it hasn't, then to start up the currency we put a large number of currency units into a specific address, and we turn on the flag.

    if !contract.storage[1000]:
        contract.storage[0x1031c7fdfca1df11b05840557be68ae3c8ceb7cb] = 10^18
        contract.storage[1000] = 1

Now, we determine the sender, recipient and value desired. The sender is determined from the sender of the message. The recipient is determined from the first element in the `msg.data` array. This is a higher-level abstraction provided by the HLL; in reality the `CALLDATA` that the message sender provides in a message is a byte array, whereas in the HLL it's treated as an array of 32-byte values. The value is determined from the second element in the `msg.data` array.

    else:
        from = msg.sender
        to = msg.data[0]
        value = msg.data[1]

If the storage index is too low, stop, since otherwise we would end up overwriting the contract's code stored in the initial memory indices.

        if to <= 1000:
            stop

If the sender doesn't have enough money to pay, then stop.

        if contract.storage[from] < value:
            stop

And finally, if everything's okay, transfer the given number of currency units from the sender's account to the receiver's account.

        contract.storage[from] = contract.storage[from] - value
        contract.storage[to] = contract.storage[to] + value

Compiling the C-like language is fairly simple as far as compilers go: variables can be assigned a memory index, and compiling an arithmetic expression essentially involves converting it to reverse Polish notation (eg. `(3 + 5) * (x + y)` -> `PUSH 3 PUSH 5 ADD PUSH 0 MLOAD PUSH 1 MLOAD ADD MUL`). Creating higher-level abstractions such as variable-length arrays, queues, hashmaps, functional maps and even Patricia trees is also possible; these will likely be integrated into the HLL by means of creating a "standard library" expressing these concepts as compile-time-substituted macros or second-class functions.

### Proprietary data feed

Full code (6 lines):

    if msg.sender == 0x1031c7fdfca1df11b05840557be68ae3c8ceb7cb:
        contract.storage[msg.data[0]] = msg.data[1]
    else:
        x = array(1)
        x[0] = msg.data[0]
        return(x,1)

Here, we have two clauses. One clause is for the owner of the contract, in this case 0x1031... The owner has the ability to change any value in the contract by sending a transaction with two 32-byte data fields. The first data field represents the index to modify, and the second data field represents the data to store at that index. For example, if the owner chooses to use the code 500 to represent the price of gold in USD, and the owner wants to update the price to 1373, then the owner would make a transaction with data:

    [ 0x00 ]*30 + [ 0x01 0xf4 ] + [ 0x00 ]*30 + [ 0x05 0x5d ]

In the HLL, these 64 bytes are treated as two values: `[ 0x00...0001f4, 0x00...00055d ]`, or `[ 500, 1373 ]` as numbers. The code then basically sets `contract.storage[500] = 1373`.

The second clause is for everyone else. Entities outside the system can obviously grab the data by simply reading the Merkle tree, but you also want to provide a mechanism for other contracts to read the data. Thus, if someone else sends a message containing just the index, the contract returns the data at that index. The code has three lines. First, it instantiates `x` as a one-item array. Second, it sets the first (and only) item of that array to the first item in `msg.data`. This basically accomplishes the task of copying the data into memory. Finally, it returns the memory starting at `x` and going for a length of one item.

### 2-Line Namecoin

Full code (2 lines):

    if msg.data[0] >= 1000 and !contract.storage[msg.data[0]]:
        contract.storage[msg.data[0]] = msg.data[1]

This one is simple. It simply checks if (1) the storage index is high enough not to overwrite code, and (2) the storage index has not been taken yet. If both checks pass, then it sets contract storage appropriately.

### 11-Line Namecoin

Full code (11 lines):

    if msg.data[0] == 0:
        if !contract.storage[msg.data[1]*2] or msg.sender == contract.storage[msg.data[1]*2+1]:
            contract.storage[msg.data[1]*2] = msg.data[2]
            contract.storage[msg.data[1]*2+1] = msg.sender
    elif msg.data[0] == 1:
        if contract.storage[msg.data[1]*2] and msg.sender == contract.storage[msg.data[1]*2+1]:
            contract.storage[msg.data[1]*2+1] = msg.data[2]
    elif msg.data[0] == 2:
        x = array(1)
        x[0] = msg.data[1]*2
        return(x,1)

This is a much more powerful namecoin with three additional features:

1. It allows users to change the values that they registered names to, provided they own those names.
2. It allows users to transfer ownership of names that they registered.
3. It provides a "function hook", just like the data feed example above.

The contract now takes in messages of the form `[ msgtype, index, data ]`. `msgtype = 0` is a registration or a data change, `msgtype = 1` is an ownership change, and `msgtype = 2` is a function hook as in the data feed. The contract has three clauses, and each contract does the appropriate thing for each clause.

### Hedging contract

Full code (19 lines):

    if contract.storage[1000] == 0:
        if tx.value >= 1000 * 10^18:
            contract.storage[1000] = 1
            x = array(1)
            x[0] = 500
            mkcall(0x878f9ef4b0fa827ddd26872a273588ad6c2b993b,0,100,x,1,x,1)
            contract.storage[1001] = 1000 * x[0]
            contract.storage[1002] = block.timestamp + 30 * 86400
            contract.storage[1003] = msg.sender
    else:
        x = array(1)
        x[0] = 500
        mkcall(0x878f9ef4b0fa827ddd26872a273588ad6c2b993b,0,100,x,1,x,1)
        ethervalue = contract.storage[1001] / x[0]
        if ethervalue >= 2000:
            suicide(contract.storage[1003])
        else if block.timestamp > contract.storage[1002]:
            mkcall(0x8a3395e1bd856360b6f716f3361eb44e7f6bc388,(2000-ethervalue) * 10^18,0,0,0,0,0)
            suicide(contract.storage[1003])

The general point of the contract is actually not particularly difficult. First, the contract creator puts in 1000 ether. Then, some counterparty puts in 1000 ether. Suppose that at that moment 1000 ether is worth $x. After 30 days, the contract creator will be able to withdraw $x of ether, and the counterparty gets the rest. For the contract creator, this is a hedging mechanism: the contract creator puts $x in, and gets $x out. The counterparty, on the other hand, is speculating in favor of ether at high leverage; the total value of the counterparty's assets is 2000 ether minus $x.

This contract is a bit more involved, and it is also tricky because it involves a setup condition: when creating the contract, the creator must put in 1000 ether (ie. 1000 * 10^18 wei) as an endowment. From there, the contract follows the common two-stage pattern. At first, when the contract is initialized, `contract.storage[1000] = 0`. Thus, the first half of the code runs. It is assumed that this constitutes a counterparty willing to take the other side of the contract. The counterparty is required to submit 1000 ether; anything less and the transaction is treated as a simple donation. Assuming 1000 ether is actually submitted, the following happens:

Set the `contract.storage[1000]` flag to 1, so that no one else can claim the contract afterward.

    contract.storage[1000] = 1

Call the "price of ether in USD" data feed contract. There are three steps here. First, create an array to hold the input (say, index 500) to the contract. Second, make the call using that array as the input and, to save space, also as the output.

    x = array(1)
    x[0] = 500
    mkcall(0x878f9ef4b0fa827ddd26872a273588ad6c2b993b,0,100,x,1,x,1)

Now, `x[0]` represents the value of ETH/USD. Here, we set `contract.storage[1001]` to be the USD value that the counterparty just put in.
    
    contract.storage[1001] = 1000 * x[0]

We set `contract.storage[1002]` to the "expiry date"

    contract.storage[1002] = block.timestamp + 30 * 86400

And finally we set `contract.storage[1003]` to the counterparty's address.
    
    contract.storage[1003] = msg.sender

Now, the second time the contract is run, it might be run in one of two cases. First, if the value of ether drops by more than 50%, the hedger will want to immediately claim their ether (which is now the entire ether supply of the contract since you now need twice as much ether to get $x due to the price drop) and put it into another heding contract. Second, if such a calamity does not occur, the contract will naturally expire after 30 days, allowing either party (technically, anyone) to end it and refund both sides their ether.

First, do the same trick as above to set `x[0]` to the value of ETH/USD.

        x = array(1)
        x[0] = 500
        mkcall(0x878f9ef4b0fa827ddd26872a273588ad6c2b993b,0,100,x,1,x,1)

Now, calculate the amount of ether that the hedger is entitled to. Remember that `contract.storage[1001]` stores the USD amount that each side put in.

        ethervalue = contract.storage[1001] / x[0]

If it's more than or equal to 2000, the entire balance of the contract, then the hedger can wihdraw immediately. Remember that `suicide` here is jargon for "destroy the contract and dump the entire balance into the desired address".

        if ethervalue >= 2000:
            suicide(0x8a3395e1bd856360b6f716f3361eb44e7f6bc388)

Otherwise, check if the timestamp is at least the expiry date. If it is, then give the hedger $x of ether and give the counterparty whatever is left via the suicide mechanism.

        else if block.timestamp > contract.storage[1002]:
            mkcall(0x8a3395e1bd856360b6f716f3361eb44e7f6bc388,ethervalue * 10^18,0,0,0,0,0)
            suicide(contract.storage[1003])
