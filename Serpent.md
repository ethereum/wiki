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
* Serpent has no concept of first-class functions. Contracts do have functions, and can call their own functions, but variables (except storage) do not persist across calls.
* Serpent has a concept of persistent storage variables (see below)
* Serpent has an `extern` statement used to call functions from other contracts (see below)

### Installation

In order to install the Serpent python library and executable do:

    sudo pip install ethereum-serpent

If you want a library you can directly call from C++, instead do:

    git clone http://github.com/ethereum/serpent
    cd serpent
    make
    sudo make install

### Tutorial

Now, let's write our first contract. Paste the following into a file called "mul2.se":

    def double(x):
         return(x * 2)

This contract is a simple two lines of code, and defines a function. Functions can be called either by transactions or by other contracts, and are the way that Serpent contracts provide an "interface" to other contracts and to transactions; for example, a contract defining a currency might have functions `send(to, value)` and `check_balance(address)`. 

Additionally, the Pyethereum testing environment that we will be using simply assumes that data input and output are in this format.

Now, let's try actually compiling the code. Type:

    > serpent compile mul2.se
    602380600b600039602e5660003560001a600014156022576020600160203760026020510260405260206040f25b5b6000f2

And there we go, that's the hexadecimal form of the code that you can put into transactions. Or, if you want to see opcodes:

    > serpent pretty_compile mul2.se
    [PUSH1, 35, DUP1, PUSH1, 11, PUSH1, 0, CODECOPY, PUSH1, 46, JUMP, PUSH1, 0, CALLDATALOAD, PUSH1, 0, BYTE, PUSH1, 0, EQ, ISZERO, PUSH1, 34, JUMPI, PUSH1, 32, PUSH1, 1, PUSH1, 32, CALLDATACOPY, PUSH1, 2, PUSH1, 32, MLOAD, MUL, PUSH1, 64, MSTORE, PUSH1, 32, PUSH1, 64, RETURN, JUMPDEST, JUMPDEST, PUSH1, 0, RETURN]

Alternatively, you can compile to LLL to get an intermediate representation:

    > serpent compile_to_lll mul2.se
    (seq 
      (return 0 
        (lll 
          (seq 
            (def ('double 'x) 
              (seq 
                (set '_temp7_1 (mul (get 'x) 2))
                (return (ref '_temp7_1) 32)
              )
            )
          )
          0
        )
      )
    )

This shows you the machinery that is going on inside. As with most contracts, the outermost layer of code exists only to copy the data of the inner code during initialization and return it, since the code returned during initialization is the code that will be executed every time the contract is called; in the EVM you can see this with the `CODECOPY` opcode, and in LLL this corresponds to the `lll` meta-operation. In the innermost layer, we set a variable `'_temp7_1` to equal twice the input, and then supply the memory address of that variable, and the length 32, to the `RETURN` opcode. The `def` mechanism itself is later translated into opcodes that actually unpack the bytes of the transaction data into the variables seen in the LLL.

Now, what if you want to actually run the contract? That is where [pyethereum](https://github.com/ethereum/pyethereum) comes in. Open up a Python console in the same directory, and run:

    > from pyethereum import tester as t
    > s = t.state()
    > c = s.contract('mul2.se')
    > s.send(t.k0, c, 0, funid=0, abi=[42])
    [84]
    > s.send(t.k0, c, 0, funid=0, abi=[303030])
    [606060]

The second line initializes a new state (ie. a genesis block). The third line creates a new contract, and sets `c` to its address. The fourth line sends a transaction from private key zero (the `tester` submodule includes ten pre-made key/address pairs at `k0`, `a0`, `k1`, `a1`, etc) to the address of the contract with 0 wei, calling function 0 (ie. the first function in the contract) with data 42, and we see 84 predictably come out.

Note that if you want to send a transaction to such a contract in the testnet or livenet, you will need to package up the transaction data for "call function 0 with data 42". The command line instruction for this is:

    > serpent encode_abi 0 42
    00000000000000000000000000000000000000000000000000000000000000002a

The first byte (ie. two hex characters) always represents the function ID; for example, if we were calling the function with ID 9 in some contract instead, we would do:

    > serpent encode_abi 9 42
    09000000000000000000000000000000000000000000000000000000000000002a

Then, after that, each argument is packed into 32 bytes. So if we wanted to encode two variables we would do:  

    > serpent encode_abi 9 42 20125
    09000000000000000000000000000000000000000000000000000000000000002a0000000000000000000000000000000000000000000000000000000000004e9d

Note that there are advanced features in Serpent for making each argument take up less than 32 bytes, but we will ignore these for now.

### Example: Name Registry

Having a multiply-by-two function on the blockchain is kind of boring. So let's do something marginally more interesting: a name registry. The main function will be a `register(key, value)` operation which checks if a given key was already taken, and if is unoccupied then register it with the desired value and return 1; if the key is already occupied return 0. We will also add a second function to check the value associated with a particular key. Let's try it out:

    def register(key, value):
        # Key not yet claimed
        if not self.storage[key]:
            self.storage[key] = value
            return(1)
        else:
            return(0)  # Key already claimed

    def ask(key):
        return(self.storage[key])

Here, we see a few parts in action. First, we have the `key` and `value` variables that the function takes as arguments. The third line is a comment; it does not get compiled and only serves to remind you what the code does. Then, we have a standard if/else clause, which checks if `self.storage[key]` is zero (ie. unclaimed), and if it is then it sets `self.storage[key] = value` and returns 1. Otherwise, it returns zero (which we expressed as a literal array just to exhibit this other style of returning; it's more cumbersome but lets you do return multiple values). `self.storage` is also a pseudo-array, acting like an array but without any particular memory location.

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
    > s.send(t.k3, c, 0, funid=1, abi=[0x6861727279])
    [65]

`funid` is the index of the function, in the order in which it appears in the contract; here, 0 is for `register` and 1 is for `ask`. If we wanted to encode the transaction data for that first call, we would do: 

    > serpent encode_abi 0 0x67656f726765 45
    00000000000000000000000000000000000000000000000000000067656f726765000000000000000000000000000000000000000000000000000000000000002d

### Including files, and calling other contracts

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

`create` is self-explanatory; it creates a contract and returns the address to the contract. The way `extern` works is that you declare a class of contract, in this case `mul2`, and then list in an array the names of the functions, in this case just `double`. From there, given any variable containing an address, you can do `x.double(arg1, as=mul2)` to call the address stored by that variable. The `as` keyword provides the class that we're using (to determine which function ID `double` corresponds to; there may be another class where `double` corresponds to 7). You do have the option of omitting the `as` keyword, but this comes at the peril of ambiguity if you are using two classes that have the same function name. The arguments are the values provided to the function. If you provide too few arguments, the rest are filled to zero, and if you provide too many the extra ones are ignored. Function calling also has some other optional arguments:

* `gas=12414` - call the function with 12414 gas instead of the default (all gas)
* `value=10^19` - send 10^19 wei (10 ether) along with the message
* `data=x`, `datasz=5` - call the function with 5 values from the array `x`; note that this replaces other function arguments. `data` without `datasz` is illegal
* `outsz=7` - by default, Serpent processes the output of a function by taking the first 32 bytes and returning it as a value. However, if `outsz` is used as here, the function will instead return an array containing 7 values; if you type `y = x.fun(arg1, outsz=7)` then you will be able to access the output via `y[0]`, `y[1]`, etc.

Another, similar, operation to `create` is `(inset 'filename')`, which simply puts code into a particular place without adding a separate contract.

### Storage data structures

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

Note that finite arrays are always preferred, because it will cost less gas to calculate the storage index associated with a finite array index lookup. And we can do tuples, where each element of the tuple is itself a valid data structure:

    data body(head(eyes[2], nose, mouth), arms[2], legs[2])

    x = self.body.head.nose
    y = self.body.arms[1]

And we can do arrays of tuples:

    data bodies[100](head(eyes[2], nose, mouth), arms[2](fingers[5], elbow), legs[2])

    x = self.bodies[45].head.eyes[1]
    y = self.bodies[x].arms[1].fingers[3]

Note that the following is unfortunately not legal:

    data body(head(eyes[2], nose, mouth), arms[2], legs[2])

    x = self.body.head
    y = x.eyes[0]

Accesses have to descend fully in a single statement. To see how this could be used in a simpler example, let's go back to our name registry, and upgrade it so that when a user registers a key they become the owner of that key, and the owner of a key has the ability to (1) transfer ownership, and (2) change the value. We'll remove the return values here for simplicity.

    data registry[](owner, value)

    def register(key):
        # Key not yet claimed
        if not self.registry[key].owner:
            self.registry[key].owner = msg.sender

    def transfer_ownership(key, new_owner):
        if self.registry[key].owner == msg.sender:
            self.registry[key].owner = new_owner

    def set_value(key, new_value):
        if self.registry[key].owner == msg.sender:
            self.registry[key].value = new_value

    def ask(key):
        return([self.registry[key].owner, self.registry[key].value], 2)

Note that in the last ask command, the function returns an array of 2 values. If you wanted to call the registry, you would have needed to do something like `o = registry.ask(key, outsz=2)` and you could have then used `o[0]` and `o[1]` to recover the owner and value. 

### Macros

Macros allow you to create rewrite rules which provide additional expressivity to the language. For example, suppose that you wanted to create a command that would compute the median of three values. You could simply do:

    macro median($a, $b, $c):
        min(min(smax($a, $b), max($a, $c)), max($b, $c))

Then, if you wanted to use it somewhere in your code, you just do:

    x = median(5, 9, 7)

Or to take the max of an array:

    macro maxarray($a:$asz):
        m = 0
        i = 0
        while i < $asz:
            m = max(m, $a[i])
            i += 1
        m

    x = maxarray([1, 9, 5, 6, 2, 4]:6)

For a highly contrived example of just how powerful macros can be, see https://github.com/ethereum/serpent/blob/poc7/examples/peano.se

Note that macros are not functions; they are copied into code every time they are used. Hence, if you have a long macro, you may instead with to make the macro call an actual function.

### Types

An excellent compliment to macros is Serpent's ghetto type system, which can be combined with macros to produce quite interesting results. Let us simply show this with an example:

    type float: [a, b, c]

    macro float($x) + float($y):
        float($x + $y)

    macro float($x) - float($y):
        float($x - $y)

    macro float($x) * float($y):
        float($x * $y / 2^32)

    macro float($x) / float($y):
        float($x * 2^32 / $y)

    macro unfloat($x):
        $x / 2^32

    macro floatfy($x):
        float($x * 2^32)

    macro float($x) = float($y):
        $x = $y

    macro with(float($x), float($y), $z):
        with($x, $y, $z)

    a = floatfy(25)
    b = a / floatfy(2)
    c = b * b
    return(unfloat(c))

This returns 156, the integer portion of 12.5^2. A purely integer-based version of this code would have simply returned 144. An interesting use case would be rewriting the [elliptic curve signature pubkey recovery code](https://github.com/ethereum/serpent/blob/df0aa0e1285d7667d4a0cc81b1e11e0abb31fff3/examples/ecc/jacobian_add.se) using types in order to make the code neater by making all additions and multiplications implicitly modulo P, or using [long integer types](https://github.com/ethereum/serpent/blob/poc7/examples/long_integer_macros.se) to do RSA and other large-value-based cryptography in EVM code.

### Miscellaneous

Additional Serpent coding examples can be found here: https://github.com/ethereum/serpent/tree/master/examples

The three other useful features in the tester environment are:

* Block access - you can dig around `s.block` to see block data (eg. `s.block.number`, `s.block.get_balance(addr)`, `s.block.get_storage_data(addr, index)`)
* Snapshots - you can do `x = s.snapshot()` and `s.revert(x)`
* Advancing blocks - you can do `s.mine(100)` and 100 blocks magically pass by with a 60-second interval between blocks. `s.mine(100, addr)` mines into a particular address.
* Full block data dump - type `s.block.to_dict()`

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

Serpent recognises the following "special functions":

* `init` - executed upon contract creation
* `shared` - executed before running `init` and user functions
* `any` - executed before any user functions

There are also special commands for a few crypto operations; particularly:

* `addr = ecrecover(h, v, r, s)` - determines the address that produced the elliptic curve signature `v, r, s` of the hash `h`
* `x = sha256(a, 4)` - returns the sha256 hash of the 128 bytes consisting of the 4-item array starting from `a`
* `x = ripemd160(a, 4)` - same as above but for ripemd160