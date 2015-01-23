# Foreword

Solidity is perhaps the first example of a contract-oriented programming language. While closely related to object-oriented languages, this is a language designed specifically to help express agreements that must encode ideas and relationships relevant to Real Life, or some formal model thereof. As such we see notions such as ownership, identity, protections and restrictions forming a core part of the vocabulary and idiomatic grammar.

Solidity is designed with a specific operating environment, or more formally, state-transitioning machine, in mind, specifically that in which the Ethereum Virtual Machine runs and which is encoded by the Ethereum world state. I generally refer to this as the Ethereum State Platform, or ESP.

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

## Functions

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