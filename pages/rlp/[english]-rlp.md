---
name: RLP
category: 
---

The purpose of RLP (Recursive Length Prefix) is to encode arbitrarily nested arrays of binary data, and RLP is the main encoding method used to serialize objects in Ethereum. The only purpose of RLP is to encode structure; encoding specific atomic data types (eg. strings, ints, floats) is left up to higher-order protocols; in Ethereum integers must be represented in big endian binary form with no leading zeroes (thus making the integer value zero be equivalent to the empty byte array).

If one wishes to use RLP to encode a dictionary, the two suggested canonical forms are to either use `[[k1,v1],[k2,v2]...]` with keys in lexicographic order or to use the higher-level [Patricia Tree](https://github.com/ethereum/wiki/wiki/Patricia-Tree) encoding as Ethereum does.

### Definition

The RLP encoding function takes in an item. An item is defined as follows:

* A string (ie. byte array) is an item
* A list of items is an item

For example, an empty string is an item, as is the string containing the word "cat", a list containing any number of strings, as well as more complex data structures like `["cat",["puppy","cow"],"horse",[[]],"pig",[""],"sheep"]`. Note that in the context of the rest of this article, "string" will be used as a synonym for "a certain number of bytes of binary data"; no special encodings are used and no knowledge about the content of the strings is implied.

RLP encoding is defined as follows:

* For a single byte whose value is in the `[0x00, 0x7f]` range, that byte is its own RLP encoding.
* Otherwise, if a string is 0-55 bytes long, the RLP encoding consists of a single byte with value **0x80** plus the length of the string followed by the string. The range of the first byte is thus `[0x80, 0xb7]`.
* If a string is more than 55 bytes long, the RLP encoding consists of a single byte with value **0xb7** plus the length of the length of the string in binary form, followed by the length of the string, followed by the string. For example, a length-1024 string would be encoded as `\xb9\x04\x00` followed by the string. The range of the first byte is thus `[0xb8, 0xbf]`.
* If the total payload of a list (i.e. the combined length of all its items) is 0-55 bytes long, the RLP encoding consists of a single byte with value **0xc0** plus the length of the list followed by the concatenation of the RLP encodings of the items. The range of the first byte is thus `[0xc0, 0xf7]`.
* If the total payload of a list is more than 55 bytes long, the RLP encoding consists of a single byte with value **0xf7** plus the length of the length of the payload in binary form, followed by the length of the payload, followed by the concatenation of the RLP encodings of the items. The range of the first byte is thus `[0xf8, 0xff]`.

In code, this is:

```python
def rlp_encode(input):
    if isinstance(input,str):
        if len(input) == 1 and chr(input) < 128: return input
        else: return encode_length(len(input),128) + input
    elif isinstance(input,list):
        output = ''
        for item in input: output += rlp_encode(item)
        return encode_length(len(output),192) + output

def encode_length(L,offset):
    if L < 56:
         return chr(L + offset)
    elif L < 256**8:
         BL = to_binary(L)
         return chr(len(BL) + offset + 55) + BL
    else:
         raise Exception("input too long")

def to_binary(x):
    return '' if x == 0 else to_binary(int(x / 256)) + chr(x % 256)
```

### Examples

The string "dog" = [ 0x83, 'd', 'o', 'g' ]

The list [ "cat", "dog" ] = `[ 0xc8, 0x83, 'c', 'a', 't', 0x83, 'd', 'o', 'g' ]`

The empty string ('null') = `[ 0x80 ]`

The empty list = `[ 0xc0 ]`

The encoded integer 15 ('\x0f') = `[ 0x0f ]`

The encoded integer 1024 ('\x04\x00') = `[ 0x82, 0x04, 0x00 ]`

The [set theoretical representation](http://en.wikipedia.org/wiki/Set-theoretic_definition_of_natural_numbers) of two, `[ [], [[]], [ [], [[]] ] ] = [ 0xc7, 0xc0, 0xc1, 0xc0, 0xc3, 0xc0, 0xc1, 0xc0 ]`

The string "Lorem ipsum dolor sit amet, consectetur adipisicing elit" = `[ 0xb8, 0x38, 'L', 'o', 'r', 'e', 'm', ' ', ... , 'e', 'l', 'i', 't' ]`
