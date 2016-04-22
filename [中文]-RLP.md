---
name: RLP
category: 
---

RLP (递归长度前缀)提供了一种适用于任意二进制数据数组的编码，RLP已经成为以太坊中对对象进行序列化的主要编码方式。 RLP的唯一目标就是解决结构体的编码问题；对原子数据类型（比如，字符串，整数型，浮点型）的编码则交给更高层的协议；以太坊中要求数字必须是一个大端字节序的、没有零占位的存储的格式（也就是说，一个整数0和一个空数组是等同的）。

对于在 RLP 格式中对一个字典数据的编码问题，有两种建议的方式，一种是通过二维数组表达键值对，比如`[[k1,v1],[k2,v2]...]`，并且对键进行字典序排序；另一种方式是通过以太坊文档中提到的高级的[基数树](https://github.com/ethereum/wiki/wiki/Patricia-Tree) 编码来实现。

### 定义

RLP 编码函数接受一个item。定义如下：

* 将一个字符串作为一个item（比如，一个 byte 数组）
* 一组item列表（list）作为一个item

例如，一个空字符串可以是一个item，一个字符串"cat"也可以是一个item，一个含有多个字符串的列表也行，复杂的数据结构也行，比如这样的`["cat",["puppy","cow"],"horse",[[]],"pig",[""],"sheep"]`。注意在本文后续内容中，说"字符串"的意思其实就相当于"一个确定长度的二进制字节信息数据"；而不要假设或考虑关于字符的编码问题。

RLP 编码定义如下：

* 对于 `[0x00, 0x7f]` 范围内的单个字节, RLP 编码内容就是字节内容本身。
* 否则，如果是一个 0-55 字节长的字符串，则RLP编码有一个特别的数值 **0x80** 加上字符串长度，再加上字符串二进制内容。这样，第一个字节的表达范围为 `[0x80, 0xb7]`.
* 如果字符串长度超过 55 个字节，RLP 编码由定值 **0xb7** 加上字符串长度的长度，加上字符串长度长度，加上字符串二进制内容组成。比如，一个长度为 1024 的字符串，将被编码为`\xb9\x04\x00` 后面再加上字符串内容。第一字节的表达范围是`[0xb8, 0xbf]`。
* 如果列表的内容（它的所有项的组合长度）是0-55个字节长，它的RLP编码由0xC0加上所有的项的RLP编码串联起来的长度得到的单个字节，后跟所有的项的RLP编码的串联组成。 第一字节的范围因此是[0xc0, 0xf7]
* 如果列表的内容超过55字节，它的RLP编码由0xC0加上所有的项的RLP编码串联起来的长度的长度得到的单个字节，后跟所有的项的RLP编码串联起来的长度的长度，再后跟所有的项的RLP编码的串联组成。 第一字节的范围因此是[0xf8, 0xff] 。

用Python代码表达以上逻辑:

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

### 例子

字符串 "dog" = [ 0x83, 'd', 'o', 'g' ]

列表 [ "cat", "dog" ] = `[ 0xc8, 0x83, 'c', 'a', 't', 0x83, 'd', 'o', 'g' ]`

空字符串 ('null') = `[ 0x80 ]`

空列表 = `[ 0xc0 ]`

数字15 ('\x0f') = `[ 0x0f ]`

数字 1024 ('\x04\x00') = `[ 0x82, 0x04, 0x00 ]`

The [set theoretical representation](http://en.wikipedia.org/wiki/Set-theoretic_definition_of_natural_numbers) of two, `[ [], [[]], [ [], [[]] ] ] = [ 0xc7, 0xc0, 0xc1, 0xc0, 0xc3, 0xc0, 0xc1, 0xc0 ]`

字符串 "Lorem ipsum dolor sit amet, consectetur adipisicing elit" = `[ 0xb8, 0x38, 'L', 'o', 'r', 'e', 'm', ' ', ... , 'e', 'l', 'i', 't' ]`
