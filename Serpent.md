See https://github.com/ethereum/wiki/wiki/Serpent-1.0-(old) for Serpent 1.0.

Serpent is one of the high-level programming languages used to write Ethereum contracts. The language, as suggested by its name, is designed to be very similar to Python; it is intended to be maximally clean and simple, combining many of the efficiency benefits of a low-level language with ease-of-use in programming style, and at the same time adding special domain-specific features for contract programming. The latest version of the Serpent compiler, available [on github](http://github.com/ethereum/serpent), is written in C++, allowing it to be easily included in any client.

This tutorial assumes basic knowledge of how Ethereum works, including the concept of blocks, transactions, contracts and messages and the fact that contracts take a byte array as input and provide a byte array as output. If you do not, then go [here](https://github.com/ethereum/wiki/wiki/Ethereum-Development-Tutorial) for a basic tutorial.

### Differences Between Serpent and Python

The important differences between Serpent and Python are:

* Python numbers have potentially unlimited size, Serpent numbers wrap around 2<sup>256</sup>. For example, in Serpent the expression `3^(2^254)` suprisingly evaluates to 1, even though in reality the actual integer is too large to be recorded in its entirety within the universe.
* Serpent has no decimals.
* There is no way to take a Serpent array and access its length; this sometimes leads to inconveniences like needing to type `return([a,b,c], 3)` instead of Python's `return [a,b,c]`. Future versions may change this specifically in the case of array literals.
* Serpent has no concept of strings (at this point)
* Serpent has no list comprehensions (expressions like `[x**2 for x in my_list]`), dictionaries or most other advanced features
* Serpent has no function definitions or classes. Future versions may add function definitions
* Serpent has a concept of persistent storage variables
* Serpent has an `extern` statement used to call functions from other contracts

### Installation

In order to install the Serpent python library and executable do:

    pip install ethereum-serpent

[Install the cpp-ethereum cli tools](https://github.com/ethereum/cpp-ethereum/wiki) to run the sc commands in the tutorial

If you want a library you can directly call from C++, instead do:

    git clone http://github.com/ethereum/serpent
    cd serpent
    make
    sudo make install

### Tutorial

Now, let's write our first contract. Paste the following into a file called "mul2.se":

    return(msg.data[0] * 2)

This contract is a simple one line of code. The first thing to point out is that Serpent sees message data, memory and output in 32-byte chunks; `msg.data[0]` returns bytes 0-31 of the input, `msg.data[5]` returns bytes 160-191, etc, and `return(202020)` returns the value 202020 encoded in binary form padded to 32 bytes. From now on, these mechanics will be assumed; "the zeroth data field" will be synonymous with "bytes 0-31 of the input" and "returns three values a,b,c" will be synonymous with "returns 96 bytes consisting of the values a, b and c, each padded to 32 bytes".

Note that the Serpent compiler (included in the [cpp-ethereum toolkit](https://github.com/ethereum/cpp-ethereum)) includes some tools to make this conversion convenient; `sc encode_datalist "1 2 3"` gives:

    000000000000000000000000000000000000000000000000000000000000000100000000000000000000000000000000000000000000000000000000000000020000000000000000000000000000000000000000000000000000000000000003

And `serpent decode_datalist 000..003` gives:

    1 2 3

Additionally, the Pyethereum testing environment that we will be using simply assumes that data input and output are in this format.

Now, let's try actually compiling the code. Type:

    > serpent compile mul2.se
    600e51600c6000396000f20060026000350260405460206040f2

And there we go, that's the hexadecimal form of the code that you can put into transactions. Or, if you want to see opcodes:

    > serpent pretty_compile mul2.se
    PUSH1 14 DUP PUSH1 12 PUSH1 0 CODECOPY PUSH1 0 RETURN STOP PUSH1 2 PUSH1 0 CALLDATALOAD MUL PUSH1 64 MSTORE PUSH1 32 PUSH1 64 RETURN

Alternatively, you can compile to LLL to get an intermediate representation:

    > serpent compile_to_lll mul2.se
    (return 0 
        (lll 
            (seq 
                (set '_temp4_1 (mul (calldataload 0) 2))
                (return (ref '_temp4_1) 32)
            )
            0
        )
    )

This shows you the machinery that is going on inside. As with most contracts, the outermost layer of code exists only to copy the data of the inner code during initialization and return it, since the code returned during initialization is the code that will be executed every time the contract is called; in the EVM you can see this with the `CODECOPY` opcode, and in LLL this corresponds to the `lll` meta-operation. In the innermost layer, we take bytes 0-31 from the input, multiply that value by two, and save it in a temp variable called `_temp4_1`. We then supply the memory address of that variable, and the length 32, to the `RETURN` opcode. Note that, when dealing with message and memory data, LLL deals not with 32-byte chunks but with bytes directly, so the conversion needs to introduce these more low-level mechanics. `msg.data[1]`, for examples, is translated into `(calldataload 32)`, and `msg.data[3]` into `(calldataload 96)`.

Now, what if you want to actually run the contract? That is where [pyethereum](https://github.com/ethereum/pyethereum) comes in. Open up a Python console in the same directory, and run:

    > from pyethereum import tester as t
    > s = t.state()
    > c = s.contract('mul2.se')
    > s.send(t.k0, c, 0, [42])
    [84]
    > s.send(t.k0, c, 0, [303030])
    [606060]

The second line initializes a new state (ie. a genesis block). The third line creates a new contract, and sets `c` to its address. The fourth line sends a transaction from private key zero (the `tester` submodule includes ten pre-made key/address pairs at `k0`, `a0`, `k1`, `a1`, etc) to the address of the contract with 0 wei and with data input 42, and we see 84 predictably come out.

And there you go.

### Lesson 2: Name Registry

Having a multiply-by-two function on the blockchain is kind of boring. So let's do something marginally more interesting: a name registry. To do this, however, we will need to use a more advanced Serpent feature: functions. Functions are the way that Serpent contracts provide an "interface" to other contracts and to transactions; here, we only need one function, a function to add a key with a value to the registry. If the key is not yet registered, then we will register it and return 1, otherwise we'll return 0. Let's try it out:

    def register(key, value):
        # Key not yet claimed
        if not self.storage[key]:
            self.storage[key] = value
            return(1)
        else:
            return([0], 1)  # Key already claimed

Here, we see a few parts in action. First, we have the `msg.data` pseudo-array, which can be used to access message input data items. Then, we have `key` and `value`, which are variables that we set. The third line is a comment; it does not get compiled and only serves to remind you what the code does. Then, we have a standard if/else clause, which checks if `self.storage[key]` is zero (ie. unclaimed), and if it is then it sets `self.storage[key] = value` and returns 1. Otherwise, it returns zero (which we expressed as a literal array just to exhibit this other style of returning; it's more cumbersome but lets you do return multiple values). `self.storage` is also a pseudo-array, acting like an array but without any particular memory location.

Now, paste the code into "namecoin.se", if you wish try compiling it to LLL, opcodes or EVM, and let's try it out in the pyethereum tester environment:

    > from pyethereum import tester as t
    > s = t.state()
    > c = s.contract('namecoin.se')
    > s.send(t.k0, c, 0, funid=0, abi=[0x67656f726765, 45])
    [1]
    > s.send(t.k1, c, 0, funid=0, abi=[0x67656f726765, 20])
    [0]
    > s.send(t.k2, c, 0, funid=0, abi=[0x6861727279, 65])
    [1]

Note that because we are calling a function, we need to use the `funid` and `abi` keywords. `funid` is the index of the function; here, because the contract only has one function, `funid` is set to 0; if the contract had three functions, then calling the first function would require `funid=0`, the second would be `funid=1`, and the third `funid=2`. The `abi` parameter contains the transaction data variables. To create the hex for transaction data, we will use a slightly different function:

    > serpent encode_abi 0 0x67656f726765 45
    00000000000000000000000000000000000000000000000000000067656f726765000000000000000000000000000000000000000000000000000000000000002d

If we were calling the fifth function in some contract instead, and the arguments to the function were 10000 and 160, we would do:

    > serpent encode_abi 4 10000 160
    04000000000000000000000000000000000000000000000000000000000000271000000000000000000000000000000000000000000000000000000000000000a0

Note how the function id gets mapped to the first byte of the transaction data, and the arguments get mapped to 32 byte chunks. There is a way to create Serpent functions that take arguments taking up less than 32 bytes, but we will ignore this for now as it is a relatively advanced feature.

### Including files

Once your projects become larger, you will not want to put everything into the same file; things become particularly inconvenient when one piece of code needs to create a contract. Fortunately, the process for splitting code into multiple files is quite simple. Make the following two files:

mul2.se:

    def double(x):
        return(x * 2)

returnten.se:

    extern mul2 = [double]
    
    MUL2 = create('mul2.se')
    return(MUL2.double(5, as=mul2))

And open Python:

    > from pyethereum import tester as t
    > s = t.state()
    > c = s.contract('returnten.se')
    > s.send(t.k0, c, 0, [])
    [10]

Note that here we introduced several new features. Particularly:

* The `create` command to create a contract using code from another file
* The `extern` keyword to declare a class of contract for which we know the names of the functions
* The interface for calling other contracts

`create` is self-explanatory; it creates a contract and returns the address to the contract. The way `extern` works is that you declare a class of contract, in this case `mul2`, and then list in an array the names of the functions, in this case just `double`. From there, given any variable containing an address, you can do `x.double(arg1, as=mul2)` to call the address stored by that variable. The `as` keyword provides the class that we're using (to determine which function ID `double` corresponds to; there may be another class where `double` corresponds to 7), and the arguments are the values provided to the function. If you provide too few arguments, the rest are filled to zero, and if you provide too many the extra ones are ignored. Function calling also has some other optional arguments:

* `gas=12414` - call the function with 12414 gas instead of the default (all gas)
* `value=10^19` - send 10^19 wei (10 ether) along with the message
* `data=x`, `datasz=5` - call the function with 5 values from the array `x`; note that this replaces other function arguments. `data` without `datasz` is illegal
* `outsz=7` - by default, Serpent processes the output of a function by taking the first 32 bytes and returning it as a value. However, if `outsz` is used as here, the function will instead return an array containing 7 values; if you type `y = x.fun(arg1, outsz=7)` then you will be able to access the output via `y[0]`, `y[1]`, etc.

Another, similar, operation to `create` is `(inset 'filename')`, which simply puts code into a particular place without adding a separate contract.

### Lesson 3: Storage data structures

In more complicated contracts, you will often want to store data structures in storage to represent certain objects. For example, you might have a decentralized exchange contract that stores the balances of users in multiple currencies, as well as open bid and ask orders where each order has a price and a quantity. For this, Serpent has a built-in mechanism for defining your own structures. For example, in such a decentralized exchange contract you might see:

    data user_balances[][]
    data orders[](buys[](user, price, quantity), sells[](user, price, quantity))

Then, you might do something like:

    def fill_buy_order(currency, order_id):
        # Available amount buyer is willing to buy
        q = self.orders[currency].buys[order_id].quantity
        # My balance in the currency
        bal = self.user_balances[msg.sender][currency]
        # The buyer
        buyer = self.orders[currency].buys[order_id].user
        if q > 0:
            # The amount we can actually trade
            amount = min(q, bal)
            # Trade the currency against the base currency
            self.user_balances[msg.sender][currency] -= amount
            self.user_balances[buyer][currency] += amount
            self.user_balances[msg.sender][0] += amount * self.orders[currency].buys[order_id].price
            self.user_balances[buyer][0] -= amount * self.orders[currency].buys[order_id].price
            # Reduce the remaining quantity on the order
            self.orders[currency].buys[order_id].quantity -= amount

Notice how we define the data structures at the top, and then use them throughout the contract. These data structure gets and sets are converted into storage accesses in the background, so the data structures are persistent. 

The language for doing data structures is simple. First, we can do simple variables:

    data blah

    x = self.blah
    self.blah = x + 1

Then, we can do arrays, both finite and infinite:

    data blah[1243]
    data blaz[]

    x = self.blah[505]
    y = self.blaz[3**160]
    self.blah[125] = x + y

And we can do tuples, where each element of the tuple is itself a valid data structure:

    data body(head(eyes[2], nose, mouth), arms[2], legs[2])

    x = self.body.head.nose
    y = self.body.arms[1]

And we can do arrays of tuples:

    data bodies[100](head(eyes[2], nose, mouth), arms[2](fingers[5], elbow), legs[2])

    x = bodies[45].head.eyes[1]
    y = bodies[x].arms[1].fingers[3]

Note that the following is unfortunately not legal:

    data body(head(eyes[2], nose, mouth), arms[2], legs[2])

    x = body.head
    y = x.eyes[0]

Accesses have to descend fully in a single statement.

### Miscellaneous

Additional Serpent coding examples can be found here: https://github.com/ethereum/serpent/tree/master/examples

The three other useful features in the tester environment are:

* Block access - you can dig around `s.block` to see block data (eg. `s.block.number`, `s.block.get_balance(addr)`, `s.block.get_storage_data(addr, index)`)
* Snapshots - you can do `x = s.snapshot()` and `s.revert(x)`
* Advancing blocks - you can do `s.mine(100)` and 100 blocks magically pass by with a 60-second interval between blocks. `s.mine(100, addr)` mines into a particular address.
* Full block data dump - type `s.to_dict()`

Serpent also gives you access to many "special variables"; the full list is:

* `tx.origin` - the sender of the transaction
* `tx.gas` - gas remaining
* `tx.gasprice` - gas price of the transaction
* `msg.sender` - the sender of the message
* `msg.value` - the number of wei (smallest units of ether) sent with the message
* `self` - the contract's own address
* `self.balance` - the contract's balance
* `x.balance` (for any x) - that account's balance
* `block.coinbase` - current block miner's address
* `block.timestamp` - current block timestamp
* `block.prevhash` - previous block hash
* `block.difficulty` - current block difficulty
* `block.number` - current block number
* `block.gaslimit` - current block gaslimit