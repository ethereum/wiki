The purpose of this page is to serve as an introduction to the basics of Ethereum that you will need to understand from a development standpoint, in order to produce contracts and decentralized applications. For a general introduction to Ethereum, see [the white paper](http://ethereum.org/ethereum.html), and for a full technical spec see the [yellow](http://gavwood.com/Paper.pdf) papers, although those are not prerequisites for this page; that is to say, this page is meant as an alternative introduction to Ethereum specifically targeted toward application developers.

### Introduction

Ethereum is a platform that is intended to allow people to easily write decentralized applications (Đapps) using blockchain technology. A decentralized application is an application which serves some specific purpose to its users, but which case the important property that the application itself does not depend on any specific party existing. Rather than serving as a front-end for selling or providing a specific party's services, a Đapp is a tool for people and organizations on different sides on different sides of an interaction to come together without any centralized intermediary. Even necessary "intermediary" functions that are typically the domain of centralized providers, such as filtering, identity management, escrow and dispute resolution, are either handled directly by the network or left open for anyone to participate, using tools like internal token systems and reputation systems to ensure that users get access to high-quality services. Early examples of Đapps include BitTorrent for file sharing and Bitcoin for currency. Ethereum takes the primary developments used by BitTorrent and Bitcoin, the peer to peer network and the blockchain, and generalizes them in order to allow developers to use these technologies for any purpose.

The Ethereum blockchain can be alternately described as a blockchain with a built-in programming lanuguage, or a consensus-based globally executed virtual machine. The part of the blockchain that actually handles internal state and computation is referred to as the Ethereum Virtual Machine (EVM). From a practical standpoint, the EVM can be thought of as a large decentralized computer containing millions of objects, called "accounts", where each object contains its own internal code and a 32-byte key/value database called storage and objects can call other objects at will. In order to allow the outside world to interact with the EVM, a special type of account exists called an "externally owned account" (EAO), and a message from an EAO to any other account can be triggered by sending a transaction signed by the private key associated with that EAO. When an account that is not an EAO, called a "contract", receives a message, it automatically runs its code, and the code has the ability to read/write to its own internal storage and send other messages.

Contracts generally serve four purposes:

1. Maintain a data store representing something which is useful to either other contracts or to the outside world; one example of this is a contract that simulates a currency, and another is a contract that records membership in a particular organization.

2. Serve as a sort of externally owned account with a more complicated access policy; this is called a "forwarding contract" and typically involves simply resending incoming messages to some desired destination only if certain conditions are met; for example, one can have a forwarding contract that waits until two out of a given three private keys have confirmed a particular message before resending it (ie. multisig). More complex forwarding contracts have different conditions based on the nature of the message sent; the simplest use case for this functionality is a withdrawal limit that is overridable via some more complicated access procedure.

3. Manage an ongoing contract or relationship between multiple users. Examples of this include a financial contract, an escrow with some particular set of mediators, or some kind of insurance. One can also have an open contract that one party leaves open for any other party to engage with at any time; one example of this is a contract that automatically pays a bounty to whoever submits a valid solution to some mathematical problem, or proves that it is providing some computational resource.

4. Provide functions to other contracts; essentially, a software library.

Contracts interact with each other through an activity that is alternately called either "calling" or "sending messages". Essentially, a "message" is an object containing some quantity of ether (a special internal currency used in Ethereum with the primary purpose of paying transaction fees), a byte-array of data of any size, a sender and a recipient, and when a contract receives a message it has the option of returning some data, which the original sender of the message can then immediately use. In this way, message sending is exactly like calling a function.

Because contracts can play such different roles, we expect that contracts will be interacting with each other. As an example, consider a situation where Alice and Bob are betting 100 GavCoin that the temperature in San Francisco will not exceed 35'C at any point in the next year. However, Alice is very security-conscious, and as her primary account uses a forwarding contract which only sends messages with the approval of two out of three private keys. Bob is paranoid about quantum cryptography, so he uses a forwarding contract which passes along only messages that have been signed with [Lamport signatures](https://en.wikipedia.org/wiki/Lamport_signature) alongside traditional ECDSA (but because he's old fashioned, he prefers to use a version of Lamport sigs based on SHA256, which is not supported in Ethereum directly). The betting contract itself needs to fetch data about the San Francisco weather from some contract, and it also needs to talk to the GavCoin contract when it wants to actually send the GavCoin to either Alice or Bob (or, more precisely, Alice or Bob's forwarding contract). Thus, we can show the relationships between the accounts thus:

![img](http://vitalik.ca/files/contract_relationship.png)

When Bob wants to finalize the bet, the following steps happen:

1. A transaction is sent, triggering a message from Bob's EAO to Bob's forwarding contract.
2. Bob's forwarding contract sends the hash of the message and the Lamport signature to a contract which functions as a Lamport signature verification library.
3. The Lamport signature verification library sees that Bob wants a SHA256-based Lamport sig, so it calls the SHA256 library many times as needed to verify the signature.
4. Once the Lamport signature verification library returns 1, signifying that the signature has been verified, it sends a message to the contract representing the bet.
5. The bet contract checks the contract providing the San Francisco price to see what the price is.
6. The bet contract sees that the response to the messages shows that the price is above 35'C, so it sends a message to the GavCoin contract to move the GavCoin from its account to Bob's forwarding contract.

Note that the GavCoin is all "stored" as database entries in the GavCoin contract; the word "account" in the context of step 6 simply means that there is a data entry in the GavCoin contract storage with its key being the bet contract's address and the value being some nonzero quantity, and after receiving this message the GavCoin contract decreases this value by some amount and increases the value in the entry corresponding to Bob's forwarding contract's address. We can see these steps in the following diagram:

![img](http://vitalik.ca/files/contract_relationship2.png)

### State Machine

Computation in the EVM is done using a stack-based bytecode language that is like a cross between Bitcoin Script, traditional assembly and Lisp (the Lisp part being due to the recursive message-sending functionality). A program in EVM is a sequence of opcodes, like this:

    PUSH1 0 CALLDATALOAD SLOAD NOT PUSH1 9 JUMPI STOP PUSH1 32 CALLDATALOAD PUSH1 0 CALLDATALOAD SSTORE

The purpose of this particular contract is to serve as a name registry; anyone can send a message containing 64 bytes of data, 32 for the key and 32 for the value. The contract checks if the key has already been registered in storage, and if it has not been then the contract registers the value at that key.

During execution, an infinitely expandable byte-array called "memory", the "program counter" pointing to the current instruction, and a stack of 32-byte values is maintained. At the start of execution, memory and stack are empty and the PC is zero. Now, let us suppose the contract with this code is being accessed for the first time, and a message is sent in with 123 wei (10<sup>18</sup> wei = 1 ether) and 64 bytes of data where the first 32 bytes encode the number 54 and the second 32 bytes encode the number 2020202020.

Thus, the state at the start is:

    PC: 0 STACK: [] MEM: [], STORAGE: {}

The instruction at position 0 is PUSH1, which pushes a one-byte value onto the stack and jumps two steps in the code. Thus, we have:

    PC: 2 STACK: [0] MEM: [], STORAGE: {}

The instruction at position 2 is CALLDATALOAD, which pops one value from the stack, loads the 32 bytes of message data starting from that index, and pushes that one the stack. Recall that the first 32 bytes here encode 54.

    PC: 3 STACK: [54] MEM: [], STORAGE: {}

SLOAD pops one from the stack, and pushes the value in contract storage at that index. Since the contract is used for the first time, it has nothing there, so zero.

    PC: 4 STACK: [0] MEM: [], STORAGE: {}

NOT pops one value and pushes 1 if the value is zero, else 0

    PC: 5 STACK: [1] MEM: [], STORAGE: {}

Next, we PUSH1 9.

    PC: 7 STACK: [1, 9] MEM: [], STORAGE: {}

The JUMPI instruction pops 2 values and jumps to the instruction designated by the first only if the second is nonzero. Here, the second is nonzero, so we jump. If the value in storage index 54 had not been zero, then the second value from top on the stack would have been 0 (due to NOT), so we would not have jumped, and we would have advanced to the STOP instruction which would have led to us stopping execution.

    PC: 9 STACK: [] MEM: [], STORAGE: {}

Here, we PUSH1 32.

    PC: 11 STACK: [32] MEM: [], STORAGE: {}

Now, we CALLDATALOAD again, popping 32 and pushing the bytes in message data starting from byte 32 until byte 63.

    PC: 13 STACK: [2020202020] MEM: [], STORAGE: {}

Next, we PUSH1 0.

    PC: 14 STACK: [2020202020, 0] MEM: [], STORAGE: {}

Now, we load message data bytes 0-31 again (loading message data is just as cheap as loading memory, so we don't bother to save it in memory)

    PC: 16 STACK: [2020202020, 54] MEM: [], STORAGE: {}

Finally, we SSTORE to save the value 2020202020 in storage at index 54.

    PC: 17 STACK: [] MEM: [], STORAGE: {54: 2020202020}

At index 17, there is no instruction, so we stop. If there was anything left in the stack or memory, it would be deleted, but the storage will stay and be available next time someone sends a message. Thus, if the sender of this message sends the same message again (or perhaps someone else tries to reregister 54 to 3030303030), the next time the `JUMPI` at position 7 would not process, and execution would STOP early at position 8.

Fortunately, you do not have to program in low-level assembly; a number of high-level languages such as [LLL](https://github.com/ethereum/cpp-ethereum/wiki/LLL-PoC-5), [serpent](https://github.com/ethereum/wiki/wiki/Serpent) and [Mutan](https://github.com/ethereum/go-ethereum/wiki/Mutan-0.2) exist to make it much easier for you to write contracts. Any code you write in these languages gets compiled into EVM, and to create the contracts you send the transaction containing the EVM bytecode.

There are two types of transactions: a sending transaction and a contract creating transaction. A sending transaction is a standard transaction, containing a receiving address, an ether amount, a data bytearray and some other parameters, and a signature from the private key associated with the sender account. A contract creating transaction looks like a standard transaction, except the receiving address is blank. When a contract creating transaction makes its way into the blockchain, the data bytearray in the transaction is interpreted as EVM code, and the value returned by that EVM execution is taken to be the code of the new contract; hence, you can have a transaction do certain things during initialization. The address of the new contract is deterministically calculated based on the sending address and the number of times that the sending account has made a transaction before (this value, called the account nonce, is also kept for unrelated security reasons). Thus, the full code that you need to put onto the blockchain to produce the above name registry is as follows:

    PUSH1 16 DUP PUSH1 12 PUSH1 0 CODECOPY PUSH1 0 RETURN STOP PUSH1 0 CALLDATALOAD SLOAD NOT PUSH1 9 JUMPI STOP PUSH1 32 CALLDATALOAD PUSH1 0 CALLDATALOAD SSTORE

The key opcodes are CODECOPY, copying the 16 bytes of code starting from byte 12 into memory starting at index 0, and RETURN, returning memory bytes 0-16, ie. code byes 12-28 (feel free to "run" the execution manually on paper to verify that those parts of the code and memory actually get copied and returned). Code bytes 12-28 are, of course, the actual code as we saw above.

### Gas

An important point of the way the EVM works is that every single operation that is executed inside the EVM is actually simultaneously executed on every full node. This is a necessary component of the Ethereum 1.0 consensus model, and has the benefit that any contract on the EVM can call any other contract at almost zero cost, but also has the drawback that computational steps on the EVM are very expensive. Roughly, a good heuristic to use is that you will not be able to do anything on the EVM that you cannot do on a smartphone from 1999. Acceptable uses of the EVM include running business logic ("if this then that") and verifying signatures and other cryptographic objects; at the upper limit of this are applications that verify parts of other blockchains (eg. a decentralized ether-to-bitcoin exchange); unacceptable uses include using the EVM as a file storage, email or text messaging system, anything to do with graphical interfaces, and applications best suited for cloud computing like genetic algorithms, graph analysis or machine learning.

In order to prevent deliberate attacks and abuse, the Ethereum protocol charges a fee per computational step. The fee is market-based, though mandatory in practice; a floating limit on the number of operations that can be contained in a block forces even miners who can afford to include transactions at close to no cost to charge a fee commensurate with the cost of the transaction to the entire network; see [the whitepaper section on fees](https://github.com/ethereum/wiki/wiki/%5BEnglish%5D-White-Paper#fees) for more details on the economic underpinnings of our fee and block operation limit system.

The way the fee works is as follows. Every transaction must contain, alongside its other data, a `GASPRICE` and `STARTGAS` value. `STARTGAS` is the amount of "gas" that the transaction assigns itself, and `GASPRICE` is the fee that the transaction pays per unit of gas; thus, when a transaction is sent, the first thing that is done during evaluation is subtracting `STARTGAS * GASPRICE` wei plus the transaction's value from the sending account balance. `GASPRICE` is set by the transaction sender, but miners have the ability to refuse to process transactions whose `GASPRICE` is too low. When transaction execution starts, a special value is initialized and maintained alongside the PC, memory and stack, known as "gas"; initially the value is set to `STARTGAS - 500 - 5 * TXDATALEN` where `TXDATALEN` is the number of bytes in transaction data. Every computational step, a certain amount (usually 1, sometimes more depending on the operation) is subtracted from the gas. If gas goes down to zero, then all execution reverts, but the transaction is still valid and the sender still has to pay for gas. If transaction execution finishes with `N >= 0` gas remaining, then the sending account is refunded with `N * GASPRICE` wei.

During contract execution, when a contract sends a message, that message call itself comes with a gas limit, and the sub-execution works the same way (namely, it can either run out of gas and revert or execute successfully and return a value). If sub-execution runs out of gas, the parent execution continues; thus, it is perfectly "safe" for a contract to call another contract if you set a gas limit on the sub-execution. If sub-execution has some gas remaining, then that gas is returned to the parent execution to continue using.

### Virtual machine opcodes

The opcodes in the EVM are as follows:

* `0x00`: `STOP`, stops execution
* `0x01`: `ADD`, pops 2 values `a`, `b` from the top of the stack and pushes `a+b` to the top of the stack (all arithmetic is modulo 2<sup>256</sup>)
* `0x02`: `MUL`, pops 2 values `a`, `b`, pushes `a*b`
* `0x03`: `SUB`, pops 2 values `a`, `b`, pushes `a-b` (`a` is the value immediately at the top of the stack before execution, `b` is second from top)
* `0x04`: `DIV`, pops 2 values `a`, `b`, pushes `a/b`
* `0x05`: `SDIV`, pops 2 values `a`, `b`, pushes `a/b`, except where `a` and `b` are treated as signed integers, ie. if `a >= 2^255` then it's treated as the negative value `a - 2^256`. Division with negative numbers is done as in Python, ie. `25 / 3 = 8`, `25 / -3 = -9`, `-25 / 3 = -9`, `-25 / -3 = 8`
* `0x06`: `MOD`, pops 2 values `a`, `b`, pushes `a%b`
* `0x07`: `SMOD`, pops 2 values `a`, `b`, pushes `a%b` treating `a`, `b` as signed values. Modulo with negative numbers is done as in Python, ie. `25 % 3 = 1`, `25 % -3 = -2`, `-25 % 3 = 2`, `-25 % -3 = -1`
* `0x08`: `EXP`, pops 2 values `a`, `b`, pushes `a^b`
* `0x09`: `NEG`, pops 1 value `a`, pushes `-a` (ie. `2^256 - a`)
* `0x0a`: `LT`, pops 2 values `a`, `b`, pushes 1 if `a < b` else 0
* `0x0b`: `GT`, pops 2 values `a`, `b`, pushes 1 if `a > b` else 0
* `0x0c`: `SLT`, pops 2 values `a`, `b`, pushes 1 if `a < b` else 0, doing a signed comparison
* `0x0d`: `SGT`, pops 2 values `a`, `b`, pushes 1 if `a > b` else 0, doing a signed comparison
* `0x0e`: `EQ`, pops 2 values `a`, `b`, pushes 1 if `a == b` else 0
* `0x0f`: `NOT`, pops 1 value `a`, pushes 1 if `a = 0` else 0
* `0x10`: `AND`, pops 2 values `a`, `b`, pushes the bitwise and of `a` and `b`
* `0x11`: `OR`, pops 2 values `a`, `b`, pushes the bitwise or of `a` and `b`
* `0x12`: `XOR`, pops 2 values `a`, `b`, pushes the bitwise xor of `a` and `b`
* `0x13`: `BYTE`, pops 2 values `a`, `b`, pushes the `a`th byte of `b` (zero if `a >= 32`)
* `0x20`: `SHA3`, pops 2 values `a`, `b`, pushes `SHA3(memory[a: a+b])`
* `0x30`: `ADDRESS`, pushes the contract address
* `0x30`: `ADDRESS`, pushes the contract address
* `0x30`: `ADDRESS`, pushes the contract address
* `0x30`: `ADDRESS`, pushes the contract address
* `0x01` - `0x0f`: arithmetic (ie. pop two values `a`,`b` from the stack, push `a+b`, `a%b`, etc)
* `0x10` - `0x13`: bitwise operations (eg. AND, OR, XOR)
* `0x20`: SHA3 (takes a start location and length in memory, and outputs the SHA3 of that onto the stack)
* `0x30` - `0x3a`: data about the incoming message, originating transaction, and your contract
* `0x40` - `0x45`: data about the block (eg. number, timestamp, prevhash)
* `0x50` - `0x5c`: operations that manipulate or provide information about the temporary state (stack, program counter, memory) and storage
* `0x60` - `0x7f`: push the next 1 to 32 bytes as a value onto the stack, zero-padding to 32 bytes
* `0xf0` - create a new contract
* `0xf1` - send a message to a contract
* `0xf2` - return data from memory
* `0xff` - suicide, deleting the contract and sending remaining balance into a particular address

Note that high-level languages will often have their own wrappers for these opcodes, sometimes with very different interfaces.

### Fine Points To Keep Track Of

* Storage is a key/value store where keys and values are both 32 bytes
* Values on the stack are 32 bytes
* Memory is a byte-array. Memory starts off zero-size, but can be expanded in 32-byte chunks by simply accessing or storing memory at indices greater than its current size. There is a fee of 1 gas per 32 bytes for expanding memory.
* All arithmetic is modulo 2<sup>256</sup>. For division, modulo and comparison, both signed and unsigned operators exist (eg. `(0 - 27) / 3` returns -9 if `SDIV` is used, but `38597363079105398474523661669562635951089994888546854679819194669304376546636` if `DIV` is used.
* Truncation and modulo operations with negative operators in the `SDIV`/`SMOD` case are handled as in Python (eg. , )
* `DIV`, `SDIV`, `MOD` and `SMOD` with dividend (second argument) zero are equivalent to `STOP`
* Any operation that tries to take more values off the stack than are on the stack is equivalent to `STOP`
* The SHA3 and RETURN opcodes take two values off the stack: start memory index and memory length, eg. `PUSH1 13 PUSH1 75 SHA3` takes the SHA3 of memory bytes 75...87.
* The `CREATE` opcode takes three values: endowment (ie. initial amount of ether), memory start and memory length, and pushes onto the stack the address of the new contract. `CREATE` gives the initializing sub-execution all the gas that you have (and if gas remains then it gets refunded back to the parent execution)
* The `CALL` opcode takes seven values: gas, recipient, ether value, memory location of start of input data, length of input data, memory location to put start of output data, length of output data. It puts onto the stack either 1 for success (ie. did not run out of gas) or 0 for failure. Note that if execution stops due to a non-gas-related error (eg. division by zero) that still counts as success; the only thing that counts as failure is 0 gas.
* When a contract calls `SUICIDE`, its ether is immediately sent to the desired address, but the contract continues existing until the end of transaction execution.
* If contract A calls contract B calls contract A, then the inner execution of A will have its own, fresh, memory, stack and PC, but it will modify and read the same balance and storage.
* If contract initialization returns an empty array, then no contract will be created. This allows you to "abuse" contract initialization as an atomic multi-operation, which might be useful in some protocols where you want to do multiple things but you don't want some of them to be able to process without others.