---
name: Serpent 3.0
category: 
---

The following is a suggested spec for Serpent 3.0.


1. Everything is strongly typed, but types are inferred, so there is no need to specify types explicitly.

For example, consider the code:

    data bar[6]

    def foo():
        x = 5
        y = [1,2,3,4,5,6]
        z = "123123124"
        w1 = y[1]
        w2 = z[2] 
        self.bar = y
        return(x)

A script would run through the code and assign types:

    x: int
    y: arr[int]
    z: string
    w1: int
    w2: int
    self.bar: arr[int] :: storage

When checking the type for w1, the script would see the line `w1 = y[1]`, note that `y` is already known as an `arr[int]` so `y[1]` will be recognized as `int`. Meanwhile, `z` will be known as `string`, and so `z[1]` will be recognized as a character, ie. `int`. Note that this means that `y[1]` will be compiled as `(mload (add y 32))` whereas `z[2]` will be compiled as `(byte (mload (add z 2)) 0)`. `self.bar = y` will be processed recursively so as to copy the entire contents of `y` into `self.bar`.