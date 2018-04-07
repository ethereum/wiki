---
name: Solidity Tutorial
category: 
---

- [Cheat Sheet](#cheatsheet)
- [Tips and Tricks](#tips-and-tricks)




Solidity は、構文がJavaScriptとよく似た高級言語で、  
EVM(イーサリアム仮想マシン)上で動くコードへとコンパイルされるために設計されました。  
この項では、Solidityの基礎事項を抑え、EVMの知識を深めます。  
より詳細な事項については[Solidity 特記事項(建設中)]()を参照して下さい。  
また、この項では、非開発者向けの言語で書かれておらず、さらに安定版を確約するものでもありません。  

オンライン・エミュレーターはこちら：  
 [Solidity in your browser](http://chriseth.github.io/cpp-ethereum),  
このリンクはコンパイルのみをサポートしています。  
Contract を実際に走らせたり、Blockchain 上に埋め込んだりするには、  
Alethzero (cpp-client) 等をご使用ください。


## Simple Example

```
contract SimpleStorage {
    uint storedData;
    function set(uint x) {
        storedData = x;
    }
    function get() constant returns (uint retVal) {
        return storedData;
    }
}
```
`uint storedData` は、`uint`型の `storedData`という呼び名の状態変数を宣言します。
(`uint`: unsigned integer of 256 bits)  
この変数のアドレス領域はコンパイラによって自動生成されます。  
`set`関数と`get`関数はこの値を引用したり修正したりするのに使われます。  


## Subcurrency Example


```js
contract Coin {
    address minter;
    mapping (address => uint) balances;

    event Send(address from, address to, uint value);

    function Coin() {
        minter = msg.sender;
    }
    function mint(address owner, uint amount) {
        if (msg.sender != minter) return;
        balances[owner] += amount;
    }
    function send(address receiver, uint amount) {
        if (balances[msg.sender] < amount) return;
        balances[msg.sender] -= amount;
        balances[receiver] += amount;
        Send(msg.sender, receiver, amount);
    }
    function queryBalance(address addr) constant returns (uint balance) {
        return balances[addr];
    }
}
```



このコントラクトでは少し新しい概念が出てきます。
　　  

* まず `address`型です。これは 160 bitの値で、論理数値演算できません。 　　
* 状態変数`balance`を見て欲しいのですが、これは複雑なデータ型から成り立っており、  
難しい言葉で言うと、`address`型から`uint`型への射(写像) ということになります。  
Mapping(写像)はハッシュテーブルのようなもので、自動的に初期化され、  
どんなkeyに対しても、初期値0(byte表記)を与えます。  
* `Coin`という関数を見てみましょう。  
コードをよく見ると、`contract Code`の中に`function Code`が定義されており、  
コンストラクタであることが見て取れます。  
コンスタラクタでありますから当然、あとでこの関数を呼び出すことはできません。  
この例では、コンストラクタにより、contract 作成者のアドレスが永久的に保存されます。 　　
  `tx`、`block`、`msg`はグローバル変数で魔法のように場所を選びません。
この３種類の魔法変数によって保持されるメンバにより contract の外部にアクセスすることが可能です。  
* `queryBalance`関数は`constant`宣言がされており、contractの状態を修正できないようにします。  
（まだ完全に修正不可能というわけではない、ということに注意してください）  
Solidity 言語では、returns のとる引数には名前がついていて、戻り値は基本、ローカル変数を作成します。
なので、ここでは、リターン文を記述しなくとも、 `balance = balances[addr];` と書くだけで、戻り値が返ります。
* `Send` のような event は外部クライアントがブロックチェーンをより効率的に探索できるようにするものです。
`send` 関数の中にあるようにして、　event が呼び起こされると、このことは、永久的にblockchain 上に保存されます。
このことについては、あとでもう少し詳しく述べます。




## Comments


一行コメント (`//`) と 複数行コメント (`/*...*/`) が使えます。
トリプルスラッシュのコメント (`///`) を用いると、[NatSpec](https://github.com/ethereum/wiki/wiki/Natspec-Example)を導入することができますが、ここでは詳しく述べません。




## Types

現段階で開発済みの 基本型 は　
* booleans `bool`
* integer
* 固定長string / byte配列(bytes0 ~ bytes32)  

の３つです。  
integer は  
`int8`/`uint8` から8刻みで、`int256`/`uint256` まで  
`uint`/`int` は `uint256`/`int256` の alias なので、同じ型  
`address` 型は `uint160` から派生した型です。

Comparisons (`<=`, `!=`, `==`, etc.) の影響力の優劣は、booleans ( `&&`, `||` and `!`の組合せ) より常に小さく、
また、`&&` and `||` に対しては、short-circuiting rules (短回路優先の法則) が成立することに注意してください。  
たとえば `(0 < 1 || fun())` では最初の命題が常に真で、fun()関数は永遠に呼ばれません。

もし、演算子が違う型に適用されたら、
コンパイラは暗黙のうちに一方を別の演算子へ変換しようとします (代入演算子に関してもあてはまります)。  
一般論としては、この暗黙変換が適用されるのは、構文がしっかりとしているときで、情報は失われません。:  
`uint8` は `uint16` ・ `int120` ・ `int256` に変換可能ですが `int8` から `uint256` へはできません。  
さらに、unsigned integers は 同等もしくはそれ以上 のサイズの bytes へ変換可ですが、その逆は不可です。  
`uint160` へ変換可能な型は `address` 型への変換可となります。


コンパイラが暗黙変換を許さない状況であっても、  
あなたが何をしているのか把握している状況では、  
例えば以下のような型変換が可能です。

```js
int8 y = -3;
uint x = uint(y);
```

この断片的なソースコードにおける `x` の最終状態は、`0xfffff..fd` (64桁の16進コード) となります。
これは 256 bit の2進数表現における -3 です。

変数の型を常に外部で特定する必要はなく、コンパイラが自動的に、その変数が最初にでてくる代入文から、決定します。

```js
uint20 x = 0x123;
var y = x;
```

ここでは、`y` の型は `uint20` となります。
ただし関数の引数や、戻り値に `var` は使用できません。
integer 型と byte 型は定数として宣言できます。

```js
uint constant x = 32;
bytes3 constant text = "abc";
```



## Integer Literals

integer 式 の型は、式の計算がされたのちに、さいごに決定されます。
次の例を見てみましょう。

```js
var x = 1 - 2;
```
`1 - 2` の値は `-1` です。 これが `x` に代入され、`x` の型は `-1` を含みうる最小の型である `int8` となります。
次にいきましょう。
今度は少し結果が違います。

```js
var one = 1;
var two = 2;
var x = one - two;
```

ここでは、`one` と `two` は `uint8`となり、`x` に対しても同じ型が遺伝します。
`uint8` 型の内部での引き算は、wrapping (`uint8`として処理) されるため、`x` の値は、`255` となります。

integer のみで表された計算式であれば、
一時的には、256bit で表される型の最大の値すら超えることが可能です。

```js
var x = (0xffffffffffffffffffff * 0xffffffffffffffffffff) * 0;
```

ここでは、`x` は `uint` 型の `0` となります。


## Ether と Time の単位

文字としての数値は `wei` , `finney` , `szabo` , `ether` といった単位を取ることができます。
単位を記述しないと、単位は "wei" となります。例を挙げると、 `2 ether == 2000 finney` は `true` を返します。
さらに、 `seconds`, `hours`, `days`, `weeks`, `years`　といった単位が使用でき、　秒が基本単位となります。
（結果として　a year は常にちょうど 365 days を表します。等等）



## Control Structures

C/JapaScript 由来の、大部分の（分岐やジャンプといった）制御構造が Solidity で利用可能です。
ただし、 `switch` と `goto` (Solidity と呼ばれることに注意) は含まれません。
ということで、`if` , `else` , `while` , `for` , `break` , `continue` , `return` といった制御文が使えます。
C言語やJavascriptにみられるような、非boolean型からboolean型への型変換は存在せず、
`if (1) { ... }` は Solidity では無効なものとなります。


## Function Calls

the current contract の関数は直接呼び出すことができ、
また再起呼び出しも可能です。
ナンセンスな事例ですが、次をごらんください。

```js
contract c {
  function g(uint a) returns (uint ret) { return f(); }
  function f() returns (uint ret) { return g(7) + f(); }
}
```

`this.g(8);` という表現も関数呼び出しに有効ですが、
この方法では、関数の呼び出しは message call を介して呼び出されるので、
直接 jump operation 等により呼び出されるものではありません。
他の contract の関数呼び出し時、呼び出しで使用されるガスの単価と量は特定されます。


```js
contract InfoFeed {
  function info() returns (uint ret) { return 42; }
}
contract Consumer {
  InfoFeed feed;
  function setFeed(address addr) { feed = InfoFeed(addr); }
  function callFeed() { feed.info.value(10).gas(800)(); }
}
```
`InfoFeed(addr)` という表現は、
与えられた address 上のコントラクトの型は `InfoFeed` であると宣言し、
外部型への変換を行い、ここにおいてコンストラクタの遂行はしません。

`feed.info.value(10).gas(800)` は関数呼び出しで送られるガスの値と量をローカルでsetするだけであり、
実際の呼び出しは終わりにある括弧が遂行します。


関数呼び出しにおける引数は名前で指定することができ、順番がバラバラでも構いません。

```js
contract c {
function f(uint key, uint value) { ... }
function g() {
  f({value: 2, key: 3});
}
}
```

関数や戻り宣言の仮引数名はなくても構いません。

```js
contract test {
  function func(uint k, uint) returns(uint){
    return k;
  }
}
```



## Special Variables and Functions

デフォルトのグローバル名前空間に存在する
グローバル変数およびグローバル関数を紹介します。


### Block and Transaction Properties

 - `block.coinbase` (`address`): current block miner's address
 - `block.difficulty` (`uint`): current block difficulty
 - `block.gaslimit` (`uint`): current block gaslimit
 - `block.number` (`uint`): current block number
 - `block.blockhash` (`function(uint) returns (bytes32)`): hash of the given block
 - `block.timestamp` (`uint`): current block timestamp
 - `msg.data` (`bytes`): complete calldata
 - `msg.gas` (`uint`): remaining gas
 - `msg.sender` (`address`): sender of the message (current call)
 - `msg.value` (`uint`): number of wei sent with the message
 - `now` (`uint`): current block timestamp (alias for `block.timestamp`)
 - `tx.gasprice` (`uint`): gas price of the transaction
 - `tx.origin` (`address`): sender of the transaction (full call chain)

### Cryptographic Functions

 - `sha3(...) returns (bytes32)`: compute the SHA3 hash of the (tightly packed) arguments
 - `sha256(...) returns (bytes32)`: compute the SHA256 hash of the (tightly packed) arguments
 - `ripemd160(...) returns (bytes20)`: compute RIPEMD of 256 the (tightly packed) arguments
 - `ecrecover(bytes32, byte, bytes32, bytes32) returns (address)`: recover public key from elliptic curve signature

 "tightly packed" ：複数の引数間の余白をなくしひとまとめにできることを意味します。
`sha3("ab", "c") == sha3("abc") == sha3(0x616263) == sha3(6382179) = sha3(97, 98, 99)`。 
もし余白が必要ならば、外部型変換が利用可能です。

注意事項として、プライベート・ブロックチェーン上においては、`sha256` 、`ripemd160` あるいは `ecrecover` はガス欠になりえます。
というのは、コントラクトは最初いわゆるプリ・コンパイルされた contract としてコンパイルされ、コントラクトは最初の message を受信してはじめて存在することとなるからです。（インスタンスが生成されるとも言います。）
存在しない（インスタンスが生成されていない）コントラクトへのメッセージはとても高くつき、このためエラーが（ガス欠が）生じるのです。
この問題への動作対処法ははじめに、（例えば）1 Wei を暗号関数を含む各コントラクトへ送信（初期化）してから、あなたの作成したコントラクト上でそれらのコントラクトを呼び出す形をとります。
テストネット上や、公式のブロックチェーン上では、（初期の段階であらかじめ初期化されているため、）このようなことは起こりません。



### Contract Related

 - `this` (current contract's type): the current contract, explicitly convertible to `address`
 - `suicide(address)`: suicide the current contract, sending its funds to the given address

Furthermore, all functions of the current contract are callable directly including the current function.

## Functions on addresses

It is possible to query the balance of an address using the property `balance`
and to send Ether (in units of wei) to an address using the `send` function:

```js
address x = 0x123;
if (x.balance < 10 && address(this).balance >= 10) x.send(10);
```

Beware that if `x` is a contract address, its code will be executed together with the `send` call (this is a limitation of the EVM and cannot be prevented). If that execution runs out of gas or fails in any way, the Ether transfer will be reverted. In this case, `send` returns `false`.

Furthermore, to interface with contracts that do not adhere to the ABI (like the classic NameReg contract),
the function `call` is provided which takes an arbitrary number of arguments of any type. These arguments are ABI-serialized (i.e. also padded to 32 bytes). One exception is the case where the first argument is encoded to exactly four bytes. In this case, it is not padded to allow the use of function signatures here.

```js
address nameReg = 0x72ba7d8e73fe8eb666ea66babc8116a41bfb10e2;
nameReg.call("register", "MyName");
nameReg.call(bytes4(sha3("fun(uint256)")), a);
```

`call` returns a boolean indicating whether the invoked function terminated (`true`) or caused an EVM exception (`false`). It is not possible to access the actual data returned (for this we would need to know the encoding and size in advance).

In a similar way, the function `callcode` can be used: The difference is that only the code of the given address is used, all other aspects (storage, balance, ...) are taken from the current contract. The purpose of `callcode` is to use library code which is stored in another contract. The user has to ensure that the layout of storage in both contracts is suitable for callcode to be used.

Both `call` and `callcode` are very low-level functions and should only be used as a *last resort* as they break the type-safety of Solidity.

Note that contracts inherit all members of address, so it is possible to query the balance of the
current contract using `this.balance`.

## Order of Evaluation of Expressions

The evaluation order of expressions is not specified (more formally, the order
in which the children of one node in the expression tree are evaluated is not
specified, but they are of course evaluated before the node itself). It is only
guaranteed that statements are executed in order and short-circuiting for
boolean expressions is done.

## Arrays

Both variably and fixed size arrays are supported in state and as parameters of external
functions:

```js
contract ArrayContract {
  uint[2**20] m_aLotOfIntegers;
  bool[2][] m_pairsOfFlags;
  function setAllFlagPairs(bool[2][] newPairs) external {
    // assignment to array replaces the complete array
    m_pairsOfFlags = newPairs;
  }
  function setFlagPair(uint index, bool flagA, bool flagB) {
    // access to a non-existing index will throw an exception
    m_pairsOfFlags[index][0] = flagA;
    m_pairsOfFlags[index][1] = flagB;
  }
  function changeFlagArraySize(uint newSize) {
    // if the new size is smaller, removed array elements will be cleared
    m_pairsOfFlags.length = newSize;
  }
  function clear() {
    // these clear the arrays completely
    delete m_pairsOfFlags;
    delete m_aLotOfIntegers;
    // identical effect here
    m_pairsOfFlags.length = 0;
  }
  bytes m_byteData;
  function byteArrays(bytes data) external {
    // byte arrays ("bytes") are different as they are stored without padding,
    // but can be treated identical to "uint8[]"
    m_byteData = data;
    m_byteData.length += 7;
    m_byteData[3] = 8;
    delete m_byteData[2];
  }
}
```

## Structs

Solidity provides a way to define new types in the form of structs, which is
shown in the following example:

```js
contract CrowdFunding {
  struct Funder {
    address addr;
    uint amount;
  }
  struct Campaign {
    address beneficiary;
    uint fundingGoal;
    uint numFunders;
    uint amount;
    mapping (uint => Funder) funders;
  }
  uint numCampaigns;
  mapping (uint => Campaign) campaigns;
  function newCampaign(address beneficiary, uint goal) returns (uint campaignID) {
    campaignID = numCampaigns++; // campaignID is return variable
    Campaign c = campaigns[campaignID];  // assigns reference
    c.beneficiary = beneficiary;
    c.fundingGoal = goal;
  }
  function contribute(uint campaignID) {
    Campaign c = campaigns[campaignID];
    Funder f = c.funders[c.numFunders++];
    f.addr = msg.sender;
    f.amount = msg.value;
    c.amount += f.amount;
  }
  function checkGoalReached(uint campaignID) returns (bool reached) {
    Campaign c = campaigns[campaignID];
    if (c.amount < c.fundingGoal)
      return false;
    c.beneficiary.send(c.amount);
    c.amount = 0;
    return true;
  }
}
```

The contract does not provide the full functionality of a crowdfunding
contract, but it contains the basic concepts necessary to understand structs.
Struct types can be used as value types for mappings and they can itself
contain mappings (even the struct itself can be the value type of the mapping, although it is not possible to include a struct as is inside of itself). Note how in all the functions, a struct type is assigned to a local variable. This does not copy the struct but only store a reference so that assignments to members of the local variable actually write to the state.

## Assignment

The semantics of assignment are a bit more complicated for non-value types like arrays and structs.
Assigning *to* a state variable always creates an independent copy. On the other hand, assigning to a local variable creates an independent copy only for elementary types, i.e. static types that fit into 32 bytes. If structs or arrays (including `bytes`) are assigned from a state variable to a local variable, the local variable holds a reference to the original state variable. A second assignment to the local variable does not modify the state but only changes the referenc. Assignments to members (or elements) of the local variable *do* change the state.

## Enums

Enums are another way to create a user-defined type in Solidity. They are explicitly convertible
to and from all integer types but implicit conversion is not allowed. The variable of enum type can be declared as constant.

```js
contract test {
    enum ActionChoices { GoLeft, GoRight, GoStraight, SitStill }
    ActionChoices choices;
    ActionChoices constant defaultChoice = ActionChoices.GoStraight;
    function setGoStraight()
    {
        choices = ActionChoices.GoStraight;
    }
    function getChoice() returns (uint)
    {
        return uint(choices);
    }
    function getDefaultChoice() returns (uint)
    {
        return uint(defaultChoice);
    }
}
```

## Exceptions

Currently, there are two situations, where exceptions can happen in Solidity: If you access an array beyond its length (i.e. `x[i]` where `i >= x.length`) or if a function called via a message call does not finish properly (i.e. it runs out of gas or throws an exception itself). In such cases, Solidity will trigger an "invalid jump" and thus cause the EVM to revert all changes made to the state.

It is planned to also throw and catch exceptions manually.

## Interfacing with other Contracts

There are two ways to interface with other contracts: Either call a method of a contract whose address is known or create a new contract. Both uses are shown in the example below. Note that (obviously) the source code of a contract to be created needs to be known, which means that it has to come before the contract that creates it (and cyclic dependencies are not possible since the bytecode of the new contract is actually contained in the bytecode of the creating contract).

```js
contract OwnedToken {
  // TokenCreator is a contract type that is defined below. It is fine to reference it
  // as long as it is not used to create a new contract.
  TokenCreator creator;
  address owner;
  bytes32 name;
  function OwnedToken(bytes32 _name) {
    address nameReg = 0x72ba7d8e73fe8eb666ea66babc8116a41bfb10e2;
    nameReg.call("register", _name);
    owner = msg.sender;
    // We do an explicit type conversion from `address` to `TokenCreator` and assume that the type of
    // the calling contract is TokenCreator, there is no real way to check.
    creator = TokenCreator(msg.sender);
    name = _name;
  }
  function changeName(bytes32 newName) {
    // Only the creator can alter the name -- contracts are explicitly convertible to addresses.
    if (msg.sender == address(creator)) name = newName;
  }
  function transfer(address newOwner) {
    // Only the current owner can transfer the token.
    if (msg.sender != owner) return;
    // We also want to ask the creator if the transfer is fine.
    // Note that this calls a function of the contract defined below.
    // If the call fails (e.g. due to out-of-gas), the execution here stops
    // immediately (the ability to catch this will be added later).
    if (creator.isTokenTransferOK(owner, newOwner))
      owner = newOwner;
  }
}
contract TokenCreator {
  function createToken(bytes32 name) returns (address tokenAddress) {
    // Create a new Token contract and return its address.
    return address(new OwnedToken(name));
  }
  function changeName(address tokenAddress, bytes32 name) {
    // We need an explicit type conversion because contract types are not part of the ABI.
    OwnedToken token = OwnedToken(tokenAddress);
    token.changeName(name);
  }
  function isTokenTransferOK(address currentOwner, address newOwner) returns (bool ok) {
    // Check some arbitrary condition.
    address tokenAddress = msg.sender;
    return (sha3(newOwner) & 0xff) == (bytes20(tokenAddress) & 0xff);
  }
}
```

## Constructor Arguments

A Solidity contract expects constructor arguments after the end of the contract data itself.
This means that you pass the arguments to a contract by putting them after the
compiled bytes as returned by the compiler in the usual ABI format.

## Contract Inheritance

Solidity supports multiple inheritance by copying code including polymorphism.
Details are given in the following example.

```js
contract owned {
    function owned() { owner = msg.sender; }
    address owner;
}

// Use "is" to derive from another contract. Derived contracts can access all members
// including private functions and state variables.
contract mortal is owned {
    function kill() { if (msg.sender == owner) suicide(owner); }
}

// These are only provided to make the interface known to the compiler.
contract Config { function lookup(uint id) returns (address adr) {} }
contract NameReg { function register(bytes32 name) {} function unregister() {} }

// Multiple inheritance is possible. Note that "owned" is also a base class of
// "mortal", yet there is only a single instance of "owned" (as for virtual
// inheritance in C++).
contract named is owned, mortal {
    function named(bytes32 name) {
        address ConfigAddress = 0xd5f9d8d94886e70b06e474c3fb14fd43e2f23970;
        NameReg(Config(ConfigAddress).lookup(1)).register(name);
    }

// Functions can be overridden, both local and message-based function calls take
// these overrides into account.
    function kill() {
        if (msg.sender == owner) {
            address ConfigAddress = 0xd5f9d8d94886e70b06e474c3fb14fd43e2f23970;
            NameReg(Config(ConfigAddress).lookup(1)).unregister();
// It is still possible to call a specific overridden function.
            mortal.kill();
        }
    }
}

// If a constructor takes an argument, it needs to be provided in the header (or modifier-invocation-style at the constructor of the derived contract (see below)).
contract PriceFeed is owned, mortal, named("GoldFeed") {
   function updateInfo(uint newInfo) {
      if (msg.sender == owner) info = newInfo;
   }

   function get() constant returns(uint r) { return info; }

   uint info;
}
```

Note that above, we call `mortal.kill()` to "forward" the destruction request. The way this is done
is problematic, as seen in the following example:
```js
contract mortal is owned {
    function kill() { if (msg.sender == owner) suicide(owner); }
}
contract Base1 is mortal {
    function kill() { /* do cleanup 1 */ mortal.kill(); }
}
contract Base2 is mortal {
    function kill() { /* do cleanup 2 */ mortal.kill(); }
}
contract Final is Base1, Base2 {
}
```

A call to `Final.kill()` will call `Base2.kill` as the most derived override, but this
function will bypass `Base1.kill`, basically because it does not even know about `Base1`.
The way around this is to use `super`:
```js
contract mortal is owned {
    function kill() { if (msg.sender == owner) suicide(owner); }
}
contract Base1 is mortal {
    function kill() { /* do cleanup 1 */ super.kill(); }
}
contract Base2 is mortal {
    function kill() { /* do cleanup 2 */ super.kill(); }
}
contract Final is Base2, Base1 {
}
```

If `Base1` calls a function of `super`, it does not simply call this function on one of its
base contracts, it rather calls this function on the next base contract in the final
inheritance graph, so it will call `Base2.kill()` (note that the final inheritance sequence is
-- starting with the most derived contract: Final, Base1, Base2, mortal, owned). Note that the actual function that
is called when using super is not known in the context of the class where it is used,
although its type is known. This is similar for ordinary virtual method lookup.

### Arguments for Base Constructors

Derived contracts need to provide all arguments needed for the base constructors. This can be done at two places:

```js
contract Base {
  uint x;
  function Base(uint _x) { x = _x; }
}
contract Derived is Base(7) {
  function Derived(uint _y) Base(_y * _y) {
  }
}
```

Either directly in the inheritance list (`is Base(7)`) or in the way a modifier would be invoked as part of the header of the derived constructor (`Base(_y * _y)`). The first way to do it is more convenient if the constructor argument is a constant and defines the behaviour of the contract or describes it. The second way has to be used if the constructor arguments of the base depend on those of the derived contract.


### Multiple Inheritance and Linearization

Languages that allow multiple inheritance have to deal with several problems, one of them being the [Diamond Problem](https://en.wikipedia.org/wiki/Multiple_inheritance#The_diamond_problem). Solidity follows the path of Python and uses "[C3 Linearization](https://en.wikipedia.org/wiki/C3_linearization)" to force a specific order in the DAG of base classes. This results in the desirable property of monotonicity but disallows some inheritance graphs. Especially, the order in which the base classes are given in the `is` directive is important. In the following code, Solidity will give the error "Linearization of inheritance graph impossible".
```js
contract X {}
contract A is X {}
contract C is A, X {}
```
The reason for this is that `C` requests `X` to override `A` (by specifying `A, X` in this order), but `A` itself requests to override `X`, which is a contradiction that cannot be resolved.

A simple rule to remember is to specify the base classes in the order from "most base-like" to "most derived".

## Abstract Contracts

Contract functions can lack an implementation as in the following example (note that the function declaration header is terminated by `;`).
```js
contract feline {
  function utterance() returns (bytes32);
}
```
Such contracts cannot be compiled (even if they contain implemented functions alongside non-implemented functions), but they can be used as base contracts:
```js
contract Cat is feline {
  function utterance() returns (bytes32) { return "miaow"; }
}
```
If a contract inherits from an abstract contract and does not implement all non-implemented functions by overriding, it will itself be abstract.

## Visibility Specifiers

Functions and state variables can be specified as being `public`, `internal` or `private`, where the default for functions is `public` and `internal` for state variables. In addition, functions can also be specified as `external`.

`external`: External functions are part of the contract interface and they can be called from other contracts and via transactions. An external function `f` cannot be called internally (i.e. `f()` does not work, but `this.f()` works). Furthermore, all function parameters are immutable.

`public`: Public functions are part of the contract interface and can be either called internally or via messages. For public state variables, an automatic accessor function (see below) is generated.

`internal`: Those functions and state variables can only be accessed internally, i.e. from within the current contract or contracts deriving from it without using `this`.

`private`: Private functions and state variables are only visible for the contract they are defined in and not in derived contracts.

```js
contract c {
  function f(uint a) private returns (uint b) { return a + 1; }
  function setData(uint a) internal { data = a; }
  uint public data;
}
```

Other contracts can call `c.data()` to retrieve the value of data in state storage, but are not able to call `f`. Contracts derived from `c` can call `setData` to alter the value of `data` (but only in their own state).

## Accessor Functions

The compiler automatically creates accessor functions for all public state variables. The contract given below will have a function called `data` that does not take any arguments and returns a uint, the value of the state variable `data`. The initialization of state variables can be done at declaration.

```js
contract test {
     uint public data = 42;
}
```

The next example is a bit more complex:

```js
contract complex {
  struct Data { uint a; bytes3 b; mapping(uint => uint) map; }
  mapping(uint => mapping(bool => Data[])) public data;
}
```

It will generate a function of the following form:
```js
function data(uint arg1, bool arg2, uint arg3) returns (uint a, bytes3 b)
{
  a = data[arg1][arg2][arg3].a;
  b = data[arg1][arg2][arg3].b;
}
```

Note that the mapping in the struct is omitted because there is no good way to provide the key for the mapping.

## Fallback Functions

A contract can have exactly one unnamed
function. This function cannot have arguments and is executed on a call to the contract if
none of the other functions matches the given function identifier (or if no data was supplied at all).

```js
contract Test {
  function() { x = 1; }
  uint x;
}

contract Caller {
  function callTest(address testAddress) {
    Test(testAddress).call(0xabcdefgh); // hash does not exist
    // results in Test(testAddress).x becoming == 1.
  }
}
```

## Function Modifiers

Modifiers can be used to easily change the behaviour of functions, for example to automatically check a condition prior to executing the function. They are inheritable properties of contracts and may be overridden by derived contracts.

```js
contract owned {
  function owned() { owner = msg.sender; }
  address owner;

  // This contract only defines a modifier but does not use it - it will
  // be used in derived contracts.
  // The function body is inserted where the special symbol "_" in the
  // definition of a modifier appears.
  modifier onlyowner { if (msg.sender == owner) _ }
}
contract mortal is owned {
  // This contract inherits the "onlyowner"-modifier from "owned" and
  // applies it to the "kill"-function, which causes that calls to "kill"
  // only have an effect if they are made by the stored owner.
  function kill() onlyowner {
    suicide(owner);
  }
}
contract priced {
  // Modifiers can receive arguments:
  modifier costs(uint price) { if (msg.value >= price) _ }
}
contract Register is priced, owned {
  mapping (address => bool) registeredAddresses;
  uint price;
  function Register(uint initialPrice) { price = initialPrice; }
  function register() costs(price) {
    registeredAddresses[msg.sender] = true;
  }
  function changePrice(uint _price) onlyowner {
    price = _price;
  }
}
```

Multiple modifiers can be applied to a function by specifying them in a whitespace-separated list and will be evaluated in order. Explicit returns from a modifier or function body immediately leave the whole function, while control flow reaching the end of a function or modifier body continues after the "_" in the preceding modifier. Arbitrary expressions are allowed for modifier arguments and in this context, all symbols visible from the function are visible in the modifier. Symbols introduced in the modifier are not visible in the function (as they might change by overriding).

## Events

Events allow the convenient usage of the EVM logging facilities. Events are inheritable members of contracts. When they are called, they cause the arguments to be stored in the transaction's log. Up to three parameters can receive the attribute `indexed` which will cause the respective arguments to be treated as log topics instead of data. The hash of the signature of the event is one of the topics except you declared the event with `anonymous` specifier. All non-indexed arguments will be stored in the data part of the log. Example:

```js
contract ClientReceipt {
  event Deposit(address indexed _from, bytes32 indexed _id, uint _value);
  function deposit(bytes32 _id) {
    Deposit(msg.sender, _id, msg.value);
  }
}
```
Here, the call to `Deposit` will behave identical to
`log3(msg.value, 0x50cb9fe53daa9737b786ab3646f04d0150dc50ef4e75f59509d83667ad5adb20, sha3(msg.sender), _id);`. Note that the large hex number is equal to the sha3-hash of "Deposit(address,bytes32,uint256)", the event's signature.

### Additional Resources for Understanding Events:

- Javascript documentation: <https://github.com/ethereum/wiki/wiki/JavaScript-API#contract-events>
- Example usage of events: <https://github.com/debris/smart-exchange/blob/master/lib/contracts/SmartExchange.sol>
- How to access them in js: <https://github.com/debris/smart-exchange/blob/master/lib/exchange_transactions.js>

## Layout of State Variables in Storage

Statically-sized variables (everything except mapping and dynamically-sized array types) are laid out contiguously in storage starting from position `0`. Multiple items that need less than 32 bytes are packed into a single storage slot if possible, according to the following rules:

- The first item in a storage slot is stored lower-order aligned.
- Elementary types use only that many bytes that are necessary to store them.
- If an elementary type does not fit the remaining part of a storage slot, it is moved to the next storage slot.
- Structs and array data always start a new slot and occupy whole slots (but items inside a struct or array are packed tightly according to these rules).

The elements of structs and arrays are stored after each other, just as if they were given explicitly.

Due to their unpredictable size, mapping and dynamically-sized array types use a `sha3`
computation to find the starting position of the value or the array data. These starting positions are always full stack slots.

The mapping or the dynamic array itself
occupies an (unfilled) slot in storage at some position `p` according to the above rule (or by
recursively applying this rule for mappings to mappings or arrays of arrays). For a dynamic array, this slot stores the number of elements in the array. For a mapping, the slot is unused (but it is needed so that two equal mappings after each other will use a different hash distribution).
Array data is located at `sha3(p)` and the value corresponding to a mapping key
`k` is located at `sha3(k . p)` where `.` is concatenation. If the value is again a
non-elementary type, the positions are found by adding an offset of `sha3(k . p)`.

So for the following contract snippet:
```js
contract c {
  struct S { uint a; uint b; }
  uint x;
  mapping(uint => mapping(uint => S)) data;
}
```
The position of `data[4][9].b` is at `sha3(uint256(9) . sha3(uint256(4) . uint(256(1))) + 1`.

## Esoteric Features

There are some types in Solidity's type system that have no counterpart in the syntax. One of these types are the types of functions. But still, using `var` it is possible to have local variables of these types:

```js
contract FunctionSelector {
  function select(bool useB, uint x) returns (uint z) {
    var f = a;
    if (useB) f = b;
    return f(x);
  }
  function a(uint x) returns (uint z) {
    return x * x;
  }
  function b(uint x) returns (uint z) {
    return 2 * x;
  }
}
```

Calling `select(false, x)` will compute `x * x` and `select(true, x)` will compute `2 * x`.


## Internals - the Optimizer

The Solidity optimizer operates on assembly, so it can be and also is used by other languages. It splits the sequence of instructions into basic blocks at JUMPs and JUMPDESTs. Inside these blocks, the instructions are analysed and every modification to the stack, to memory or storage is recorded as an expression which consists of an instruction and a list of arguments which are essentially pointers to other expressions. The main idea is now to find expressions that are always equal (on every input) and combine them into an expression class. The optimizer first tries to find each new expression in a list of already known expressions. If this does not work, the expression is simplified according to rules like `constant` + `constant` = `sum_of_constants` or `X` * 1 = `X`. Since this is done recursively, we can also apply the latter rule if the second factor is a more complex expression where we know that it will always evaluate to one. Modifications to storage and memory locations have to erase knowledge about storage and memory locations which are not known to be different: If we first write to location x and then to location y and both are input variables, the second could overwrite the first, so we actually do not know what is stored at x after we wrote to y. On the other hand, if a simplification of the expression x - y evaluates to a non-zero constant, we know that we can keep our knowledge about what is stored at x.

At the end of this process, we know which expressions have to be on the stack in the end and have a list of modifications to memory and storage. This information is stored together with the basic blocks and is used to link them. Furthermore, knowledge about the stack, storage and memory configuration is forwarded to the next block(s). If we know the targets of all JUMP and JUMPI instructions, we can build a complete control flow graph of the program. If there is only one target we do not know (this can happen as in principle, jump targets can be computed from inputs), we have to erase all knowledge about the input state of a block as it can be the target of the unknown JUMP. If a JUMPI is found whose condition evaluates to a constant, it is transformed to an unconditional jump.

As the last step, the code in each block is completely re-generated. A dependency graph is created from the expressions on the stack at the end of the block and every operation that is not part of this graph is essentially dropped. Now code is generated that applies the modifications to memory and storage in the order they were made in the original code (dropping modifications which were found not to be needed) and finally, generates all values that are required to be on the stack in the correct place.

These steps are applied to each basic block and the newly generated code is used as replacement if it is smaller. If a basic block is split at a JUMPI and during the analysis, the condition evaluates to a constant, the JUMPI is replaced depending on the value of the constant, and thus code like
```js
var x = 7;
data[7] = 9;
if (data[x] != x + 2)
  return 2;
else
  return 1;
```
is simplified to code which can also be compiled from
```js
data[7] = 9;
return 1;
```
even though the instructions contained a jump in the beginning.

## Tips and Tricks

 * Use `delete` on arrays to delete all its elements.
 * Use shorter types for struct elements and sort them such that short types are grouped together. This can lower the gas costs as multiple SSTORE operations might be combined into a single (SSTORE costs 5000 or 20000 gas, so this is what you want to optimise). Use the gas price estimator (with optimiser enabled) to check!
 * Make your state variables public - the compiler will create [getters](#accessor-functions) for you for free.
 * If you end up checking conditions on input or state a lot at the beginning of your functions, try using [modifiers](#function-modifiers)

## Cheatsheet

### Global Variables

 - `block.coinbase` (`address`): current block miner's address
 - `block.difficulty` (`uint`): current block difficulty
 - `block.gaslimit` (`uint`): current block gaslimit
 - `block.number` (`uint`): current block number
 - `block.blockhash` (`function(uint) returns (bytes32)`): hash of the given block
 - `block.timestamp` (`uint`): current block timestamp
 - `msg.data` (`bytes`): complete calldata
 - `msg.gas` (`uint`): remaining gas
 - `msg.sender` (`address`): sender of the message (current call)
 - `msg.value` (`uint`): number of wei sent with the message
 - `now` (`uint`): current block timestamp (alias for `block.timestamp`)
 - `tx.gasprice` (`uint`): gas price of the transaction
 - `tx.origin` (`address`): sender of the transaction (full call chain)
 - `sha3(...) returns (bytes32)`: compute the SHA3 hash of the (tightly packed) arguments
 - `sha256(...) returns (bytes32)`: compute the SHA256 hash of the (tightly packed) arguments
 - `ripemd160(...) returns (bytes20)`: compute RIPEMD of 256 the (tightly packed) arguments
 - `ecrecover(bytes32, byte, bytes32, bytes32) returns (address)`: recover public key from elliptic curve signature
 - `this` (current contract's type): the current contract, explicitly convertible to `address`
 - `super`: the contract one level higher in the inheritance hierarchy
 - `suicide(address)`: suicide the current contract, sending its funds to the given address
 - `<address>.balance`: balance of the address in Wei
 - `<address>.send(uint256) returns (bool)`: send given amount of Wei to address, returns `false` on failure.

### Function Visibility Specifiers

```js
function myFunction() <visibility specifier> returns (bool) {
    return true;
}
```

 - `public`: visible externally and internally (creates accessor function for storage/state variables)
 - `private`: only visible in the current contract
 - `external`: only visible externally (only for functions) - i.e. can only be message-called (via `this.fun`)
 - `internal`: only visible internally

### Modifiers

 - `constant` for state variables: Disallows assignment (except initialisation), does not occupy storage slot.
 - `constant` for functions: Disallows modification of state - this is not enforced yet.
 - `anonymous` for events: Does not store event signature as topic.
 - `indexed` for event parameters: Stores the parameter as topic.

### Types

TODO
