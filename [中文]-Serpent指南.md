---
name: Serpent
category: 
---

译者: jan

**更多以太坊信息请访问[EthFans.org](http://ethfans.org)**

有关Serpent 1.0的信息请查阅: https://github.com/ethereum/wiki/wiki/Serpent-1.0-(old)

Serpent是一种用来编写以太坊合约(Ethereum Contract)的高级编程语言。Serpent翻译成中文意思是"大蛇"，如这个名字所示，这是一种与Python类似的编程语言。Serpent在兼顾底层语言效率与良好编程风格的同时尽可能的追求简洁，还加入了一些针对合约编程的特性。Serpent编译器由C++实现，因此可以被轻松打包进任何客户端，最新版本可以在[Github](http://github.com/ethereum/serpent)上找到。

以下内容需要读者对以太坊的工作原理有一个基本认识，包括区块，交易，合约和消息的概念，以及合约是如何通过读入字节数组和输出字节数组工作的。读者可以通过阅读[以太坊开发指南](https://github.com/ethereum/wiki/wiki/Ethereum-Development-Tutorial)得到这些知识。

### Serpent与Python的区别

Serpent与Python之间的主要区别有:

* Python中的数字类型没有大小限制，Serpent的数字类型则会在2<sup>256</sup>溢出。例如，在Serpent中计算`3^(2^254)`的结果是1,虽然事实上这是一个天文数字。
* Serpent没有Decimal类型。
* Serpent没有list comprehensions (例如`[x**2 for x in my_list]`这样的表达式),字典(Hash/Map)，和其它一些高级特性。
* Serpent没有first-class函数的概念。虽然合约中可以定义函数, 合约也可以调用这些函数，但是在两次函数调用之间变量(除了持久变量)是会丢失的。
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

这是一个非常简单的只有两行代码的合约，它定义了一个函数。一份Serpent合约通过函数向其他合约或者交易(transaction)提供”接口“(interface)，合约定义的函数即可以被交易也可以被其他合约调用。例如一份定义货币的合约可能会定义类似`send(to,value)`和`check_balance(address)`的函数。

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

如果要在testnet或者livenet上调用这样一份合约，你需要打包一个内容为”以整型参数42作为输入调用函数double“的消息发送给这份合约。使用下面的命令可以得到消息对应的字节码：

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

如果我们想要把上面的第一次调用写成消息的形式：

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
* `outsz=7` - Serpent在默认情况下取运算结果的头32字节作为函数返回值，通过`outsz`可以要求函数通过数组返回更多的值。例如执行`y = x.fun(arg1, outsz=7)`之后你可以通过`y[0]`, `y[1]`等等操作返回值。

和`create`类似的一个操作是`inset('filename')`，它的作用是用文件中的代码替换调用的那一行，而不是创建一个合约。

### 持久化保存结构化数据

（注：原文用词是data structure，直译应为数据结构，但例子中定义的更像是数据而不是数据结构，因此翻译为结构化数据。）

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

定义结构化数据的语法很简单。首先可以定义最简单的变量：

    data blah

    x = self.blah
    self.blah = x + 1

还可以定义定长数组和不定长数组：

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

### 内存中的数组

使用内存中的数组有一套不同的语法。内存数组只能是定长数组，也不能有元组(tuple)或者更复杂的数组元素。例如：

    def bitwise_or(x, y):
        blah = array(1243)
        blah[567] = x
        blah[568] = y
        blah[569] = blah[567] | blah[568]
        return(blah[569])

Serpent还定义了两个常用的数组操作：

    len(x)

用于查询数组的长度。

    slice(x, start, end)

用于截取从start开始到end结束范围内数组元素，注意end必须大于等于start，否则执行是会出错。

### 数组与函数

函数可以接受数组作为参数，也可以返回一个数组：

    def compose(inputs:arr):
        return(inputs[0] + inputs[1] * 10 + inputs[2] * 100)

    def decompose(x):
        return([x % 10, (x % 100) / 10, x / 100]:arr)

在参数定义后面加`:arr`表示这个参数是一个数组；而在返回值后面加`:arr`表示返回一个数组（如果不加`:arr`只写`return([x,y,z])`，函数的返回值将会是这个数组的内存地址）。

当合约调用自己定义的函数的时候，数组参数会被自动识别出来正确处理，例如：

    def compose(inputs:arr, radix):
        return(inputs[0] + inputs[1] * radix + inputs[1] * radix ** 2)

    def main():
        return self.compose([1,2,3,4,5], 100)

但是当合约需要调用另一份接受数组参数的合约时，你需要通过`extern`声明这一点，加入数组签名到函数签名中：

    extern composer: [compose:ai, main]

这里`ai`的意思是“一个数组参数后面跟着一个整型参数”。同理，如果签名是`iiaa`，则代表“两个整型参数后面跟着两个数组参数”，而`iss`表示“一个整型参数，后面是两个字符串做参数”。如果函数名后面没有跟冒号(:)，如本例中`main` 所示，则表示这个函数不接受参数。如果你想知道一个文件中定义了哪些函数签名，执行下面的命令即可：

    > serpent mk_signature compose_test.se
    extern compose_test: [compose:ai, main]

### 字符串

Serpent中有两种字符串：短字符串(short strings)，例如`"george"`，以及长字符串(long strings)，例如`text("afjqwhruqwhurhqkwrhguqwhrkuqwrkqwhwhrugquwrguwegtwetwet")`.用双引号括起来的短字符串和数字类型是等价的；用`text`关键字定义的长字符串则是类似与数组的对象。我们可以使用`getch(str, index)`和`setch(str, index)`方法来操作字符串中的字符。`str[0]`这样的写法则会将字符串当作数组处理，将字符当作数字返回。

将字符串用作函数参数或者返回值时，需要在函数签名中使用字符串标记`str`，类似使用`arr`来标记数组。使用`len(s)`方法可以得到字符串的长度，而`slice(s)`方法可以用来裁剪字符串。

下面是一个用字符串做参数和返回值的例子：

```
data str

def t2():
    self.str = text("01")
    log(data=self.str)
    return(self.str, chars=2)

def runThis():
    s = self.t2(outsz=2)
    log(data=s)
```

### Macros (宏)

**警告：这是新加入的未经测试的特性，大蛇出没注意！**

通过macro我们可以创造重写规则，极大的增强程序的表达能力。例如，假设我们想写一条计算三个数字的中位数的命令：

    macro median($a, $b, $c):
        min(min(max($a, $b), max($a, $c)), max($b, $c))

现在我们可以使用这个新定义的macro：

    x = median(5, 9, 7)

接下来看另一个例子，一个查找数组中最大元素的macro:

    macro maxarray($a:$asz): # search pattern. below is substitution pattern
        $m = 0
        $i = 0
        while i < $asz:
            $m = max($m, $a[i])
            $i += 1
        $m

    x = maxarray([1, 9, 5, 6, 2, 4]:6)

点击下面的链接可以看到更多展示macros牛逼用法的例子：

https://github.com/ethereum/serpent/blob/poc7/examples/peano.se

这里要提醒读者注意的是：macros不是函数(functions)。每次调用macro的时候，macro的定义都会被文本复制到调用的地方（宏展开）。因此当macro的定义变得很长时，请考虑定义一个函数替代它。还要注意的是占位符前面的$符号非常重要：如果$a前少了$符号，实际上是在使用名字为a的变量。我们也可以在替换模式(substitution pattern, 指宏的定义体)而不是搜索模式(search pattern，指宏定义的第一行)中使用$占位符，这样在每一次宏展开时会产生一个名字为随机前缀的变量。在替换模式中也可以使用普通变量(变量名前面没有$)，结果就是所有宏展开使用的是同一个变量，而且该变量可能在宏展开之外的地方也会用到。

### 类型

**警告：这是新加入的未经测试的特性，大蛇出没注意！**

Serpent中的穷人版类型系统是体现macros威力的绝佳例子，和macros一起使用时能产生非常有趣的结果。直接上例子：

    type float: [a, b, c]

    macro float($x) + float($y):
        float($x + $y)

    macro float($x) - float($y):
        float($x - $y)

    macro float($x) * float($y):
        float($x * $y / 2^32)

    macro float($x) / float($y):
        float($x * 2^32 / $y)

    macro unfloat($x):
        $x / 2^32

    macro floatfy($x):
        float($x * 2^32)

    macro float($x) = float($y):
        $x = $y

    macro with(float($x), float($y), $z):
        with($x, $y, $z)

    a = floatfy(25)
    b = a / floatfy(2)
    c = b * b
    return(unfloat(c))

这段代码的返回值是156,即12.5^2的整数部分。如果只用整型计算，结果将是144。这个技巧可以用来重写[椭圆曲线签名的公钥复原代码](https://github.com/ethereum/serpent/blob/df0aa0e1285d7667d4a0cc81b1e11e0abb31fff3/examples/ecc/jacobian_add.se)，类型系统可以让里面的加法和乘法隐式的做模运算(modulo P)，从而是代码更精巧；我们也可以利用[长整形](https://github.com/ethereum/serpent/blob/poc7/examples/long_integer_macros.se)来处理RSA以及其他基于大数的加密算法中的计算。

### 其他

更多的Serpent代码示例请参阅：https://github.com/ethereum/serpent/tree/master/examples

pyethereum tester的一些有用技巧：

* 区块数据 - 你可以通过`s.block`查看区块数据。例如，`s.block.number`, `s.block.get_balance(addr)`, `s.block.get_storage_data(addr, index)`等等。
* 快照 - 通过`x = s.snapshot()`和`s.revert(x)`可以创建和恢复快照。
* 挖矿 - 调用`s.mine(100)`会挖出100个块，块之间间隔60秒。通过`s.mine(100, addr)`可以指定获得挖矿收益的地址。
* Dump所有区块数据 - `s.block.to_dict()`

Serpent还提供了许多特别变量(special variables)，以下是完整列表：

* `tx.origin` - 交易的发送者
* `tx.gas` - 剩余gas
* `tx.gasprice` - 交易包含的gasprice(油价)
* `msg.sender` - 消息的发送者
* `msg.value` - 通过消息转移的wei(以太坊中最小的价值单位）的数量
* `self` - 合约自身的地址
* `self.balance` - 合约自身的余额（拥有多少wei）
* `x.balance` (对于任意的x） - x的账户余额
* `block.coinbase` - 挖出当前块的矿工的收益地址(coinbase)
* `block.timestamp` - 当前块的时间戳
* `block.prevhash` - 前一个块的hash
* `block.difficulty` - 当前的挖矿难度
* `block.number` - 当前块的编号
* `block.gaslimit` - 当前块的可用gas上限

Serpent支持以下特别函数(special functions)：

* `init` - 在创建合约时执行
* `shared` - 在执行`init`和用户函数之前执行
* `any` - 在用户函数之前执行

此外还提供了几个加密操作：

* `addr = ecrecover(h, v, r, s)` - determines the address that produced the elliptic curve signature `v, r, s` of the hash `h`
* `x = sha256(a, items=4)` - 返回数组a中头4个元素构成的128字节的SHA256 hash。
* `x = ripemd160(a, items=4)` - same as above but for ripemd160
* 使用字符语法(chars syntax)可以计算任意数量字节的hash。例如：`x = sha256([0xf1fc122bc7f5d74df2b9441a42a1469500000000000000000000000000000000], chars=16)` - 返回的是头16个字节的hash。注意最后用0对齐，否则头16个字节会是0（因为一个word是32字节）,计算的是16个0字节的hash。

### 小提示

* 如果函数的返回值和你预计的不同，仔细检查所有的变量名 - 使用没有声明过的变量是不会产生错误信息的。

* `Invalid argument count or LLL function`通常表示你将`self.foo()`写成了`foo()`。

* 有时你需要的是无符号运算符，例如div()和lt()而不是'/'和'<'。

* 升级Serpent的时候，需要先`pip uninstall ethereum-serpent`然后`python setup.py install`。避免使用`pip install ethereum-serpent`，因为pypi上的包八成已经过时了。

* 在调用abi_contract()时如果你遇到`Exception: Error (file "main", line 1, char 5): Invalid object member (ie. a foo.bar not mapped to anything)`这样的错误，确认你要编译的文件所在路径写对了。

* 如果在调用abi_contract()的时候遇到core dump，确认你没有定义同名函数。
