# Foreword

Solidity is perhaps the first example of a contract-oriented programming language. A language with a vocabulary and grammar specifically to help express agreements that are designed, in part, to encode ideas and relationships relevant to Real Life, or some formal model thereof. As such we see notions such as ownership, identity, protections and restrictions forming a core part of the language.

Solidity is a language designed with a specific operating environment, or more formally, state-transitioning machine, in mind, specifically that in which the Ethereum Virtual Machine runs and which is encoded by the Ethereum world state.

We see language grammar actually tieing in with many of the aspects of this: the `event` primitives along with the `indexed` keyword explicitly address the logging environment which Ethereum provides. The variadic return values mimic the fact that output data of Ethereum's calling mechanism is, like the input data, an arbitrary byte array.

# Basic Contract Anatomy

## Hello, World!

```
contract HelloWorld {
  function() returns(string32 _r) {
    _r = "Hello, World!";
  }
}
```

## Functions

## Simple Types

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