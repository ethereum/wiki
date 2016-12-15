# This page has moved

The current Solidity documentation can be found at http://solidity.readthedocs.io/en/latest/

# Foreword

**Yoichi: no foreword of this detail can be found in the official documentation.**

Solidity is roughly speaking, an object-oriented language designed for writing contracts in Ethereum. Contracts are (typically) small programs which govern the behaviour of accounts within the Ethereum state. These programs operate within the context of the Ethereum environment. Such accounts are able to pass messages between themselves as well as doing practically Turing complete computation.

Solidity is perhaps the first example of a *contract-oriented* programming language; a slight tweak on the notion of object-orientation. While closely related to object-oriented languages, this is a language designed specifically to help express agreements that must encode ideas and relationships relevant to Real Life, or some formal model thereof. As such we see notions such as ownership, identity, protections and restrictions forming a core part of the vocabulary and idiomatic grammar.

We see language grammar actually tieing in with many of the aspects of this: the `event` primitives along with the `indexed` keyword explicitly address the logging environment which Ethereum provides. The variadic return values mimic the fact that output data of Ethereum's calling mechanism is, like the input data, an arbitrary byte array.

# Basic Contract Anatomy

## Hello, World!

**Yoichi: this example is missing from the official documentation**

No language would be complete without a Hello World program. Operating within the Ethereum environment, Solidity has no obvious way of "outputting" a string. The closest we can do is to use a log event to place a string into the blockchain:

```
contract HelloWorld {
  event Print(string out);
  function() { Print("Hello, World!"); }
}
```

This contract, if placed on the blockchain and called, would create a log entry on the blockchain of type `Print` with a parameter `"Hello, World!"`. Easy, eh?

## The Contract

**Yoichi: This example is missing from the official documentation**

The contract is the basic structure of Solidity. It is a prototype of an object which lives on the blockchain. A contract may be instantiated into a contract-account (or 'object', or sometimes just 'account') at which point it gets a uniquely identifying address with which it may be called. The address here is similar to a reference or pointer in C-like languages, or just a plain old object in Javascript. Like plain objects in many object-oriented languages, contracts can never run themselves - they may only be called, or, put another way, they can only react to the receipt of a message; they can never be proactive.

We denote a contract with the `contract` keyword and a name, followed by its definition enclosed in braces (`{` & `}`). Here is the most basic contract:

```
contract MostBasicContract {
}
```

It doesn't do anything. To make it do something when it receives a message, we can introduce a function. The function you've already seen is the so-called default function. Why this is default will be properly explained later, but for now, suffice it to say that it is simply the function that gets called when this contract (well, an instantiation of it, anyway) receives a message from the Ethereum Environment.

The syntax of a function is similar to Javascript: it begins with the `function` keyword, then has a parenthesised parameter list, and finally has a braced expression block.

```
contract MostBasicContract {
	function() {
		// executed when a message is received.
	}
}
```

This expression block is very similar to other C-like languages including Javascript. Within our expression block, we are able to do arbitrary computation; for instance, we might try to determine what the sum of 1 and 1 is and place it into a variable called `two`:

```
contract Simple {
	function() {
		var two = 1 + 1;
	}
}
```

Unlike in Javascript, all variables must be declared prior to use and typed. However, for convenience, `var` is provided as a way to automatically determine the type through the expression it is initialised to. In this case, the type is `uint`, but more on the types later.

## Simple Types

**Yoichi: this kind of list of types is missing from the official documentation.**

The type system in Solidity revolves largely around the 256-bit word size of the EVM. To this end, there are 4 major types, each of which are specifically 256-bits wide:

- `uint`: 256-bit unsigned integer, operable with bitwise and unsigned arithmetic operations.
- `int`: 256-bit signed integer, operable with bitwise and signed arithmetic operations.
- `real`: 256-bit signed fixed-point quantity, 127-bit left of the point, 128-bit right.
- `bytes32`: sequence of 32 bytes (256 bit in total).

In addition to these, there is the intrinsic `address` type used for identifying specific accounts and a bool type for representing true and false:

- `address`: account identifier, 160-bits.
- `bool`: two-state value.

Most of the time, we'll tend to use `uint` and `bytes32`; the former if we intend to do arithmetic, the latter when we need to identify or refer to pieces of data, events, &c.

So to declare a variable it is very similar to the C-like languages: we simply place the type, followed by the variable name and finish the expression with a `;`. For example to declare a uint named `x` we would write:

```
uint x;
```

If we then wanted to assign `69` to it, we'd use the assignment operator, `=` (not forgetting to place the trailing `;`:

```
uint x;
x = 69;
```

There is a shorthand to declaring and assigning; those two statements may be collapsed over the `x` into:

```
uint x = 69;
```

In this situation, where we are simultaneously declaring and initialising the variable, we are able to substitute the type with the keyword `var`. If we do this, the type will be determined through the type of the expression we are assigning:

```
var x = 69;
```

If we were to assign a decimal, `var` would become equivalent to a real:

```
var x = 69;		// here, "var" is equivalent to "uint".
var y = 69.42;	// here, "var" is equivalent to "real".
```
