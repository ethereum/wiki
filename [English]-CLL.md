### ECLL: Ethereum C-Like Language

The purpose of ECLL is to provide a language that will be simple and friendly for users to write contracts in, but at the same time easily and efficiently compile into Ethereum script code. ECLL abstracts away concepts like pointers, direct memory access and jumping in favor of a more traditional syntax of conditionals, loops, arrays and variables. Function support, including first-class-function support, will be left to a future more powerful, but less efficient, language that will be denoted EHLL (&quot;Ethereum High Level Language&quot;)

The specification will be in three parts. The first part will define the language as an abstract syntax tree, formalized using LISP-style S-expression syntax, the second part will provide specifications for what each operation does, and the third will show how the AST is to be rendered in text.

### Abstract Syntax Tree

An **expression** is defined as follows:

* A number is an expression
* A variable name is an expression
* `(&quot;arr&quot; a b)` is an expression, where `a` and `b` are expressions. This is the equivalent of C's `a[b]`.
* `(OP a b)` is an expression, where `OP` is in the set `(+ - * / % s/ s% ^ &lt; &gt; &lt;= &gt;= ==)`
* `(OP a)` is an expression, where `a` is in the set `(- !)`
* The special values `tx.datan`, `tx.value`, `tx.sender`, `contract.address`, `block.number`, `block.difficulty`, `block.timestamp`, `block.parenthash` and `block.basefee` are expressions
* `(&quot;pseudo&quot; a b)` is an expression, where `a` is a pseudoarray and `b` is an expression
* `(&quot;fun&quot; a b...)` is an expression, where `a` is a returning function and `b...` contains the valid number of arguments

A **pseudofunction** is defined as follows:

* `block.contract_storage` is a pseudofunction

A **pseudoarray** is defined as follows:

* `tx.data` and `contract.storage` are pseudoarrays
* `(&quot;pfun&quot; a b...)` where `a` is a pseudofunction and `b...` contains the valid number of arguments is a pseudoarray

`contract.storage` is a **mutable pseudoarray**, and the other two are **immutable pseudoarrays**.

A **returning function** is defined as follows:

* `block.account_balance`, `sha256`, `sha3`, `ripemd160`, `ecvalid` are returning functions
* `array` is a returning function

A **multireturning function** is defined as follows:

* `ecsign` and `ecrecover` are multireturning functions

A **multiexpression** is defined as follows:

* `(&quot;mfun&quot; a b...)` where `a` is a multireturning function and `b...` contains the valid number of arguments is a multiexpression

An **acting function** is defined as follows:

* `mktx` is an acting function

A **left-hand-side expression** is defined as follows:

* A variable is a left-hand-side expression.
* `(&quot;arr&quot; a b)` is a left-hand-side expression, where `a` is a left-hand-side expression and `b` is an expression.
* `(&quot;pseudo&quot; a b)` is a left-hand-side expression, where `a` is a mutable pseudoarray and `b` is an expression.

A **statement** is defined as follows:

* `(&quot;afun&quot; a b...)` where `a` is an acting function and `b...` contains the valid number of arguments is a statement
* `(&quot;set&quot; a b)` where `a` is a left-hand-side expression and `b` is an expression is a statement
* `(&quot;mset&quot; a... b)` where `a...` is a list of left-hand-side expressions with the valid number of arguments and `b` is a multiexpression is a statement
* `(&quot;seq&quot; a...)` where `a...` are statements
* `(&quot;if&quot; a b)` where `a` is an expression and `b` is a statement
* `(&quot;if&quot; a1 b1 &quot;elif&quot; a2 b2 &quot;elif&quot; a3 b3 ...)` where `ak` are expressions and `bk` are statements
* `(&quot;if&quot; a1 b1 &quot;elif&quot; a2 b2 &quot;elif&quot; a3 b3 ... &quot;else&quot; c)` where `ak` are expressions and `bk` and `c` are statements
* `(&quot;while&quot; a b)` where `a` is an expression and `b` is a statement
* `&quot;exit&quot;`

### Definitions

Pre-compilation, if there are `n` variables, each variable will be assigned an index in `[0,n-1]`. Let `ind(x)` be the index of variable `x`, and let `M[x]` be the contract memory at index `x`

In the context of a right-hand-side expression:

* Arithmetic works in the obvious way. `(* (+ 3 5) (+ 4 8))`, for example, returns 84.
* `a &amp;&amp; b` is a shortcut for `!(!(a) + !(b))`
* `a || b` is a shortcut for `!(!(a + b))`
* A variable `x` should become `M[ind(x)]`
* `(&quot;arr&quot; a b)` returns `M[M[ind(a)]+b]`
* `tx.datan` returns the number of data fields in the transaction
* `(&quot;pseudo&quot; &quot;tx.data&quot; i)` returns the ith data field in the transaction or zero if `i &gt;= tx.datan`
* `tx.sender`, `tx.value`, `block.basefee`, `block.difficulty`, `block.timestamp`, `block.parenthash` and `contract.address` return the obvious values.
* `(&quot;pseudo &quot;contract.storage&quot; i)` returns the contract storage at index `i`.
* `(&quot;fun&quot; &quot;block.account_balance&quot; a)` returns the balance of address `a`
* `(&quot;pseudo&quot; (&quot;pfun&quot; &quot;block.contract_storage&quot; a) b)` returns the contract storage of account `a` at index `b`
* `(&quot;mfun&quot; &quot;ecsign&quot; hash key)` returns the `v,r,s` triple from the signature
* `(&quot;mfun&quot; &quot;ecrcover&quot; h v r s)` returns the `x,y` public key
* `(&quot;mfun&quot; &quot;sha256&quot; len arr)` returns the SHA256 hash of the string of length `len` starting from `M[ind(arr)]`. `sha3` and `ripemd160` work similarly
* `(&quot;fun&quot; &quot;array&quot;)` returns `2^160 + M[2^256 - 1]` and sets `M[2^256 - 1] &lt;- 2^160 + M[2^256 - 1]`

In the context of a left-hand-side expression:

* A variable `x` returns `ind(x)`
* `(&quot;arr&quot; a b)` returns `a + b` where `a` and `b` have already been evaluated
* `(&quot;pseudo&quot; a b)` returns an object which, if set, modifies the value of the pseudoarray referent at index `a` to `b` instead of doing a memory set

In the context of a statement:

* `(&quot;afun&quot; &quot;mktx&quot; dest value dn ds)` creates a transaction sending `value` ether to `dest` with `dn` data fields starting from `M[ind(ds)]`
* `(&quot;set&quot; a b)` sets `M[a] &lt;- b` where `a` and `b` have already been evaluated
* `(&quot;mset&quot; a... b)` sequentially sets the memory index at each of the values in `a...` to a value from the multiexpression `b`
* `(&quot;if&quot; a b)`, `(&quot;while&quot; a b)` and derivatives will work as they do in other languages

### Syntax

* Arithmetic is done using infix notation using the [C++ operator precedence](http://en.wikipedia.org/wiki/Operators_in_C_and_C%2B%2B#Operator_precedence ): `^` then `* / % s/ s%` then `+ -` then `&lt; &lt;= &gt; &gt;=` then `==` then `&amp;&amp;` then `||`. That is to say, arithmetic formulas will behave as they normally do; for example, `(+ (* 3 5) (* x y))` would be (3 * 5 + x * y) and `(* (+ 3 5) (+ x y))` would be `((3 + 5) * (x + y))`
* `(&quot;arr&quot; a b)` and `(&quot;pseudo&quot; a b)` will both be represented as `a[b]`
* `(&quot;fun&quot; a b...)`, `(&quot;mfun&quot; a b...)`, `(&quot;afun&quot; a b...)` and `(&quot;pfun&quot; a...)` will all be represented as `a(b1,b2, ... ,bn)`
* Multiexpressions will be represented as `a1, a2, ... an`
* `(&quot;set&quot; a b)` and `(&quot;mset a... b)` will be represented as `a = b` and `a1, a2 ... ,an = b`, respectively.
* `(&quot;seq&quot; a...)` consists of each of the statements on their own separate line
* `(&quot;if&quot; a b)` and `(&quot;while&quot; a b)` will be represented as in Python, using the keyword directly followed by the expression for `a` and the indented statement for `b`
* Values can be represented either as integers or inside string quotes, in which case they will be treated as hexadecimal
* `exit` exits.

**Examples:**

Factorial:

    x = 1
    n = 1
    while x &lt; 10:
        n = n * x
        x = x + 1

Fibonacci sequence:

    a = array()
    a[0] = 1
    a[1] = 1
    i = 2
    while i &lt; 70:
        a[i] = a[i-1] + a[i-2]
        i = i + 1
    mktx(&quot;0676d13c8d2cf5e9e988cc3b5f6dfb5a6a3938fa&quot;,a[69],a,70)

A real example - simple forwarding contract to create a transferable account:

    if tx.value &lt; tx.basefee * 200:
        exit
    else:
        a = contract.storage[2^256 - 1]
        !(tx.sender == a) &amp;&amp; (tx.sender &gt; 0):
            exit
    if tx.data[0] == 2^160:
        contract.storage[2^256 - 1] = tx.data[1]
    else:
        if a == 0:
            contract.storage[2^256 - 1] = tx.sender
        o = array()
        i = 0
        while i &lt; tx.datan - 1:
            o[i] = tx.data[i+1]
            i = i + 1
        mktx(tx.data[0],tx.value - tx.basefee * 250,o,i)

### Alternative Syntax: C++ style

* `(&quot;seq&quot; a...)` is implemented by separating the statements with semicolons. Newlines are ignored.
* `(&quot;if&quot; a b)` and `(&quot;while&quot; a b)` will be represented as in C++, storing `a` inside brackets and `b` inside curly brackets

### Alternative Syntax: Lisp style

The AST is used as the code directly, with the minor modification that `("fun" a b...)`, `("pfun" a b...)` and `("mfun" a b...)` are replaced with `(a b...)`.