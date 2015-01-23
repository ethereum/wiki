# Foreword

Solidity is perhaps the first example of a contract-oriented programming language. While closely related to object-oriented languages, this is a language designed specifically to help express agreements that must encode ideas and relationships relevant to Real Life, or some formal model thereof. As such we see notions such as ownership, identity, protections and restrictions forming a core part of the vocabulary and idiomatic grammar.

Solidity is designed with a specific operating environment, or more formally, state-transitioning machine, in mind, specifically that in which the Ethereum Virtual Machine runs and which is encoded by the Ethereum world state. I generally refer to this as the Ethereum State Platform, or Ethereum Environment.

We see language grammar actually tieing in with many of the aspects of this: the `event` primitives along with the `indexed` keyword explicitly address the logging environment which Ethereum provides. The variadic return values mimic the fact that output data of Ethereum's calling mechanism is, like the input data, an arbitrary byte array.

# Basic Contract Anatomy

## Hello, World!

No language would be complete without a Hello World program. Unfortunately, being based around the Ethereum State Platform, Solidity has no obvious way of outputting a string. The closest we can do is to use a log event:

```
contract HelloWorld {
  event Print(string);
  function() { Print("Hello, World!"); }
}
```

This contract, if placed on the blockchain and called, would create a log entry on the blockchain of type `Print` with a parameter `"Hello, World!"`. Easy, eh?

## The Contract

The contract is the basic structure of Solidity. It is a prototype of an object which lives on the blockchain. A contract may be instantiated into a contract-account (or 'object', or sometimes just 'account') at which point it gets a uniquely identifying address with which is may be called. The address here is similar to a reference or pointer in C-like languages, or just a plain old object in Javascript. Like plain objects in many object-oriented languages, contracts can never run themselves - they may only be called, or, put another way, they can only react to the receipt of a message; the can never be proactive.

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

Unlike in Javascript, all variables must be declared prior to use and typed. However, for convenience, `var` is provided as a way to automatically determine the type.

## Simple Types

The type system in Solidity revolves largely around the 256-bit word size of the EVM. To this end, there are 5 major types, each of which are specifically 256-bits wide:

- `hash`: 256-bit, 32-byte data chunk, indexable into bytes and operable with bitwise operations.
- `uint`: 256-bit unsigned integer, operable with bitwise and unsigned arithmetic operations.
- `int`: 256-bit signed integer, operable with bitwise and signed arithmetic operations.
- `real`: 256-bit signed fixed-point quantity, 127-bit left of the point, 128-bit right.
- `string32`: zero-terminated ASCII string of maximum length 32-bytes (256-bit).

In addition to these, there is the intrinsic `address` type used for identifying specific accounts and a bool type for representing two and false:

- `address`: account identifier, similar to a 160-bit hash type.
- `bool`: two-state value.

Most of the time, we'll tend to use `uint` and `hash`; the former if we intend to do arithmetic, the latter when we need to identify or refer to pieces of data, events, &c.

## Literals

## Parameters and Returns

Messages can have arbitrary input and output data. Since functions are our way of expressing how messages should be handled, they need a way of expressing how this data should be utilised. As such, like in Javascript and C, functions may take parameters as input; unlike in Javascript and C, they may so return arbitrary parameters as output. The input parameters, and types, must be named, type followed by name. For example, suppose we want our contract to accept messages that present two integers, we would write something like:

```
contract Simple {
	function(uint _a, uint _b) {
		// do something with _a and _b.
	}
}
```

Parameters are not allowed to be altered; they are so-called *constant* values and as such may not be used on the left-hand side of an expression.

Returning information through the message is equally simple using the same syntax with the `returns` keyword. For example, suppose we wished to return two results: the sum and the product of the two given integers, then we would write:

```
contract Simple {
	function(uint _a, uint _b) returns (uint o_sum, uint o_product) {
		o_sum = _a + _b;
		o_product = _a * _b;
	}
}
```

Return parameters are always reset to zero; if they are not explicitly set, then they 

## Arithmetic & Flow-control

There are a number of 

On-hand, we also have the Keccak, aka SHA-3 function. So, 


### Fields

Contracts may have data menver

### Constant

## Methods

### Constant

## The Constructor

## The Environment

# More Complex Typing

## Contracts & Forward Declarations

## Arrays

There is also a special type 

- `byte[]`: dynamic-length packed byte array.

## Mappings

## Structs and Enums

# Advanced Solidity

## Visibility

- Public
- Private
- External

## Inheritance

## Function Modifiers

## Logging

## Calling conventions

# The Solidity Library

### owned

### multiowned

### mortal

### named

### daylimit