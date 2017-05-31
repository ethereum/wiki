RLP(Recursive Length Prefix, 递归长度前缀编码)，是Ethereum中对象序列化的一个主要的编码方式，其目的是对任意嵌套的二进制数据的序列(List)进行编码。RLP的目的仅仅是编码一些数据结构，而像string，int，float这些特定的原子数据类型就留给了更高阶的编码协议。在以太坊中，整形必须用没有前导0的大端格式编码(因此整数0等价于一个空字符串)。
如果要编码一个字典，推荐使用两种规范的编码格式——一是通过key的字典序来组织字典[[k1,v1],[k2,v2]……]，另一种是以太坊中使用的高级[Patricia Tree](https://github.com/ethereum/wiki/wiki/Patricia-Tree)。


### 定义

RLP编码只有一个输入(item). 一个item 定义如下:

* 一个字符串(或者bytes)是一个item
* 一个item的列表(List) 是一个item

例如一个空的string是一个item，同样一个单词“cat”也是一个item。包含任意个string的列表(例如，["cat",["puppy","cow"],"horse",[[]],"pig",[""],"sheep"])也是一个item。要特别注意,下文中提到的字符串（string）等价于二进制字节数组（char[]），没有任何编码信息。

RLP编码定义如下:

* 对于值在[0x00,0x7f]范围内的单字节(ascii表定义的字符)，其RLP编码就是其自身。
*否则，如果一个string的长度是0-55字节，那么他的RLP编码是在string开头加一个字节，这个字节的值是\x80加上string的长度，该字节范围为[\x80, \xb7]。
* 如果一个string的长度大于55，那么RLP编码是在string开头加一个字节，这个字节的值等于\xb7加上string长度的二进制编码的字节长度，然后后面跟着string的长度。比如一个长度为1024字节的string，其长度位1024=\x04\x00，长度为2个字节，因此RLP编码头字节的值为\xb7+\x02=\xb9，跟着\x04\x00。String的RLP编码即\xb9\x04\x00加上该string。第一个字节的范围为[0xb8,0xbf]
* 如果一个列表的总的payload(应该是它包含的所有item的编码后长度的和)的长度为0-55，那么list的RLP编码在其item的RLP编码的串联前加上一个字节，这个字节的值是0xc0加上列表的长度(item经过RLP编码后串联记起来的长度)。比如RLP（[“cat”，“dog”]）= [ 0xc8, 0x83, ‘c‘, ‘a‘, ‘t‘, 0x83, ‘d‘, ‘o‘, ‘g‘ ]。所以头字节的范围为[0xc0,0xf7]
* 如果一个list的payload的长度大于55，其RLP编码是list的item的RLP编码的串联，前面加上一个表示payload长度的字节，前面再加上一个payload长度的二进制表示的字节长度。

直接上代码，更简洁

```python
def rlp_encode(input):
    if isinstance(input,str):
        if len(input) == 1 and ord(input) < 128: return input
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

### 举例

 "dog" = [ 0x83, 'd', 'o', 'g' ]

 [ "cat", "dog" ] = `[ 0xc8, 0x83, 'c', 'a', 't', 0x83, 'd', 'o', 'g' ]`

空字符串 ('null') = `[ 0x80 ]`

空列表 = `[ 0xc0 ]`

编码后的整数15 ('\x0f') = `[ 0x0f ]`

编码后的 1024 ('\x04\x00') = `[ 0x82, 0x04, 0x00 ]`

The [set theoretical representation](http://en.wikipedia.org/wiki/Set-theoretic_definition_of_natural_numbers) of three, `[ [], [[]], [ [], [[]] ] ] = [ 0xc7, 0xc0, 0xc1, 0xc0, 0xc3, 0xc0, 0xc1, 0xc0 ]`

字符串 "Lorem ipsum dolor sit amet, consectetur adipisicing elit" = `[ 0xb8, 0x38, 'L', 'o', 'r', 'e', 'm', ' ', ... , 'e', 'l', 'i', 't' ]`