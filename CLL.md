---
name: CLL
category: 
---

### ECLL: Ethereum C-Like Language

The purpose of ECLL is to provide a language that will be simple and friendly for users to write contracts in, but at the same time easily and efficiently compile into Ethereum script code. ECLL abstracts away concepts like pointers, direct memory access and jumping in favor of a more traditional syntax of conditionals, loops, arrays and variables. Function support, including first-class-function support, will be left to a future more powerful, but less efficient, language that will be denoted EHLL ("Ethereum High Level Language")

The specification will be in three parts. The first part will define the language as an abstract syntax tree, formalized using LISP-style S-expression syntax, the second part will provide specifications for what each operation does, and the third will show how the AST is to be rendered in text.

### Abstract Syntax Tree

An **expression** is defined as follows:

* A number is an expression
* A variable name is an expression
* `("arr" a b)` is an expression, where `a` and `b` are expressions. This is the equivalent of C's `a[b]`.
* `(OP a b)` is an expression, where `OP` is in the set `(+ - * / % s/ s% ^ < > <= >= ==)`
* `(OP a)` is an expression, where `a` is in the set `(- !)`
* The special values `tx.datan`, `tx.value`, `tx.sender`, `contract.address`, `block.number`, `block.difficulty`, `block.timestamp`, `block.parenthash` and `block.basefee` are expressions
* `("pseudo" a b)` is an expression, where `a` is a pseudoarray and `b` is an expression
* `("fun" a b...)` is an expression, where `a` is a returning function and `b...` contains the valid number of arguments

A **pseudofunction** is defined as follows:

* `block.contract_storage` is a pseudofunction

A **pseudoarray** is defined as follows:

* `tx.data` and `contract.storage` are pseudoarrays
* `("pfun" a b...)` where `a` is a pseudofunction and `b...` contains the valid number of arguments is a pseudoarray

`contract.storage` is a **mutable pseudoarray**, and the other two are **immutable pseudoarrays**.

A **returning function** is defined as follows:

* `block.account_balance`, `sha256`, `sha3`, `ripemd160`, `ecvalid` are returning functions
* `array` is a returning function

A **multireturning function** is defined as follows:

* `ecsign` and `ecrecover` are multireturning functions

A **multiexpression** is defined as follows:

* `("mfun" a b...)` where `a` is a multireturning function and `b...` contains the valid number of arguments is a multiexpression

An **acting function** is defined as follows:

* `mktx` is an acting function

A **left-hand-side expression** is defined as follows:

* A variable is a left-hand-side expression.
* `("arr" a b)` is a left-hand-side expression, where `a` is a left-hand-side expression and `b` is an expression.
* `("pseudo" a b)` is a left-hand-side expression, where `a` is a mutable pseudoarray and `b` is an expression.

A **statement** is defined as follows:

* `("afun" a b...)` where `a` is an acting function and `b...` contains the valid number of arguments is a statement
* `("set" a b)` where `a` is a left-hand-side expression and `b` is an expression is a statement
* `("mset" a... b)` where `a...` is a list of left-hand-side expressions with the valid number of arguments and `b` is a multiexpression is a statement
* `("seq" a...)` where `a...` are statements
* `("if" a b)` where `a` is an expression and `b` is a statement
* `("if" a1 b1 "elif" a2 b2 "elif" a3 b3 ...)` where `ak` are expressions and `bk` are statements
* `("if" a1 b1 "elif" a2 b2 "elif" a3 b3 ... "else" c)` where `ak` are expressions and `bk` and `c` are statements
* `("while" a b)` where `a` is an expression and `b` is a statement
* `"exit"`

### Definitions

Pre-compilation, if there are `n` variables, each variable will be assigned an index in `[0,n-1]`. Let `ind(x)` be the index of variable `x`, and let `M[x]` be the contract memory at index `x`

In the context of a right-hand-side expression:

* Arithmetic works in the obvious way. `(* (+ 3 5) (+ 4 8))`, for example, returns 96.
* `a && b` is a shortcut for `!(!(a) + !(b))`
* `a || b` is a shortcut for `!(!(a + b))`
* A variable `x` should become `M[ind(x)]`
* `("arr" a b)` returns `M[M[ind(a)]+b]`
* `tx.datan` returns the number of data fields in the transaction
* `("pseudo" "tx.data" i)` returns the ith data field in the transaction or zero if `i >= tx.datan`
* `tx.sender`, `tx.value`, `block.basefee`, `block.difficulty`, `block.timestamp`, `block.parenthash` and `contract.address` return the obvious values.
* `("pseudo "contract.storage" i)` returns the contract storage at index `i`.
* `("fun" "block.account_balance" a)` returns the balance of address `a`
* `("pseudo" ("pfun" "block.contract_storage" a) b)` returns the contract storage of account `a` at index `b`
* `("mfun" "ecsign" hash key)` returns the `v,r,s` triple from the signature
* `("mfun" "ecrcover" h v r s)` returns the `x,y` public key
* `("mfun" "sha256" len arr)` returns the SHA256 hash of the string of length `len` starting from `M[ind(arr)]`. `sha3` and `ripemd160` work similarly
* `("fun" "array")` returns `2^160 + M[2^256 - 1]` and sets `M[2^256 - 1] <- 2^160 + M[2^256 - 1]`

In the context of a left-hand-side expression:

* A variable `x` returns `ind(x)`
* `("arr" a b)` returns `a + b` where `a` and `b` have already been evaluated
* `("pseudo" a b)` returns an object which, if set, modifies the value of the pseudoarray referent at index `a` to `b` instead of doing a memory set

In the context of a statement:

* `("afun" "mktx" dest value dn ds)` creates a transaction sending `value` ether to `dest` with `dn` data fields starting from `M[ind(ds)]`
* `("set" a b)` sets `M[a] <- b` where `a` and `b` have already been evaluated
* `("mset" a... b)` sequentially sets the memory index at each of the values in `a...` to a value from the multiexpression `b`
* `("if" a b)`, `("while" a b)` and derivatives will work as they do in other languages

### Syntax

* Arithmetic is done using infix notation using the [C++ operator precedence](http://en.wikipedia.org/wiki/Operators_in_C_and_C%2B%2B#Operator_precedence ): `^` then `* / % s/ s%` then `+ -` then `< <= > >=` then `==` then `&&` then `||`. That is to say, arithmetic formulas will behave as they normally do; for example, `(+ (* 3 5) (* x y))` would be (3 * 5 + x * y) and `(* (+ 3 5) (+ x y))` would be `((3 + 5) * (x + y))`
* `("arr" a b)` and `("pseudo" a b)` will both be represented as `a[b]`
* `("fun" a b...)`, `("mfun" a b...)`, `("afun" a b...)` and `("pfun" a...)` will all be represented as `a(b1,b2, ... ,bn)`
* Multiexpressions will be represented as `a1, a2, ... an`
* `("set" a b)` and `("mset a... b)` will be represented as `a = b` and `a1, a2 ... ,an = b`, respectively.
* `("seq" a...)` consists of each of the statements on their own separate line
* `("if" a b)` and `("while" a b)` will be represented as in Python, using the keyword directly followed by the expression for `a` and the indented statement for `b`
* Values can be represented either as integers or inside string quotes, in which case they will be treated as hexadecimal
* `exit` exits.

**Examples:**

Factorial:

    x = 1
    n = 1
    while x < 10:
        n = n * x
        x = x + 1

Fibonacci sequence:

    a = array()
    a[0] = 1
    a[1] = 1
    i = 2
    while i < 70:
        a[i] = a[i-1] + a[i-2]
        i = i + 1
    mktx("0676d13c8d2cf5e9e988cc3b5f6dfb5a6a3938fa",a[69],70,a)

A real example - simple forwarding contract to create a transferable account:

    if tx.value < tx.basefee * 200:
        exit
    else:
        a = contract.storage[2^256 - 1]
        if !(tx.sender == a) && (tx.sender > 0):
            exit
    if tx.data[0] == 2^160:
        contract.storage[2^256 - 1] = tx.data[1]
    else:
        if a == 0:
            contract.storage[2^256 - 1] = tx.sender
        o = array()
        i = 0
        while i < tx.datan - 1:
            o[i] = tx.data[i+1]
            i = i + 1
        mktx(tx.data[0],tx.value - tx.basefee * 250,i,o)

### Alternative Syntax: C++ style

* `("seq" a...)` is implemented by separating the statements with semicolons. Newlines are ignored.
* `("if" a b)` and `("while" a b)` will be represented as in C++, storing `a` inside brackets and `b` inside curly brackets

### Alternative Syntax: Lisp style

The AST is used as the code directly, with the minor modification that `("fun" a b...)`, `("pfun" a b...)` and `("mfun" a b...)` are replaced with `(a b...)`.
