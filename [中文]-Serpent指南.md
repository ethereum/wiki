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

### 另一个实例：名称注册合约 (Name Registry)

读者一定觉得在区块链上跑一个”乘以2“的函数略显无聊吧... 接下来让我们来实现一个更有趣的合约：名称注册。这个合约的主要接口是一个`register(key, value)`函数。通过这个接口可以检查给定的key是否已经被使用/注册了；如果没有，则将这个key注册为指定的键值然后返回1；如果已经被使用则直接返回0。这个合约还提供另外一个接口用来获取指定key对应的键值。下面就是合约代码：

    def register(key, value):
        # Key not yet claimed
        if not self.storage[key]:
            self.storage[key] = value
            return(1)
        else:
            return(0)  # Key already claimed

    def ask(key):
        return(self.storage[key])

这份合约首先定义了`register`函数，这个函数接受`key`和`value`变量作为参数。第二行是注释，以`#`开头，注释会被编译器忽略，仅仅用来帮助程序员更好的理解程序。然后是一个标准的if/else条件语句：先检查`self.storage[key]`是否为0(即表明这个key没有被使用)，如果是0则执行赋值`self.storage[key] = value`然后返回1,否则直接返回0。`self.storage`是一个伪数组，有和数组类似的行为但是实际上没有一块内存与之对应。

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

如果我们想要把上面的第一次调用写成交易的形式：

```
> serpent encode_abi register ii 0x67656f726765 45
d66d6c10000000000000000000000000000000000000000000000000000067656f726765000000000000000000000000000000000000000000000000000000000000002d
```

### 引用代码和调用其他合约

当你的项目越来越大时把所有代码放在一个文件里面就很不科学了，所幸的是把合约代码分开保存在多个文件中是一件很容易的事情。假设我们创建了如下两个文件：

mul2.se:

    def double(x):
        return(x * 2)

returnten.se:

    extern mul2: [double]
    
    MUL2 = create('mul2.se')
    def returnten():
        return(MUL2.double(5, as=mul2))

现在打开Python，执行以下代码：

    > from pyethereum import tester as t
    > s = t.state()
    > c = s.abi_contract('returnten.se')
    > c.returnten()
    [10]

通过这个例子我们可以看到一些新特性：

* 使用`create`方法可以从文件中读入代码创建合约
* 使用`extern`关键字可以声明一个合约类(class of contract)，通过这个类可以知道合约中的函数名
* 用来调用其他合约的编程接口

`create`方法很容易理解 - 使用它可以创建一份合约，返回值是这份合约的地址（address）。`extern`后面先是声明了合约类的名字，例子中是`mul2`,然后用一个数组声明了这个合约类中的所有方法名，例子中是`[double]`。声明之后，对于任何地址变量x（变量的值是一个地址），都可以通过`x.double(arg1, as=mul2)`来调用该地址中保存的合约。合约类通过`as`关键字提供（合约类用来确定`double`对应的函数ID；另一个合约类中如果`double`函数可能对应不同的ID）。`as`关键字是可选的，但是在两个合约类包含同名函数时会产生歧义。`arg1`是传给`double`函数的参数，如果传入的参数个数比函数声明需要的参数少，缺少的参数会以'0'补上；如果传入的参数比声明需要的参数多，多余的参数会被忽略。函数调用还支持一些其他的可选参数：

* `gas=12414` - 设置函数执行可用的gas上限 （默认是所有gas，即合约可用gas上限）。
* `value=10^19` - 随调用消息转账10^19 wei(10 ether)
* `data=x`, `datasz=5` - 用数组x中的头5个值作为参数调用函数；之前传入的参数会被覆盖掉；只指定data而不指定datasz会导致非法调用。
* `outsz=7` - Serpent在默认情况下取运算结果的头32字节作为函数返回值，通过`outsz`可以要求函数通过数组返回更多的值。??? 例如执行`y = x.fun(arg1, outsz=7)`之后你可以通过`y[0]`, `y[1]`等等操作返回值。

和`create`类似的一个操作是`inset('filename')`，它的作用是用文件中的代码替换调用的那一行，而不是创建一个合约。

### 持久化保存结构化数据

（注：原文用词是data structure，直译应为数据结构，但例子中定义的更像是数据而不是数据结构，因此翻译为结构化数据。???）

在更复杂的场景中，我们经常需要将结构化数据持久的保存。例如一份去中心话的交易所合约，既需要保存用户各种资金的余额，也需要保存待成交的包含了价格和数量信息的买单和卖单。因此Serpent内建支持自定义数据结构。以去中心化交易所合约为例，其中可能需要定义这样的结构化数据：

    data user_balances[][]
    data orders[](buys[](user, price, quantity), sells[](user, price, quantity))

接下来可以使用这些定义好的数据：

    def fill_buy_order(currency, order_id):
        # Available amount buyer is willing to buy
        q = self.orders[currency].buys[order_id].quantity
        # My balance in the currency
        bal = self.user_balances[msg.sender][currency]
        # The buyer
        buyer = self.orders[currency].buys[order_id].user
        if q > 0:
            # The amount we can actually trade
            amount = min(q, bal)
            # Trade the currency against the base currency
            self.user_balances[msg.sender][currency] -= amount
            self.user_balances[buyer][currency] += amount
            self.user_balances[msg.sender][0] += amount * self.orders[currency].buys[order_id].price
            self.user_balances[buyer][0] -= amount * self.orders[currency].buys[order_id].price
            # Reduce the remaining quantity on the order
            self.orders[currency].buys[order_id].quantity -= amount

请注意我们是如何先在顶部定义结构化数据，然后在合约中使用它们的。对这些结构化数据的读和写都会被转化成在持久化存储上的读写，因此这些结构化数据是持久保存的。

定义结构化数据的语法很简单。首先可以定义最简单的变量：???

    data blah

    x = self.blah
    self.blah = x + 1

还可以定义定长数组和不定长数组：???

    data blah[1243]
    data blaz[]

    x = self.blah[505]
    y = self.blaz[3**160]
    self.blah[125] = x + y

我们应该优先考虑使用定长数组，因为在计算数组元素(array index)对应的存储位置(storage index)的时候，定长数组消耗的gas更少。

也可以定义元组(tuple)，元组的元素也可以是结构化数据：

    data body(head(eyes[2], nose, mouth), arms[2], legs[2])

    x = self.body.head.nose
    y = self.body.arms[1]

然后是元组数组(array of tuples)：

    data bodies[100](head(eyes[2], nose, mouth), arms[2](fingers[5], elbow), legs[2])

    x = self.bodies[45].head.eyes[1]
    y = self.bodies[x].arms[1].fingers[3]

注意下面这样的写法是不行的：

    data body(head(eyes[2], nose, mouth), arms[2], legs[2])

    x = self.body.head
    y = x.eyes[0]

也就是说对结构化数据内部元素的存取必须在一行语句中完成。

还是以名称注册表（name registry）为例，让我们来看看具体如何使用结构化数据。我们要实现一个增强版的名称注册表，当用户注册key成功时他会成为key的所有者，key的所有者可以 (1) 转移所有权，以及(2) 改变key对应的键值(value)。为了简洁函数不再返回1或者0。

    data registry[](owner, value)

    def register(key):
        # Key not yet claimed
        if not self.registry[key].owner:
            self.registry[key].owner = msg.sender

    def transfer_ownership(key, new_owner):
        if self.registry[key].owner == msg.sender:
            self.registry[key].owner = new_owner

    def set_value(key, new_value):
        if self.registry[key].owner == msg.sender:
            self.registry[key].value = new_value

    def ask(key):
        return([self.registry[key].owner, self.registry[key].value], items=2)

最后定义的ask函数返回一个长度为2的数组。通过调用ask函数，例如`o = registry.ask(key, outsz=2)`，我们可以通过返回值`o[1]`和`o[1]`获取key对应的所有者和键值。