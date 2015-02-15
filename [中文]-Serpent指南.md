有关Serpent 1.0的信息请查阅: https://github.com/ethereum/wiki/wiki/Serpent-1.0-(old)

Serpent是一种用来编写以太坊合约(Ethereum Contract)的高级编程语言。Serpent翻译成中文意思是"大蛇"，如这个名字所示，这是一种与Python类似的编程语言。Serpent在兼顾底层语言效率与良好编程风格的同时尽可能的追求简洁，还加入了一些针对合约编程的特性。Serpent编译器由C++实现，因此可以被轻松打包进任何客户端，最新版本可以在[Github](http://github.com/ethereum/serpent)上找到。

以下内容需要读者对以太坊的工作原理有一个基本认识，包括区块，交易，合约和消息的概念，以及合约是如何通过读入字节数组和输出字节数组工作的。读者可以通过阅读[以太坊开发指南](https://github.com/ethereum/wiki/wiki/Ethereum-Development-Tutorial)得到这些知识。

### Serpent与Python的区别

Serpent与Python之间的主要区别有:

* Python中的数字类型没有大小限制，Serpent的数字类型则会在2<sup>256</sup>溢出。例如，在Serpent中计算`3^(2^254)`的结果是1,虽然事实上这是一个天文数字。
* Serpent没有Decimal类型。
* Serpent没有list comprehensions (例如`[x**2 for x in my_list]`这样的表达式),字典(Hash/Map)，和其它一些高级特性。
* Serpent没有first-class函数的概念。虽然合约中可以定义函数, 合约也可以调用这些函数，但是在两次函数调用之间变量(除了持久变量)是会丢失的。???
* Serpent有一个被称作”持久变量“(persistent storage variables)的概念。
* 在Serpent中可以使用`extern`语句来调用其他合约中定义的函数。

### 安装

我们可以通过pip来安装Serpent的python库和可执行文件 (注：译者使用的Python版本为2.7):

    sudo pip install ethereum-serpent

如果想要一个在C++中可以直接调用的库，可以自行编译Serpent:

    git clone http://github.com/ethereum/serpent
    cd serpent
    make
    sudo make install

### 指南

现在开始编写我们的第一个合约吧！

首先创建一个叫做"mul2.se"的文件，内容如下:

    def double(x):
         return(x * 2)

这是一个非常简单的只有两行代码的合约，它定义了一个函数。一份Serpent合约通过函数向其他合约或者交易(transaction)提供”接口“(interface)，合约定义的函数即可以被交易也可以被其他合约调用。例如一份定义货币的合约可能会定义类似`send(to,value`和`check_balance(address)`的函数。

Additionally, the Pyethereum testing environment that we will be using simply assumes that data input and output are in this format.

现在让我们来编译这份代码。在命令行中输入:

```
> serpent compile mul2.se
604380600b600039604e567c01000000000000000000000000000000000000000000000000000000006000350463eee9720681141560415760043560405260026040510260605260206060f35b505b6000f3
```

执行成功后就得到了一串可以放入交易执行的16进制编码的字符串。如果你想要的是机器指令(opcodes)，可以使用`pretty_compile`来编译：

    > serpent pretty_compile mul2.se
    [PUSH1, 67, DUP1, PUSH1, 11, PUSH1, 0, CODECOPY, PUSH1, 78, JUMP, PUSH29, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, PUSH1, 0, CALLDATALOAD, DIV, PUSH4, 238, 233, 114, 6, DUP2, EQ, ISZERO, PUSH1, 65, JUMPI, PUSH1, 4, CALLDATALOAD, PUSH1, 64, MSTORE, PUSH1, 2, PUSH1, 64, MLOAD, MUL, PUSH1, 96, MSTORE, PUSH1, 32, PUSH1, 96, RETURN, JUMPDEST, POP, JUMPDEST, PUSH1, 0, RETURN]

使用`compile_to_lll`可以查看LLL形式的中间编译结果：

```
> serpent compile_to_lll mul2.se
(return 0 
  (lll 
    (with '__funid 
      (div (calldataload 0) 
        26959946667150639794667015087019630673637144422540572481103610249216
      )
      (unless (iszero (eq (get '__funid) 4008276486)) 
        (seq 
          (set 'x (calldataload 4))
          (seq 
            (set '_temp_521 (mul (get 'x) 2))
            (return (ref '_temp_521) 32)
          )
        )
      )
    )
    0
  )
)
```

让我们来理解一下上面编译结果的含义。与大多数合约一样，代码最外一层的目的仅仅是在合约初始化阶段将合约内部的代码复制并作为运算结果返回。初始化阶段返回的代码将会在每次调用合约的时候被执行。因此在EVM (Ethereum Virtual Machine)执行的代码中你可以看到`CODECOPY`指令, 在LLL表达式中对应的则是`lll`元操作。在`lll`元操作内部，首先是一段获取保存在合约数据头四个字节的函数ID(function ID)的代码（也就是`(div (calldataload 0) 26959946667150639794667015087019630673637144422540572481103610249216)`这一段，我们通过先读入32个字节，再将其除以2^224得到头四个字节）。再往下，则会将获得的函数ID与该合约支持的所有函数ID逐一比较。在我们的例子中，如果函数ID是4008276486，则会将消息数据中的4到35字节的数据赋值给一个名为`'x`的变量（对应的代码是`(calldataload 4)`），然后将`2 * x`的结果赋值给一个临时变量`'_temp_521`，最后将这个临时变量对应的内存地址中存放的32字节长的数据作为结果返回。

函数ID通过计算函数名字和参数的哈希值(Hash)的头四个字节得到。在我们的例子中，定义了一个名字为”double“，接受一个整型数作为参数的函数，因此可以通过如下命令得到其对应的函数ID:

    > serpent get_prefix double i
    4008276486

定义接受不同参数列表的同名函数是允许的。例如，我们可以定义一个接受三个整型数为参数的”double“函数，它会将每一个参数翻倍然后以数组形式返回。这个函数会有一个不同的函数ID (或者说前缀，prefix）：

    > serpent get_prefix double iii
    1142360101

字母`i`代表整型或者定长字符串（32字节，在Serpern和EVM中与整型等价）参数，字母`s`代表变长字符串参数，而字母`a`代表数组参数。后文会对此做更详细的介绍。

那么如何运行这份合约呢？这时候我们需要使用[pyethereum](https://github.com/ethereum/pyethereum)。先通过pip(环境是python 2.7)安装pyethereum:

    > sudo pip install pyethereum

在合约代码所在目录中打开Python控制台，输入：

    > from pyethereum import tester as t
    > s = t.state()
    > c = s.abi_contract('mul2.se')
    > c.double(42)
    [84]

第二行创建了一个初始化的状态(类似于创世块, genesis block)。第三行创建了一个Python对象，代表mul2.se这个合约。你可以用`c.address`来查看这份合约存放的地址。第四行调用这个合约，传入参数42,然后我们看到这份合约返回给我们结果84。

如果要在testnet或者livenet上调用这样一份合约，你需要打包一个包含类似”以整型参数42作为输入调用函数double“这样信息的交易发送给这份合约。命令如下：

    > serpent encode_abi double i 42
    eee97206000000000000000000000000000000000000000000000000000000000000002a

### 另一个实例：名称注册表 (Name Registry)

读者一定觉得在区块链上跑一个”乘以2“的函数略显无聊吧... 接下来让我们来实现一个更有趣的合约：名称注册表。这个合约的主要接口是一个`register(key, value)`函数。通过这个接口可以检查给定的key是否已经被使用/注册了；如果没有，则将这个键值注册为给定的value然后返回1；如果已经被使用则直接返回0。这个合约还提供另外一个接口用来获取指定key对应的value。下面就是合约代码：

    def register(key, value):
        # Key not yet claimed
        if not self.storage[key]:
            self.storage[key] = value
            return(1)
        else:
            return(0)  # Key already claimed

    def ask(key):
        return(self.storage[key])

这份合约首先定义了`register`函数，这个函数接受`key`和`value`变量作为参数。第二行是注释，以`#`开头，注释会被编译器忽略，仅仅用来帮助程序员更好的理解程序。然后是一个标准的if/else条件语句：先检查`self.storage[key]`是否为0(即表明这个key没有被使用)，如果是0则执行赋值`self.storage[key] = value`然后返回1,否则直接返回0（实际上这里返回的是一个数组字面量，所以在Serpent里面可以一次返回多个值）。`self.storage`是一个伪数组，有和数组类似的行为但是实际上没有一块内存与之对应。

让我们把这段代码放入一个名为"namecoin.se"的文件，然后在pyethereum中试试：

    > from pyethereum import tester as t
    > s = t.state()
    > c = s.abi_contract('namecoin.se')
    > c.register(0x67656f726765, 45)
    [1]
    > c.register(0x67656f726765, 20)
    [0]
    > c.register(0x6861727279, 65)
    [1]
    > c.ask(0x6861727279)
    [65]

`funid`是函数的索引，按照函数在合约代码中的字面顺序递增。这里0代表`register`，1代表`ask`。如果我们想要把上面的第一次调用写成交易的形式：

```
> serpent encode_abi register ii 0x67656f726765 45
d66d6c10000000000000000000000000000000000000000000000000000067656f726765000000000000000000000000000000000000000000000000000000000000002d
```
