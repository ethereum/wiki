---
name: Ethereum Development Tutorial
category:
---

　このページの目的はコントラクトや分散アプリケーションを作成するために、開発の観点から理解する必要があるEthereumの基礎を紹介することです。Ethereumの全般的なイントロダクションについてはホワイトペーパー、完全な技術仕様についてはイエローペーパーが参考になりますが、このページを読むのに必須ではありません。アプリケーション開発者にとって、このページがそれらに代わるEthereumへの導入となり得ます。

### Introduction

　Ethereumは、ブロックチェーンを利用した分散型アプリケーション（DApps）を簡単にプログラムすることを目的としたプラットフォームです。DAppsとは、ユーザーに対し特定の目的のためを果たすアプリケーションですが、アプリケーション自体は既存の特定の管理者に依存しないという重要な特性を持っています。DAppsは、特定の管理者のサービスを提供するためのフロントエンドとして機能するのではなく、仲介業者なしで人や組織がやり取りするのためのツールです。

　このDAppsというものは、特定のプラットフォームを提供し売り込むための開発環境というよりかはむしろ、（売り手と買い手などといった）違った立場にいる人間や機関が、中央集約型の媒介を無しにして取引、契約等（相互利用）をするための道具である。フィルタリングや第三者認証、紛争解決、個人認証業務のような典型的な「中央集約型の媒介」の業務でさえ、分散型ネットワークで扱うことが可能であり、参加者全員にオープンとなっており、内部トークンシステムとして評判の高いシステムをツールとして採用し、利用者に高品質なサービスを提供する。初期のDAppsの例としては、ファイルシェアアプリとしてBitTorrentが、通貨アプリとしてビットコインが含まれる。Ethereumは　BittorentやBitcoinで使われたものや、P2Pネットワークやブロックチェーン技術を含む最初の進化版開発物（web3.0)といえよう。そして、開発者がこれらの技術を使いやすいように一般化したものだ。

　Ethereumのブロックチェーンは（ビットコインに見られる単なる金銭記録データではなく）プログラミング言語が組み込まれたブロックチェーンというふうに説明できる。あるいは、大衆の思惑による決定（コンセンサス）を基板とした地球規模の仮想マシンとも言える。Ethereum仮想マシン（EVM）を参照してえられる部分的なプロトコルは、内部状態と計算を実際に処理する。実用的な観点からして、EVMは膨大な量のアカウント（というオブジェクト）を内部に保持する分散型コンピュータと考えることができ、このコンピュータでは各々のオブジェクトがそれぞれ内部コードとストレージという名前の３２バイトのkey/valueデータベースを保持し、オブジェクトは他のオブジェクトを意のままに呼び出すことができる。
　〜アカウントには2つのタイプがある〜
　オブジェクトの外側の世界からEVMを使用してアクセスすることができるアカウント（オブジェクト）として「外部所有アカウント（EOA,EOアカウント）」という名の特別なタイプがある。あるEOアカウントから他のどんなアカウントに対する「メッセージ」も、そのEOアカウントの秘密鍵で署名されたトランザクションを送ることが引き金となり、送ることが可能だ。EOアカウントでないものは「 契約、contract 」と呼ばれ、「メッセージ」の受け取り、自動的に内部コードを実行し、（自身のもつ内部ストレージに読み書きする能力を持つ。）受け取った「メッセージ」のストレージを読み取り、そして他の契約アカウントにメッセージを送る。（自分自身にも送ることができる。）　ひとつの捉え方として、contract とは、契約を遂行するためのプログラミングコードだ。

　contract はふつう、４つの目的　に分類できる。

1. データを維持する。
他の契約アカウントや外の世界のアカウントにとって役立つ何かを表すデータ貯蔵を維持する。一例として、通貨をシミュレートした「 contract 」がある。他の例としては、ある特定の機関の構成員を記録する「 contract 」がある。

2. 「 EOアカウント 」に保持される。
「 contract 」よりも複雑なアクセス制御がなされる「 EOアカウント 」に保持される。このコントラクトは「 先渡契約 forwarding contract 」と呼ばれる。これの典型的なものとして、「ある条件が整えば受信した「メッセージ」を送りたい場所へただ単に転送する」というものがある。例として「3つある秘密鍵のうち2つが、ある今送りたいメッセージが確約（コンファーム）するのを待ってから、メッセージを転送する」といった先渡契約をもつものが考えられる（「 multisig 」）。より複雑な先渡契約は送られたメッセージの形態に基づいて違った状態を取るというもので、この機能の最もシンプルな例として、より複雑なアクセス手順によって上書きされうる「引き出し制限」がある。（つまり複雑な手順の親クラス・あるいはインターフェースとしてEOアカウントに保持される）

3. ユーザー間の契約内容や関係性を管理する。
　金融契約における「第3者認証」が例としてわかりやすい。

4. ライブラリとして機能する。
　他の契約によって呼び出される関数を格納しておく。

　contract は、互いに「 呼出（calling）」や「 送信（sending message）」などと呼ばれる動作を通じて作用しあう。一通の「メッセージ」は、1.ある量のether　2.どんなサイズのデータを格納したバイト型配列　3.送受信者双方のアドレス　を保持する。契約アカウントが、返り値としてあるデータを返す「メッセージ」を受信し、その返り値をメッセージの送り手がすぐに使用できるようなときは、「送信」とは、まさに、プログラミングにおける関数の履行である。

　以上に示したように、契約アカウントは異なる働きをし、それら同士が互いに作用しあうことが期待できる。例をあげよう。次のような状況を考えてみよう。
　アリスとボブが、「来年サンフランシスコの気温が35度を超えることはない」ということに１００Gavcoin（開発者の名前が付けられた仮想通貨）の賭けをしたとしよう。しかし、アリスは危険回避思考の持ち主で、アリスの第一アカウントは、マルチシグをかけた「前進型契約」を保持している。ボブは量子暗号に猜疑的で（疑い深く）、伝統的な楕円曲線アルゴリズムに沿ったランポート署名を施したメッセージを転送する「前進型契約」を保持している（訳略）。この賭博を管理する契約アカウントはそれ自身サンフランシスコの気象データをあるコントラクトから呼び出してこないといけない。（以後、契約アカウントの呼び名として、プログラマであるわれわれがmethodのことをメソッドと呼ぶように、コントラクトと呼ぶことにする。コンストラクタ等と混同しないように別名がほしいところだが。）またGavCoinコントラクトともやりとりしなくてはならない。・・・
　以下に図を示す。

![img](https://github.com/ethereumbuilders/GitBook/blob/master/en/vitalik-diagrams/contract_relationship.png)

　スタック上に積み上がっていくメソッドの呼び出し（コールスタック）を頭に思い描くことができれば、
ここでの説明は容易であろう。

ボブが賭けを終了させたいと思った時、次のことが起こる:

1. トランザクションが送信され、それが有効になると、ボブの EOA からボブの「 forwading contract 」へ message が送信される
2. ボブの「 forwading contract 」は message を受け取り、hash と ランポート署名 を「 ランポート署名検証 Library contract 」へ送信する
3. 「 ランポート署名検証 Library contract 」はボブがSHA256のランポート署名が欲しいことをみて、 SHA256 のライブラリを検証に必要な回数にわたって呼び続ける。
4. 「 ランポート署名検証 Library contract 」が 1 を返すと、署名が有効であると署名し、message を「 bet contract 」に送信する
5. 「 bet contract 」は 「 サンフランシスコの気温を提供する Library contract 」 に値を要求する
6. 「 bet contract 」は返事をみて、値が 35 度以上であることを確認する。そこで「 Gavcoin contract 」に、Gavcoin を その account から ボブの「 forwarding contract 」に移動するよう message を送信する。

GavCoin はすべて Gavcion contract データベースの項目として "貯蔵" されていることに注意して欲しい。
ステップ 6 の内容にある単語 "account" が意味するところは単に Gavcoin cocntract の中に、bet contract の address のための key と 残高 とともに、データ項目 があるということだ。
この message を受信したあと、Gavcoin contract は自身の残高からある量の値を減らし、同じ分だけボブの forwarding contract への送信項目の値を増やす。以下のdiagramにこれらのステップを示した:

![img](https://raw.githubusercontent.com/ethereumbuilders/GitBook/master/en/vitalik-diagrams/contract_relationship2.png?1)

### State Machine

　Ethereum仮想マシンの計算処理は、ビットコインスクリプトと伝統的アセンブラやLispといったものの架け橋である、スタックベースのバイトコード言語を使用する。（Lispは再帰的コントラクト呼出の機能の役割を担う。）仮想マシン上のプログラムは以下のようなopcodesのシーケンスである。

    PUSH1 0 CALLDATALOAD SLOAD NOT PUSH1 9 JUMPI STOP PUSH1 32 CALLDATALOAD PUSH1 0 CALLDATALOAD SSTORE

　この例が示すコントラクトの目的は、レジストリという名前をもつ contract として振る舞うことにある。誰でも64バイトのデータを含む「メッセージ」送付することができる。 32バイトは鍵keyに 32バイトは値valueにわり振られる。このレジストリcontractは、鍵がすでにストレージ内に登録されているかをチェックし、もし登録されていなければ、その鍵と共に値を登録する。

　実行中は、「メモリ」と呼ばれる無限拡張可能なバイト型配列、現在の指令の位置を指し示す「program counter」、および３２バイトの値を格納したスタックが維持されている。実行の開始時、メモリとスタックは空で、PCは０である。さて、それでは、一番最初にアクセスされるこのコードと、123weiと、(値54,鍵2020202020) の64バイトのデータともに、contract についてみていこう。


初期状態は：

    PC: 0 STACK: [] MEM: [], STORAGE: {}

最初の命令は`PUSH1`で次の値である0をスタックにpushする。`PUSH1`は１バイトの値を扱う。よって以下のような状態に変わる：

    PC: 2 STACK: [0] MEM: [], STORAGE: {}

２番目の命令である`CALLDATALOAD`では、スタックから値を一つ取り出し、そのアドレス先のデータの先頭32バイトのメッセージを取得し、その値をスタックに保存する（ここでは先頭の32ビットが54である）。よって以下のような状態に変わる：

    PC: 3 STACK: [54] MEM: [], STORAGE: {}

次の命令の`SLOAD`はスタックから値を一つ取り出し、contractのstorageの参照先の値をスタックに保存する。今回初めてcontractは使用するため値が入っておらず、値は０が埋められる:

    PC: 4 STACK: [0] MEM: [], STORAGE: {}

`NOT`スタックから値を取り出し、0であれば1を、それ以外は0をスタックに入れる:

    PC: 5 STACK: [1] MEM: [], STORAGE: {}

次は`PUSH1`であるため、9をスタックに保存する:

    PC: 7 STACK: [1, 9] MEM: [], STORAGE: {}

次の`JUMPI`命令では、スタックから２つの値を取り出し、二つ目の値が０でなければ、一つ目の値によって指定された命令へジャンプする。ここでは０でないのでジャンプする。もしストレージ内の５４番目の値が０でなかったならば、スタックの二つ目の値が０になっているだろう。よって、この場合ジャンプできずに動作がここで終了する:

次は`PUSH1`であるため、32をスタックに保存する.

    PC: 11 STACK: [32] MEM: [], STORAGE: {}

次は再び`CALLDATALOAD`である。 スタックの32バイト目から63バイトまで値を取得しスタックに保存する:

    PC: 13 STACK: [2020202020] MEM: [], STORAGE: {}

次は`PUSH1`で0をスタックに保存する:

    PC: 14 STACK: [2020202020, 0] MEM: [], STORAGE: {}

そして再び0-31バイト目のデータを取得してスタックに保存する (なぜメモリに保存させて再利用しないかって？それはメモリからロードをする方が安上がりだからさ):

    PC: 16 STACK: [2020202020, 54] MEM: [], STORAGE: {}

最後は`SSTORE`で、スタックから取り出した１番目の値を、ストレージ内の参照値とした場所へ、二つ目の値を格納する。よって以下の通りになる：

    PC: 17 STACK: [] MEM: [], STORAGE: {54: 2020202020}

　17番目の指令は何も書かれていないので、ここでストップとなり、もし、スタックやメモリ上に何か残っていれば、消えてしまう。しかし、ストレージ内のデータは次回誰かが呼び出すときまで保存される。これと同じようにして、同じ送信者が同じメッセージを再び呼び出したとすると（あるいは別の誰かが、５４番目に3030303030を登録しようと試みたとすると）これは、８番目の指令のところで動作が終了する。

　幸運なことに、低級アセンブラを君が扱う必要はない。
LLLやSerpent、Mutanなどと言った数々の高級言語が存在し、コントラクトをもっと簡単に記述してくれる。これらの言語で書かれたどんな言語もEthereum仮想マシンにコンパイルされ、あなたがEVMバイトコードを含むトランザクション上で呼び出すコントラクトを作成する。

　トランザクションには二つのタイプがある。
「Ether送信トランザクション」と「コントラクト生成トランザクション」だ。
Ether送信トランザクションとは標準的なトランザクションのことで、受信用アドレス、etherの額、バイト型配列のデータと他のパラメータを含み、秘密鍵による署名は送信者のアカウントに関連づけられている。「コントラクト生成トランザクション」は、受信用アドレスが空である点を除いて標準的なトランザクションと似ている。コントラクト生成トランザクションがブロックチェーンに自身を組み込むとき、トランザクションのバイト型配列のデータはEVMコードとして解釈され、EVMの実行による返り値が新しいコントラクトのコードとなる。このように、初期化の間中、ひとつのトランザクションにあることをやってもらうことができる。新しいコントラクトのアドレスは送信アドレスとそのアカウントが以前トランザクションを作成した回数に基づいて決定論的に計算される。（nonceと呼ばれるこの値もまた別のセキュリティの観点から保存される。）このように、上記のnameレジストリを生成するためにブロックチェーン上に載せる必要があるフルコードは以下のとおり：

    PUSH1 16 DUP PUSH1 12 PUSH1 0 CODECOPY PUSH1 0 RETURN STOP PUSH1 0 CALLDATALOAD SLOAD NOT PUSH1 9 JUMPI STOP PUSH1 32 CALLDATALOAD PUSH1 0 CALLDATALOAD SSTORE

　このopcodesを読み解く上での鍵はCODECOPYだ。今スタック上には[16,12,0]の順に積まれていて、コード配列の要素１２（１３番目）からスタートして、１６バイト分をコピーしたものをメモリの０番地を起点としたところに貼り付け、今貼り付けられたメモリ上の０番目から１６番目の値を返す。
結果として、１２（含まず）〜２８番目のコードがこの一つのコードにより実行される。

### Gas

　仮想マシンを動かす上で重要な側面がある。それは「仮想マシン内部で処理されるあらゆる単一操作は、実際はすべてのノード上で同時に実行される」ということだ。Ethereum１．０の必要なコンポーネントであり、「仮想マシン上のどんなコントラクトもほぼコスト０で他のあらゆるコントラクトを呼び出すことができる」といった恩恵をもたしてくれる。しかしながら、Ethereum仮想マシン上での計算処理のステップはとても高価なもとなる。Ethereum仮想マシンの使用方法の概要は、「１９９９年代以降のスマホでできないことは、ここでもできない。」とでもとらえておけばだいたい正しい。
ビジネスロジックを走らせたり、署名やその他の暗号オブジェクトの有効化をしているEVMの正しい使用法として；EVMの使用法の上限としてあるのが、他のブロックチェーンの部分集合を有効化するアプリケーションだ。（例：分散型ether - bitcoin 両替所） 受け入れがたい使い道としては、ファイルストレージやEmailテキスト、メールシステム、画像処理インターフェースを用いて行うプログラムや遺伝的アルゴリズムや機械学習、グラフ探索などのcloud computingにベストマッチするアプリケーションなどだ。

　故意による攻撃や破壊を予防するため、Ethereumプロトコルは、一計算ステップあたりの使用料を徴収する。料金は市場に基づくものの、いざ動かしてみると強制力をともわすような設計がなされている。；ブロックひとつあたりに蓄えられるオペレーション数の流動的上限があることによって、ほぼコストなしでトランザクションをインクルードすることのできる（ブロックを生成することのできる）マイナー（採掘者）でさえネットワーク全体へのトランザクションのコストと同額の使用料が課せられる。手数料とブロックのオペレーション上限のシステムの経済基礎知識についての詳細は、Ethereum白書の「fee」の項目を参照。

　手数料が実際にどう動くかをいかに述べる。あらゆるトランザクションは、他のデータとともに、ガス価格とガス開始値を含む。ガス開始値とはガスの量であり、トランザクション自身がそれを確約する。ガス価格とはトランザクションが支払うことになる単位ガス料金だ。；このようにして、トランサクションが送られると、ガスがあるとき最初に行われるのが、送信者のアカウントのバランスから、トランザクションの値と、（ガス価格☓ガス開始値）[wei]を足したものを差し引く。ガス価格は送信者によりセットされるが、低すぎるガス価格のトランザクションの処理は、採掘者が拒否をする可能性が高いだろう。

　ガスはざっくりといえば、計算ステップのカウンターとして考えることができ、トランザクションの実行中存在するもので、実行が終われば消えてなくなる。トランザクションの実行が始まった時、今あるガスは（ガス開始値 - ５００ - ５ ☓ トランザクションデータ長※）としてセットされる。全計算ステップにおいて、あるガス量（ふつう１で、時々オペレーションに依存して値が大きくなる）が全体から差し引かれていく。もしガスが０に達すれば、すべての実行は元に戻るがトランザクションは有効で送信者はガスの料金を支払わなければならない。もし、トランザクションの実行が無事終了し、Nだけガスがのこっていたら、N×ガス価格が払い戻されるというわけだ。



ーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーー

※　トランザクションデータ長　トランザクションのデータにあるバイトの数のこと。

　コントラクトによるコントラクト呼び出しという場合にも同様のことが起こる。
（つまりトランザクションと送信作業、コントラクト呼び出しは同義で、それが人の手書きによるものなのか、コントラクトによる自動実行なのかという違いに尽きる。）
　コントラクト呼び出しの際、途中でガスが尽きると、処理は元に戻るだけで、処理が遂行すれば返り値を返し、このどちらかの状態しかありえないので、完全に安全に呼び出し処理ができるのである。
サブのコントラクトが余力を残していれば、親の実行まで戻る。

### Virtual machine opcodes

The opcodes in the EVM are as follows:

* `0x00`: `STOP`, stops execution
* `0x01`: `ADD`, pops 2 values `a`, `b` from the top of the stack and pushes `a+b` to the top of the stack (all arithmetic is modulo 2<sup>256</sup>)
* `0x02`: `MUL`, pops 2 values `a`, `b`, pushes `a*b`
* `0x03`: `SUB`, pops 2 values `a`, `b`, pushes `a-b` (`a` is the value immediately at the top of the stack before execution, `b` is second from top)
* `0x04`: `DIV`, pops 2 values `a`, `b`, pushes `a/b` if `b != 0` else 0
* `0x05`: `SDIV`, pops 2 values `a`, `b`, pushes `a/b` if `b != 0` else 0, except where `a` and `b` are treated as signed integers, ie. if `a >= 2^255` then it's treated as the negative value `a - 2^256`. Division with negative numbers is done as in Python, ie. `25 / 3 = 8`, `25 / -3 = -9`, `-25 / 3 = -9`, `-25 / -3 = 8`
* `0x06`: `MOD`, pops 2 values `a`, `b`, pushes `a%b` if `b != 0` else 0
* `0x07`: `SMOD`, pops 2 values `a`, `b`, pushes `a%b` if `b != 0` else 0, treating `a`, `b` as signed values. Modulo with negative numbers is done as in Python, ie. `25 % 3 = 1`, `25 % -3 = -2`, `-25 % 3 = 2`, `-25 % -3 = -1`
* `0x08`: `EXP`, pops 2 values `a`, `b`, pushes `a^b`
* `0x09`: `NEG`, pops 1 value `a`, pushes `-a` (ie. `2^256 - a`)
* `0x0a`: `LT`, pops 2 values `a`, `b`, pushes 1 if `a < b` else 0
* `0x0b`: `GT`, pops 2 values `a`, `b`, pushes 1 if `a > b` else 0
* `0x0c`: `SLT`, pops 2 values `a`, `b`, pushes 1 if `a < b` else 0, doing a signed comparison
* `0x0d`: `SGT`, pops 2 values `a`, `b`, pushes 1 if `a > b` else 0, doing a signed comparison
* `0x0e`: `EQ`, pops 2 values `a`, `b`, pushes 1 if `a == b` else 0
* `0x0f`: `NOT`, pops 1 value `a`, pushes 1 if `a = 0` else 0
* `0x10`: `AND`, pops 2 values `a`, `b`, pushes the bitwise and of `a` and `b`
* `0x11`: `OR`, pops 2 values `a`, `b`, pushes the bitwise or of `a` and `b`
* `0x12`: `XOR`, pops 2 values `a`, `b`, pushes the bitwise xor of `a` and `b`
* `0x13`: `BYTE`, pops 2 values `a`, `b`, pushes the `a`th byte of `b` (zero if `a >= 32`)
* `0x20`: `SHA3`, pops 2 values `a`, `b`, pushes `SHA3(memory[a: a+b])`
* `0x30`: `ADDRESS`, pushes the contract address
* `0x31`: `BALANCE`, pushes the contract balance
* `0x32`: `ORIGIN`, pushes the original sending account of the transaction that led to the current message (ie. the account that pays for the gas)
* `0x33`: `CALLER`, pushes the sender of the current message
* `0x34`: `CALLVALUE`, pushes the ether value sent with the current message
* `0x35`: `CALLDATALOAD`, pops 1 value `a`, pushes `msgdata[a: a + 32]` where `msgdata` is the message data. All out-of-bounds bytes are assumed to be zero
* `0x36`: `CALLDATASIZE`, pushes `len(msgdata)`
* `0x37`: `CALLDATACOPY`, pops 3 values `a`, `b`, `c`, copies `msgdata[b: b+c]` to `memory[a: a+c]`
* `0x38`: `CODESIZE`, pushes `len(code)` where `code` is the contract's code
* `0x39`: `CODECOPY`, pops 3 values `a`, `b`, `c`, copies `code[b: b+c]` to `memory[a: a+c]`
* `0x3a`: `GASPRICE`, pushes the `GASPRICE` of the current transaction
* `0x40`: `PREVHASH`, pushes the hash of the previous block
* `0x41`: `COINBASE`, pushes the coinbase (ie. miner's address) of the current block
* `0x42`: `TIMESTAMP`, pushes the timestamp of the current block
* `0x43`: `NUMBER`, pushes the number of the current block
* `0x44`: `DIFFICULTY`, pushes the difficulty of the current block
* `0x45`: `GASLIMIT`, pushes the gas limit of the current block
* `0x50`: `POP`, pops one value from the stack
* `0x51`: `DUP`, pops one value `a` from the stack and pushes `a` twice
* `0x52`: `SWAP`, pops two values `a` and `b` and pushes them in reverse order
* `0x53`: `MLOAD`, pops one value `a` and pushes `memory[a: a + 32]`
* `0x54`: `MSTORE`, pops two values `a`, `b` and sets `memory[a: a + 32] = b`
* `0x55`: `MSTORE8`, pops two values `a`, `b` and sets `memory[a] = b % 256`
* `0x56`: `SLOAD`, pops one value `a` and pushes `storage[a]`
* `0x57`: `SSTORE`, pops two values `a`, `b` and sets `storage[a] = b`
* `0x58`: `JUMP`, pops one values `a`, and sets `PC = a` where `PC` is the program counter
* `0x59`: `JUMPI`, pops two values `a`, `b` and sets `PC = a` if `b != 0`
* `0x5a`: `PC`, pushes the program counter
* `0x5b`: `MSIZE`, pushes `len(memory)`
* `0x5c`: `GAS`, pushes the amount of gas remaining (before executing this operation)
* `0x60` - `0x7f`: `PUSH1` - `PUSH32`, `PUSH_k` pushes a value corresponding to the next `k` bytes in the code, and sets `PC += k + 1` (ie. to the byte immediately after the `k` bytes pushed)
* `0xf0`: `CREATE`, pops three values `a`, `b`, `c`, creates a new contract with initialization code `memory[b: b+c]` and endowment (ie. initial ether sent) `a`, and pushes the value of the contract
* `0xf1`: `CALL`, pops seven values `a`, `b`, `c`, `d`, `e`, `f`, `g`, and sends a message to address `b` with `a` gas and `c` ether and data `memory[d: d+e]`. Output is saved to `memory[f: f+g]`, right-padding with zero bytes if the output length is less than `g` bytes. If execution did not run out of gas pushes 1, otherwise pushes 0.
* `0xf2`: `RETURN`, pops two values `a`, `b`, and stops execution, returning `memory[a: a + b]`
* `0xff`: `SUICIDE`, pops one value `a`, sends all remaining ether to that address, returns and flags the contract for deletion as soon as transaction execution ends

Note that high-level languages will often have their own wrappers for these opcodes, sometimes with very different interfaces.

### Ethereum ブロックチェーンの基本

　Ethereum・ブロックチェーン(あるいは「帳簿」）は分散型で（中央集約型ではなく）、全アカウントのカレントステイト（現在の状態）を保持し、大規模に複製されたデータベースである。このブロックチェーンは全アカウントの状態を貯蔵するのに、[「パトリシア木」](https://github.com/ethereum/wiki/wiki/%5BEnglish%5D-Patricia-Tree) というデータ構造を採用している。この「パトリシア木」は本質的に、一般的なKey/value貯蔵時にふるまう「マークル木」の特殊型である。標準のマークル木のように、パトリシア木はルートハッシュを保持している。ルートハッシュは、木構造全体を参照するために使用可能で、木のコンテンツはルートハッシュの変更なしに修正不可能である。それぞれのアカウントのために、木は、[    account_nonce,     ether_balance, code_hash,     storage_root     ]　をふくんだ、４つの構成要素からなるタップル（４-tuple)を貯蔵している。ここで、このタップルの要素の定義は以下の通りである。

* account_nonce：アカウントから送られたトランザクション数
* ether_balance  ：アカウントの残金
* code_hash　　：アカウントの種類がコントラクト->コードのハッシュ値
　　　　　　　　アカウントの種類がEOアカウント->""
* storage_root 　：アカウントの保持するストレージ内を格納した
　　　　　　　　新たなパトリシア木データ構造のルート

![img](https://raw.githubusercontent.com/ethereumbuilders/GitBook/master/en/vitalik-diagrams/chaindiag.png)

　毎分、マイナーは新しいブロックを生成し、（Ethereumのマイニングの概念はビットコインにおける概念と全く同じである。より情報が必要ならばそこらのビットコインのチュートリアルを見るとよい。）新しいブロックは、「一番最近生成されたブロック以降に生成され、かつパトリシア木が、これらのトランザクションを一つのブロックとして適用しマイナーに報酬を与えたのち、そのルートのハッシュ値を新しい状態（状態木）にしてから、新たに生成されたトランザクションのリスト」を含んでいる。

　パトリシア木の仕組みのおかげで、もし少ししかない変更がなされれば、木の大部分は変更前の最後の状態と全く同じになるだろう。このように、新しいパトリシア木のノードが単に、古いパトリシア木と新しいパトリシア木が全く同じである空間における古い方のノードを貯蔵する同じメモリアドレスを指し戻すことができるだろうような場合には、同じデータを二度蓄える必要はない。N番目のブロックとN+１番目のブロックの間に（N＋１番目のブロックに取り込まれるべき）、千箇所の変更があったとして、たとえ全パトリシア木のデータサイズが何ギガバイトに及ぼうとも、N＋１番目のブロックに貯蔵されるのに必要な新しいデータ量はたかだか数百キロバイトであり、基本的にもっと少ない（同じコントラクト内で変化が生じたときは特に）。全てのブロックは、ブロック数、タイムスタンプ、マイナーのアドレス、ガスの上限のような他のデータと同様にして、一つ前のブロックのハッシュ値を含んでいる。

### Graphical Interfaces

　コントラクトはそれ自身とてもパワフルなものだが、Dアプリを完全にするものとは言えない。Dアプリはむしろ、コントラクトとそのコントラクトを使用するグラフィカルインターフェースの融合によるものである。（これは、現バージョンのEthereumのことを述べており、Ethereumの未来バージョンでは、whisperといった、Dアプリ内のノードに対して、ブロックチェーンなしで互いにp２pメッセージを送らせることのできるプロトコルを含むだろう。）いまちょうど、そのインターフェイスは、HTML/CSS/JSのウェブページとして実装され、特別な  javascript  API がEthereumブロックチェーンと一緒に動作する eth オブジェクト形式の中にある。javascript APIのキーとなる部分は以下の通りである。

* `eth.transact(from, ethervalue, to, data, gaslimit, gasprice)` - sends a transaction to the desired address from the desired address (note: `from` must be a private key and `to` must be an address in hex form) with the desired parameters
* `(string).pad(n)` - converts a number, encoded as a string, to binary form `n` bytes long
* `eth.gasPrice` - returns the current gas price
* `eth.secretToAddress(key)` - converts a private key into an address
* `eth.storageAt(acct, index)` - returns the desired account's storage entry at the desired index
* `eth.key` - the user's private key
* `eth.watch(acct, index, f)` - calls `f` when the given storage entry of the given account changes

　ethオブジェクトを利用するのに、特別なソースファイルやライブラリは全く要らない。
しかしながら、そうして作られたDアプリはEthereumクライアントの中で開いたときのみ動作するようになり、一般的なwebブラウザ上では動かない。Javascript APIの実践的使用例として[the source code of this webpage](http://gavwood.com/gavcoin.html)を訪れるとよい。
.

### Fine Points To Keep Track Of

See [https://github.com/ethereum/wiki/wiki/Subtleties](https://github.com/ethereum/wiki/wiki/Subtleties)
