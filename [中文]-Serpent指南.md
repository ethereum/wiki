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