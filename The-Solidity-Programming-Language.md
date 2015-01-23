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

The contract is the basic structure of Solidity. It is a prototype of an object which lives on the blockchain. A contract may be instantiated into a contract-account (or 'object', or sometimes just 'account') at which point it gets a uniquely identifying address with which is may be called. The address here is similar to a reference or pointer in C-like languages. As in some object-oriented languages, contracts can never run themselves - they may only be called.

We denote a contract with the `contract` keyword and a name, followed by its definition enclosed in braces (`{` & `}`). Here is the most basic contract:

```
contract MostBasicContract {
}
```

It doesn't do anything. To make it do something then it gets called, we can introduce a function. The function you've already seen is the so-called default function. Why this is default will be explained later, but for now, suffice it to say that it is simply the function that gets called when this contract (well, an instantiation of it, anyway) receives an otherwise unknown message from the Ethereum Environment.

### Fields

Contracts may have data menver

## Methods

## Simple Types

## Simple Arithmetic

## The Constructor

## The Environment

# More Complex Typing

## Constant

## Arrays

## Mappings

## Structs and Enums

# Advanced Solidity

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