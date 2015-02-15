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

我们可以通过pip来安装Serpent的python库和可执行文件:

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