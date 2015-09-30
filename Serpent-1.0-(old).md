---
name: Serpent 1.0
category: 
---

Serpent is one of the high-level programming languages used to write Ethereum contracts. The language, as suggested by its name, is designed to be very similar to Python; later versions may even eventually come to target the entire [RPython spec](http://pypy.readthedocs.org/en/latest/coding-guide.html#rpython-definition). The language is designed to be maximally clean and simple, combining many of the efficiency benefits of a low-level language with ease-of-use in programming style. The latest version of the Serpent compiler, available [on github](http://github.com/ethereum/serpent), is written in C++, allowing it to be easily included in any client, and works by compiling the code first to LLL then to EVM; thus, if you like LLL, a possible intermediate option is to write Serpent, compile it to LLL, and then hand-tweak the LLL at the end.

This tutorial assumes basic knowledge of how Ethereum works, including the concept of blocks, transactions, contracts and messages and the fact that contracts take a byte array as input and provide a byte array as output. If you do not, then go [here](https://github.com/ethereum/wiki/wiki/Ethereum-Development-Tutorial) for a basic tutorial.

### Differences Between Serpent and Python

The important differences between Serpent and Python are:

* Python numbers have potentially unlimited size, Serpent numbers wrap around 2<sup>256</sup>. For example, in Serpent the expression `3^(2^254)` suprisingly evaluates to 1, even though in reality the actual integer is too large to be recorded in its entirety within the universe.
* Serpent has no decimals.
* There is no way to take a Serpent array and access its length; this sometimes leads to inconveniences like needing to type `return([a,b,c], 3)` instead of Python's `return [a,b,c]`. Future versions may change this specifically in the case of array literals.
* Serpent has no concept of strings (at this point); if you make an expression like `x = "george"`, the compiler converts that into a number using ASCII encoding, so you get 103 * 256<sup>5</sup> + 101 * 256<sup>4</sup> + 111 * 256<sup>3</sup> + 114 * 256<sup>2</sup> + 103 * 256 + 101 = 113685359126373. These numbers can be converted back into strings, so you can still represent strings, but they are limited to 32 characters in length. Future versions may change this to turn strings into char arrays, which would be accessible via `getch(str, i) -> byte` and `setch(str, i, byte)`
* Serpent has no list comprehensions (expressions like `[x**2 for x in my_list]`), dictionaries or most other advanced features
* Serpent has no function definitions or classes. Future versions may add function definitions

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

And `sc decode_datalist 000..003` gives:

    1 2 3

Additionally, the Pyethereum testing environment that we will be using simply assumes that data input and output are in this format.

Now, let's try actually compiling the code. Type:

    > sc compile mul2.se
    600e51600c6000396000f20060026000350260405460206040f2

And there we go, that's the hexadecimal form of the code that you can put into transactions. Or, if you want to see opcodes:

    > sc pretty_compile mul2.se
    PUSH1 14 DUP PUSH1 12 PUSH1 0 CODECOPY PUSH1 0 RETURN STOP PUSH1 2 PUSH1 0 CALLDATALOAD MUL PUSH1 64 MSTORE PUSH1 32 PUSH1 64 RETURN

Alternatively, you can compile to LLL (the compiler compiles through LLL anyway, so this is just stopping at an intermediate step instead of at the end):

    > sc compile_to_lll mul2.se
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

Having a multiply-by-two function on the blockchain is kind of boring. So let's do something marginally more interesting: a name registry. Our name registry will be fairly simple; it will take in two data items (ie. 64 bytes), treating the first item as the key and the second as the value. If the key is not yet registered, then it registers the key/value pair and returns 1; otherwise, it returns 0. Let's try it out:

    key = msg.data[0]
    value = msg.data[1]
    # Key not yet claimed
    if not contract.storage[key]:
       contract.storage[key] = value
       return(1)
    else:
       return([0], 1)  # Key already claimed

Here, we see a few parts in action. First, we have the `msg.data` pseudo-array, which can be used to access message input data items. Then, we have `key` and `value`, which are variables that we set. The third line is a comment; it does not get compiled and only serves to remind you what the code does. Then, we have a standard if/else clause, which checks if `contract.storage[key]` is zero (ie. unclaimed), and if it is then it sets `contract.storage[key] = value` and returns 1. Otherwise, it returns zero (which we expressed as a literal array just to exhibit this other style of returning; it's more cumbersome but lets you do return multiple values). `contract.storage` is also a pseudo-array, acting like an array but without any particular memory location.

Now, paste the code into "namecoin.se", if you wish try compiling it to LLL, opcodes or EVM, and let's try it out in the pyethereum tester environment:

    > from pyethereum import tester as t
    > s = t.state()
    > c = s.contract('namecoin.se')
    > s.send(t.k0, c, 0, ["george", 45])
    [1]
    > s.send(t.k1, c, 0, ["george", 20])
    [0]
    > s.send(t.k2, c, 0, ["harry", 65])
    [1]

### Including files

Once your projects become larger, you will not want to put everything into the same file; things become particularly inconvenient when one piece of code needs to create a contract. Fortunately, the process for splitting code into multiple files is quite simple. Make the following two files:

mul2.se:

    return(msg.data[0] * 2)

returnten.se:

    MUL2 = create('mul2.se')
    return(call(MUL2, 5))

And open Python:

    > from pyethereum import tester as t
    > s = t.state()
    > c = s.contract('returnten.se')
    > s.send(t.k0, c, 0, [])
    [10]

Another, similar, operation is `(inset 'filename')`, which simply puts code into a particular place without adding a separate contract.

### Miscellaneous

Additional Serpent coding examples can be found here: https://github.com/ethereum/serpent/tree/master/examples

The three other useful features in the tester environment are:

* Block access - you can dig around `s.block` to see block data (eg. `s.block.number`, `s.block.get_balance(addr)`, `s.block.get_storage_data(addr, index)`)
* Snapshots - you can do `x = s.snapshot()` and `s.revert(x)`
* Advancing blocks - you can do `s.mine(100)` and 100 blocks magically pass by with a 60-second interval between blocks. `s.mine(100, addr)` mines into a particular address.
* Full block data dump - type `s.to_dict()`

## Complete language spec

### Expressions

An expression is defined as anything that fits on one line. An expression is recursively defined as follows:

* A number is an expression (eg. `125`)
* A number in hex is an expression (eg. `0xdeadbeef`), and evaluates to the corresponding decimal value (in this case, `3735928559`). Uppercase does not work.
* A string is an expression (eg. `"george"`), and evaluates to the corresponding hex (in this case, `0x67656f726765`, ie. `113685359126373`). Note that due to wraparound all but the last 32 characters of a 33+ char string are truncated off.
* A variable is an expression (eg. `a`)
* An arithmetic expression built out of arithmetic operations and expressions is an expression (eg. `2 + 3`, `(4 + 5) ^ 7`, `"george" ^ 2 / (0xdeadbeef - 4848) ^ "harry"`). Supported operations are:
  * `+` (`ADD`)
  * `-` (`SUB`); note that right now `-5` is converted into `(sub 0 5)`
  * `*` (`MUL`)
  * `/` (`SDIV`, ie. signed division, where negative values of `x` are stored as `x + 2^256`, eg. -3 is `0xfffffff....ffd`)
  * `%` (`SMOD`)
  * `@/` (`DIV`, ie. unsigned division)
  * `@%` (`MOD`)
  * `^` (`EXP`)
  * `**` (`EXP`)
  * `<` (`SLT`, ie. signed comparison)
  * `@<` (`LT`, ie unsigned comparison)
  * `>` (`SGT`)
  * `@>` (`GT`)
  * `==` (`EQ`)
  * `|` (`OR`, ie. bitwise or)
  * `xor` (`XOR`)
  * `or`: `a or b` returns `a` if `a` is nonzero, otherwise `b`; `a` is never evaluated twice, and if `a != 0` then `b` is never evaluated at all
  * `and`: `a and b` returns `b` if `a` is nonzero, otherwise `0`; if `a = 0` then `b` is never evaluated at all
* An array literal consisting of expressions is an expression (eg. `[1,2,3]`, `[3 + 5, [8, 12, [24]], 9 ^ 6 / "cow"]`)
* An array access where the accessed array and the index are expressions is an expressoin (eg. `x[4]`, `y[z + 5][c + 2]`)
* A function call where the arguments are expressions is an expression (eg. `return(call(addrs[7], x ^ 3 + 5))`). Supported functions are:
  * `array(n)`: returns an empty array of size `n` (in 32-byte chunks)
  * `bytes(n)`: returns a byte array of size `n` (in bytes)
  * `getch(arr, i)`: returns the `i`th character of the given bytearray
  * `setch(arr, i, c)`: sets the `i`th character of the given bytearray to `c`
  * `msg(gas, to, value, dataval)`: sends a message with the specified recipient, quantity of ether (in wei) and amount of gas, with `dataval` as a 32-byte input, and returns the first 32 bytes of the output as a value on the stack
  * `msg(gas, to, value, datarray, insize)`: sends a message with the specified recipient, quantity of ether (in wei) and amount of gas, taking `insize` values from the specified array as input, and returns the first 32 bytes of the output as a value on the stack
  * `msg(gas, to, value, datarray, insize, outsize)`: sends a message with the specified recipient, quantity of ether (in wei) and amount of gas, taking `insize` values from the specified array as input, and returns an `outsize`-sized data array as the output
  * `call(addr, dat)` - equivalent to `msg(tx.gas - 25, addr, 0, dat)` where `tx.gas` is the remaining gas
  * `call(addr, datarray, insize)` - equivalent to `msg(tx.gas - 25, addr, 0, datarray, insize)`
  * `call(addr, datarray, insize, outsize)` - equivalent to `msg(tx.gas - 25, addr, 0, datarray, insize, outsize)`
  * `send(gas, addr, value)` - sends the desired amount of value to the desired address with the desired gas limit
  * `send(addr, value)` - equivalent to `send(tx.gas - 25, addr, value)`
  * `create('filename')` - creates a contract out of code from the desired filename and returns the address
  * `sha3(value)` - returns the SHA3 of the given value (as 32 bytes, zero-padded if necessary)
  * `sha3(str, bytes)` - returns the SHA3 of the given byte array with the given number of bytes
  * `return(value)` - exits message execution, returning the given value (as 32 bytes, zero-padded if necessary) (ie. if you run `x = send(C)` and the contract at address `C` reaches a point where there is the code `return(7)`, then in the outer execution `x` will equal 7)
  * `return(array, size)` - exits message execution, returning the given array with the given number of 32-byte chunks
  * `stop` - exits message execution, reporting nothing
  * `suicide(addr)` - destroys the contract, sending all ether to the given address
  * `debug(num)` - does nothing (in the pyethereum implementation with the `DUP POP POP` sequence of opcodes). However, implementations may wish to show the number in some kind of debug output.

### Code blocks

A code block constitutes a complete program in Ethereum. A code block is defined as follows: 

(1) An expression is a code block.  

(2) A statement of the form:

    a
    b

is a code block, where `a` and `b` are code blocks. Note that this can be expanded to basically mean that any number of code blocks with the same indenting level put together is a code block.

(3) A statement of the form:

    if a:
        b

is a code block, where `a` is an expression and `b` is a code block.

(4) A statement of the form:

    if a:
        b
    else:
        c

is a code block, where `a` is an expression and `b` and `c` are code blocks.

(5) A statement of the form:

    if a:
        b
    elif c:
        d
    else:
        e

is a code block, and an unlimited number of other elif clauses can be added in the middle.

(6) A statement of the form:

    while a:
        b

is a code block, where `a` is an expression and `b` is a code block.

(7) A statement of the form:

    init:
        a
    code:
        b

is a code block. This block should only be used at the top level; the `init` sub-block is called during initialization and the `code` sub-block is called during future executions.

(8) A statement of the form:

    shared:
        a
    init:
        b
    code:
        c

is a code block, and is synonymous with:

    init:
        a
        b
    code:
        a
        c

(9) `inset('filename')` is a code block, and is substituted at compile time with the text of the file.