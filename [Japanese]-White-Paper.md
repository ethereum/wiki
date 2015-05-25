# Ethereum 白書

### A Next-Generation Smart Contract and Decentralized Application Platform

ナカモトサトシの論文により、2009年に開発された Bitcoin は通貨・貨幣における革新的な発明だと
謳われ、金兌換のような後ろ盾がなく、中央通貨管理局をもたないはじめての デジタル財産 の例です。 
- see [intrinsic value](http://bitcoinmagazine.com/8640/an-exploration-of-intrinsic-value-what-it-is-why-bitcoin-doesnt-have-it-and-why-bitcoin-does-have-it/)　  
しかし、その壮大な Bitcoin の実験における、より特筆すべき重要部は別の所にあります。
それは分散型大衆決定のツールとして、まさにその基礎をなす Blockchain の技術であり、急速に人々の注目を集めつつあります。

一般的に、
blockchain テクノロジーを引用している Bitcoin の代替アプリで、
blockchain 上の電子財産を実装したものとして:

* 一定取引量のある通貨や金融商品をあらわすもの([ colored coins ](https://docs.google.com/a/buterin.com/document/d/1AnkP_cVZTCMLIzw4DvsW6M8Q2JC0lIzrTLuoWu2z1BE/edit))
* 基礎となる物理デバイスの所有権  ([ smart property ](https://en.bitcoin.it/wiki/Smart_Property))
* ドメインのような投資対象外の財産　([ Namecoin ](http://namecoin.org))  


があり、より複雑なアプリケーションとしては以下のものが挙げられます:

* ( 役人や銀行員に取って代わり、)コーディングによってあらゆるルールを実装することで、個々の電子資産を管理しようというもの([ smart contracts ](http://szabo.best.vwh.net/smart_contracts_idea.html))
* 上記のスマートコントラクトを blockchain 上で実装したもの ([ decentralized autonomous organizations (分散型自動組織) ](http://bitcoinmagazine.com/7050/bootstrapping-a-decentralized-autonomous-corporation-part-i/) (DAOs))



Ethereum が提供しようとしているものは、チューリング完全なプログラミング言語の完成品を
 blockchain に埋め込み提供することにあります。
この言語は、"contract" を生成するために使用され、
"contract" とはあらゆる 関数 をプログラムしたものです。
これにより、ユーザーは上記の全てのシステムを実装することが可能で、
われわれがまだ想像すらしていない多くの可能性が、
論理を秘めし数行のコードを書き上げるだけで実現できるようになります。


### 目次

* [歴史](#history)
    * [状態遷移システム としての Bitcoin](#bitcoin-as-a-state-transition-system)
    * [採掘](#mining)
    * [マークル木](#merkle-trees)
    * [Blockchain を用いた代替アプリケーション](#alternative-blockchain-applications)
    * [スクリプト言語による記述](#scripting)
* [Ethereum](#ethereum)
    * [Ethereum アカウント](#ethereum-accounts)
    * [メッセージ と トランザクション](#messages-and-transactions)
    * [Ethereum の 状態遷移関数](#ethereum-state-transition-function)
    * [コード実行](#code-execution)
    * [Blockchain と 採掘](#blockchain-and-mining)
* [アプリケーション](#applications)
    * [証明書発行のシステム](#token-systems)
    * [Financial derivatives](#financial-derivatives-and-stable-value-currencies)
    * [Identity and Reputation Systems](#identity-and-reputation-systems)
    * [Decentralized File Storage](#decentralized-file-storage)
    * [Decentralized Autonomous Organizations](#decentralized-autonomous-organizations)
    * [Further Applications](#further-applications)
* [Miscellanea And Concerns](#miscellanea-and-concerns)
    * [Modified GHOST Implementation](#modified-ghost-implementation)
    * [Fees](#fees)
    * [Computation And Turing-Completeness](#computation-and-turing-completeness)
    * [Currency And Issuance](#currency-and-issuance)
    * [Mining Centralization](#mining-centralization)
    * [Scalability](#scalability)
* [Conclusion](#conclusion)
* [References and Further Reading](#references-and-further-reading)

## Introduction to Bitcoin and Existing Concepts

### 歴史


上述した資産登録マシンのような代替アプリや、
分散型デジタル通貨の概念が現れ始めたのは、ここ数十年です。
80〜90年代にかけて、David Chaum の「ブラインディング署名 blinding sgnature」をよりどころとした
匿名のデジタル通貨プロトコルがたくさん開発され、高いプライバシーをもつ通貨を提供しましたが、
これらは中央集約型の媒体に依存していたため、広く注目を浴びるには至りませんでした。
1998年に発表された、Wei Daiによる [b-money](http://www.weidai.com/bmoney.txt) が、
現行の分散型のコンセンサスと同様の、計算問題を解くことによって
お金を創造するというアイデアを、はじめて導入した事例となります。
しかし、このプロポーザルの詳細は不十分であったため、実用的な分散型の大衆意思決定を実装することができませんでした。
2005年、Hal Finney が、暗号通貨のコンセプトをつくりあげるために、
ABCD Hashcash パズル<sup>[jp-1]</sup> と b-money からアイデアをしぼり作られたシステムである "[reusable proofs of work](http://www.finney.org/~hal/rpow/)" というコンセプトを発表しましたが、バックエンドに、信用のある計算機を使用しなければならなかったため、真に分散型とは呼べず、再び失敗しました。
2009年のナカモトサトシによる実用的な実装がはじめての分散型の通貨となりました。
これは、昔からあった「公開鍵暗号（所有権を管理) 」と
大衆意思決定アルゴリズムである「 "proof of work" (誰がコインを所有しているのか追跡)」
を結びあわせたものとなります。


proof of work の背景にある技術は宇宙史に名を刻むほどの飛躍的進歩でありました。
なぜなら proof of work は、同時に二つの難題を解決したのです。

* ひとつめは、単純明快で適切な影響力をもつ大衆意思決定のアルゴリズムの提供で、
ネットワーク上のノードはBitcoinの帳簿の「一般規則に従う状態更新」ができるようになりました。
* ふたつめは、大衆意思決定のプロセスへの自由参加を可能にするメカニズムの提供で、
誰が、コンセンサスに影響をもたらすのかということを決める政治的問題を解決したのです。

これは次のようにして、参加基準を定式化したということです。

1. ノードに対し、特定リストにおける唯一性の証明書の提出を要求し、
2. さらに「大衆意思決定のプロセスにおける単位ノードの重みは、ノードのもつ計算能力に応じて分配する」
という エコノミックバリア<sup>[jp-2]</sup> を採用する



### 状態遷移システム としての Bitcoin

![statetransition.png](http://vitalik.ca/files/statetransition.png?2)

技術的観点から見て、Bitcoin をはじめとした暗号通貨の帳簿は、全 bitcoin の所有状況をあらわす「状態」と、状態と取引(トランザクション、状態遷移関数のこと)から新たな状態を出力する「状態遷移関数」をもった、「状態遷移」のシステムと見てとれます。
一般的銀行のシステムでは、たとえば「状態」はバランスシートにあたり、
「トランザクション」はAからBにXドル移動してくれ、というリクエストにあたり、
この状態遷移関数は、XドルだけAの口座の残高を減らし、Bの残高を増やします。
もし、Aの口座の残高がXドルに満たなかった場合には、
状態遷移関数はエラーを返します。
このようして、定式化をすると

    APPLY(S,TX) -> S' or ERROR

この銀行システムは以上で定義され、以下は適用例です。

    APPLY({ Alice: $50, Bob: $50 },"send $20 from Alice to Bob") = { Alice: $30, Bob: $70 }  

    APPLY({ Alice: $50, Bob: $50 },"send $70 from Alice to Bob") = ERROR


Bitcoin における「状態 state」とは、全コインの集合 であり、
技術的に説明いたしますと、
発行されているコインのうちで「UTXO（未使用の取引出力値）」の全集合 となり、各 UTXO には、それぞれ「残高」と「所有者」が記録されています。
「所有者」は、基本的に、暗号理論における公開鍵<sup>[1]</sup>である20バイト(160bit)のアドレスとなります。
「トランザクション」は、状態遷移関数であり、一以上の入力値 と 一以上の出力値 をとります。
各入力値は、「既存の UTXO への参照」と「所有者のアドレスと関連付けられた秘密鍵による暗号署名」 から構成され、
各出力値は、「新しく生成された UTXO」を保持しています。

状態遷移関数 `APPLY(S,TX) -> S'` を定義するプログラムの概略は以下となります。:

1. For each input in `TX`:
    * If the referenced UTXO is not in `S`, return an error.
    * If the provided signature does not match the owner of the UTXO, return an error.
2. If the sum of the denominations of all input UTXO is less than the sum of the denominations of all output UTXO, return an error.
3. Return `S` with all input UTXO removed and all output UTXO added.

ひとつめのステップにおける
前半部により、トランザクションの送信者が、存在しないコインを不正に送ることを防止し、
後半部により、トランザクションの送信者が、他人のコインを勝手に送ることを防止します。
ふたつめのステップによって、トータルバリューの保存（入力値の総計 が 出力値の総計 と等しい）が執行されます。
これを実用的な支払いに適用するための、プロトコルは以下のようになります。

アリスがボブに 11.7BTC を送信したいとします。
まずはじめに、アリスは、利用可能な UTXO を自分の持っているものの中からかき集め、
少なくとも総計11.7BTCになるようにします。アリスの UTXO を集めてちょうど11.7BTCをつくることはできず、6+4+2=12 BTCがアリスの得る最小の値です。
そして彼女は、３つの入力値と２つの出力値をもつトランザクションをつくります。
ひとつめの出力値は11.7BTCでボブのアドレスが所有者として記録され、
ふたつめの出力値は0.3BTCの"お釣り"がアリス自身を所有者として記録されます。



### 採掘

![block_picture.jpg](http://vitalik.ca/files/block_picture.png)

もし、アクセス対象として信用取引可能な 中央集約型 のサービスを使っているのであれば、
このシステムの実装は至極簡単なものであったでしょう。
単に上記のプログラムコードを記すのに、中央サーバーのハードディスクを使用し、「状態」を記録・維持すれば済む話であったでしょう。
しかし、わたしたちが Bitcoin を用いてやろうとしているのは、分散型通貨システムの構築です。
なので、トランザクションの順番をみんなが合意できることを確約するために、
状態遷移システム と 大衆意思決定のシステム をくっつけてやらなくてはなりません。
Bitcoin の分散型大衆決定プロセスでは、「ブロック」と呼ばれる「トランザクションを梱包したもの」を作り続けようとする、
ネットワーク上のノードが必要です。 
ネットワークは、だいたい10分毎にひとつの ブロック を生成するように設計されており、
各々のブロックは、

* 「タイムスタンプ」
* 「ノンス」
* 「直前のブロックへの参照値」
* 「（直前のブロック生成後から現在までに遂行された）トランザクションのリスト」

を保持します。（ ※ ノンス：ブロック生成時にインクリメントされ続ける個体識別ハッシュ値で、マイナーがブロックを掘り当てることを目標にして独自にインクリメントする。）
このブロックが時間発展することによって、
Bitcoin の帳簿を最新状態に更新し続ける、
永続的かつ恒久的成長をなす " blockchain " （ブロックの鎖）を生成します。


現パラダイム下において、ブロックが有効かどうかをチェックするアルゴリズムは以下となります：

1. Check if the previous block referenced by the block exists and is valid.
2. Check that the timestamp of the block is greater than that of the previous block<sup>[2]</sup> and less than 2 hours into the future
3. Check that the proof of work on the block is valid.
4. Let `S[0]` be the state at the end of the previous block.
5. Suppose `TX` is the block's transaction list with `n` transactions. For all `i` in `0...n-1`, set `S[i+1] = APPLY(S[i],TX[i])` If any application returns an error, exit and return false.
6. Return true, and register `S[n]` as the state at the end of this block.

基本的に
ブロック内の各トランザクションは、
トランザクション執行前の 過去の状態 をもとにして、有効な状態遷移を提供しなければなりません。
「状態」はいかなる点においても、ブロック内に記述されないことに注意してください。（ブロックは状態遷移関数をつなげ合わせた関数そのものであり、入力値 である「状態」については何も書かれていません）；
（このアルゴリズムは、「検証ノード」を説明する簡単な抽象例であり、
どのブロックの検証においても、開始状態から、全ブロックの全トランザクションを順番に適用することによって、目的となるブロックの示す状態を計算すれば十分となります。）
さらに「採掘者 miner」がトランザクションをブロックに取り込む順番がとても重要だということに注意してください。
（もし、A、Bという二つのトランザクションがあって、BはAの生成した UTXO(未使用出力値) を使う場合において、
AのあとにBがきているブロックは有効ですが、そうでない場合は無効となってしまいます。）


他のシステムでは見受けられない仕様として、
上述のトランザクションリストにおいて「一有効性条件」（有効なものを一つ選ぶための条件）が
 "proof of work" には必要となります。
厳密な定義は、
全てのブロックの double-SHA256 hash値（256bit の数値）が 
動的に変化するように設計された「目的値 target」より小さくなること、であり、
目的値は、これを執筆している当時では、約2<sup>187</sup>でした。
これは、ブロック生成を計算科学上 "難しく" する為であり、
その結果、Sybil Attack（ひとりでノードを多数生成し多数決的に攻撃する手法）による攻撃者が自身の好きなように 全 blockchain を改竄してしまうことを防止いたします。
SHA256（エスエイチエーにごろ）は、完全に予測不可能な擬似乱数関数として設計されており、
有効なブロックをつくる唯一の方法は、単に ノンス をインクリメントしてはその新しい hash値 が適合するかを確かめるという、試行錯誤を繰り返すしかありません。



現在における~2<sup>187</sup>の「目的値」では、
ネットワークは~2<sup>69</sup> 回の試行錯誤をしてやっとブロックを見つけることができます。
ふつう、目的値 は、ネットワーク上で2016ブロック生成される毎に再設定され、
ネットワーク上にあるノードによるブロックの発掘が平均して10分毎に生じるよう調整されます。
採掘者に競わせてこの計算をさせるための設定として、
ブロックを採掘したものは、どこからともなく湧いた自分への25BTCの報酬を、
トランザクションとして最後に付け加えます。
さらに、
全入力値が全出力値よりも大きいような全てのトランザクションにおける、
その差額は「取引手数料 transaction fee」として、採掘者のもとへ行く仕組みです。
ところで、これはBitcoinが発行される唯一のメカニズムとなります。
つまり、初期状態においては、Bitcoin は皆無であったわけです。



マイニングの目的をより深く理解するために、
悪意ある攻撃者のおこす事件によって何がおこるのかを見ていきましょう。
Bitcoin の基礎となる暗号理論はセキュリティの高いものと知られているので、
攻撃者の狙い目としては、直接、暗号理論で守られていない部分 : トランザクションの順序 となるでしょう。

攻撃者の戦略は簡単なものです:

1. ある商売人に 100 BTC をある商品の購入代金として送る (瞬間的な発送ができるデジタル商品が好まれます)
2. 商品の到着を待つ
3. 自分自身に 100 BTC を送る別のトランザクションを生成する
4. ネットワークが、後に作った方のトランザクションの順番が最初にくるようなブロックを、承認するように試みる

一度、ステップ 1 が履行されると
数分後に採掘者がトランザクションをブロックに含めます。
ブロック番号は 270000 とします。
一時間後、５個以上のブロックが、そのブロックの後ろに追加され、
この５つのブロックが、間接的にそのトランザクションを参照しているため、
トランザクションは「承認 confirming」されたということになります。
この時点で、
商売人は、支払いが確定したものとみなし、商品を発送します。
ここではデジタル商品を考え、商品がすぐに届くこととします。
さていま、攻撃者が、別のトランザクションを作成し、自分宛に 100BTC を送るものとします。
攻撃者が、もし単にそれを野に放っただけならば、
そのトランザクションは受理されないでしょう。
法の番人である採掘者は、`APPLY(S,TX)` を実行するとき、`TX` が、使用済みUTXO を使用しようとしていることに気づくでしょう。
なので代わりに、
攻撃者はブロックチェーンを分岐させ、
親として同じ 269999 番目のブロックを参照する 270000 番目の新しいバージョンのブロックを生成します。
ここでは、もとのブロックに含まれていたトランザクションは含まれず、新しいトランザクションが追加されていくこととなります。
ブロックのデータの中身が違うので、
攻撃者は proof of work をやり直す必要があります。
さらに、攻撃者の新しいブロック 270000 では、異なるハッシュ値を生成するので、
もとのブロックチェーン上のブロック 270001 ~ 270005 は、このブロックを参照しません。
このように、もとのブロックチェーンと攻撃者のチェーンは完全に分断されるのです。
このとき適用されるルールは次のようになります。
ブロックチェーンの分岐時は、
一番長いブロックチェーンが "信用" あるものとして選択されます。
なので、攻撃者が新しい 270000 のブロックチェーン上で採掘し続ける傍で、
このシステムの法の番人である採掘者達はもとの 270005 のブロックチェーンを採掘し続けることになります。
攻撃者が、自分のブロックチェーンを最長にするためには、
ネットワーク上の残りのすべてのノードの総和より、高い計算能力を誇る必要があり、
これを「51%攻撃」と呼びます。








### マークル木
  
![SPV in bitcoin](https://raw.githubusercontent.com/ethereum/www/master-postsale/src/extras/gh_wiki/spv_bitcoin.png)


_革新　 : 　
分岐の正当性の証明には、少しのノードを与えてやるだけでよい_

_伝統　 : 　
どの部分にいかなる変化を付与しても、鎖の上方で必ず不一致を生む_



  

Bitcoin の重要なスケーラビリティ特性は「ブロックは多層データ構造で保管される」ということです。
ブロックの「ハッシュ値」とは実は、ブロックヘッダ（先頭部）のハッシュ値 に過ぎず、これは約 200 byte のデータであり、

* タイムスタンプ
* ノンス
* 直前のブロックの ハッシュ値 
* マークル木（ブロック内の全トランザクションを保持するデータ構造）の ルート（根の元となる部分）の ハッシュ値

を保持します。マークル木は、バイナリ木のひとつで、以下の三つから構成されます。

* 基礎データを保持する木構造の最下層の「葉（リーフノード）」の集合
* 二つの 子ノード のハッシュ値である「枝（中間ノード）」の集合 
* 唯一の「根（ルートノード）」（二つの子ノードのハッシュ値で "頂上" にくるもの）

マークル木は、ブロック中のデータをバラバラに運搬するためにつくられました。
ノードは、ひとつのソース（ネットワーク上の自身とは別のノード）からブロックヘッダだけを、
別のソースから、必要なトランザクションに関連する小さな部分木を、ダウンロードすることができ、それでもなお全データの整合性を保証できるのです。
これがうまく動作する所以は、ハッシュ値が上に伝播していくところ です。：
もし悪意のあるユーザーが偽物のトランザクションをマークル木の底のノードと取り替えようとすると、この変化はその親のノードを変化させ、繰り返し伝播することで最終的にルートの値を変化させます。
つまり、ブロックのハッシュ値が変化し、マークル木のプロトコルにより、結果として、全く別のブロックとして記録され、このブロックは十中八九 proof of work が無効となります。


マークル木のプロトコルは、言うまでもなく長期にわたるアプリケーションの維持のために必要です。
Bitcoin ネットワークにおける「完全ノード、フルノード」とは、「全ブロックの全トランザクションを保管・処理するノード」のことで、
2014年4月の時点で 15GB の容量をとり、ひと月あたり１GB以上の速さで増え続けています。
現在、これはデスクトップコンピュータ上で目視できますが、携帯電話では確認できません。
容量的な観点から、後々の未来、完全ノード に参加できるのは、ビジネスや趣味の範疇に限られてくるでしょう。
「SPV （簡素な支払検証）」として知られるプロトコルにより、「完全ノード」とは別タイプのノードが開発されました。
「軽量ノード、ライトノード」と呼ばれ、このノードは、ブロックヘッダをダウンロードし、ブロックヘッダで proof of work を検証し、そして自身に関係のある トランザクションの「枝、ブランチ」だけをダウンロードします。
軽量ノードは、セキュリティを強く保ったまま、トランザクション履歴や残高を、状態遷移関数により決定することができるというのに、
全ブロックチェーンの小さな部分木をダウンロードすればよい、というものなのです。



### Blockchain を用いた代替アプリケーション

基礎技術である blockchain の他コンセプトへの応用は、これもまた、長い歴史があります。
2005 年　Nick Szabo が "[secure property titles with owner authority（自己の権威によるセキュアな財産の獲得）](http://szabo.best.vwh.net/securetitle.html)" というコンセプトを発表しました。この論文は、複製データベースの技術の進歩により、いかにして
blockchain 基調のシステムが、土地所有の登記 の保管を可能にするのかを記述し、
「開拓 homesteading」「不法占有 adverse possesion」「ジョージの土地課税 Georgian land tax」といったコンセプトを含む枠組みを、苦労して築き上げました。
しかし、残念ながら、当時利用できる、効果的な複製データシステムがなかったため、プロトコルが実際に実装されることはありませでした。
とは言うものの、2009 年に Bitcoin の分散型コンセンサス が一度開発されてからは、急速に代替アプリが出現し始めました。


* **Namecoin** - 2010年に作られた [Namecoin](https://namecoin.org/) は「分散型名前登録データベース」と表現されます。
Tor や Bitcoin , BitMessage のような分散型プロトコルでは、個体識別に アカウント が必要で、そのため他人による干渉が可能ですが、
どのようにしても利用可能な識別子は、`1LW79wp5ZBqaHW1jL5TCiBCrhQYtHagUWy`のような擬似乱数となります。
できれば "ジョージ" のような名前をつけることができたらいいな、と考えるでしょう。
しかしながら、問題なのは "ジョージ" という名前を誰でも、同じプロセスをたどることで登録でき、"ジョージ"として振舞えるのです。
唯一の解決策は 「fist-to-file パラダイム」を用いることです。
これは、最初（first）の登録者は登録（file）に成功し、二番目以降では失敗するというものです。
この問題は Bitcoin の大衆意思決定のプロトコルに完全に合致し、 Namecoinは一早くにこの考えを使って名前登録のシステムを実装し、見事に成功しました。

* **Colored coins** - [colored coins](https://docs.google.com/a/buterin.com/document/d/1AnkP_cVZTCMLIzw4DvsW6M8Q2JC0lIzrTLuoWu2z1BE/edit) の目的は、Bitcoin の blockchain 上に「自身で作ったデジタル通貨」や、
通貨の重要な性質である少額使用の例としてユニットを採用した「デジタルトークン」を、構築できるプロトコルを提供することです。
colored coins のプロトコルでは、
特定の Bitcoin UTXO に「色」を設定することで、
新しい通貨を "発行" します。
プロトコルは、colered coins を生成するトランザクションの入力値に「色」が付いていれば、他の UTXO も同じ「色」であるものと再帰的に定義します。
（様々な「色」の入力が混じった場合は、特別なルールが適用されます。）
このことで、ユーザーは特別な色の UTXO だけを保持する財布を維持し、
ほとんど Bitcoin と同じように周囲に対し送金することが可能で、
受け取った UTXO の色を特定するには blockchain を遡ります。

* **Metacoins** - 
metacoins の背景となる思想は「 Bitcoin を土台として、その上で動作するプロトコルをもつ」であり、
metacoins のトランザクションの保管に、Bitcoin のトランザクションを使用しますが、Bitcoin とは別の 状態遷移関数 `APPLY'` を保持します。
metacoins のプロトコルは、無効な metacoins トランザクションが Bitcoin blockchain 上にでてくることを防止するために、
規則「 if `APPLY'(S,TX)` returns an error, 
the protocol defaults to `APPLY'(S,TX) = S
」を加えます。
metacoins は、任意の独自暗号通貨をつくるための 簡単なメカニズム を提供しており、
Bitcoin 自体のシステム内部においては表面化することのない 先進的な独自機能 を持たせることができます。
採掘とネットワークのシステムといった複雑な部分がすでに Bitcoin プロトコルによって処理されているので、
開発コストはとても低く済みます。
metacoins はいくつかの金融契約や名前登録や分散型両替所を実装するのに使われています。


一般的に言って、大衆意思決定プロトコルを構築する方法は二種類あります。
「独自のネットワークをつくる方法」と「Bitcoin を土台とする方法」です。
前者の方法は、namecoin では適度な成功を収めたものの、実装するのが大変です。
と言いますのは、独自の実装はそれぞれにおいて、独自のブロックチェーンをつくる必要があり、
同様に、それに必要な状態遷移とネットワークを構成するコードのあらゆるビルド・アンド・テストが必要となります。
さらに、そうしてできた分散型大衆決定のアプリケーションの数々の集合は、
採掘（power）と検証（law）の分散を招き、仮にアプリケーションの中では多数派であったとしても、
規模が小さすぎて自分のブロックチェーンを公正なものとすることができない、といった事態を招きます。
そして次のことを認識するに至りました。
大きな種類の分散型アプリがあったとして、とりわけ分散型自動組織では、
それらはお互いに手を取り合わなければなりません。

一方で、「Bitcoin を土台とする方法」では、Bitcoin の SPV 特性 を継承しないという欠陥があります。
SPV は Bitcoin では動作しますが、それは blockchain におけるブロックの「深さ」が その正当性 を代弁するためです。
一度、トランザクションの祖先が深いところへ行ってしまえば、
そのトランザクションは、現在の「状態」を構成する正当な状態遷移関数であると、安心して言うことができます。
一方、meta プロトコル では、そのコンテクスト内でトランザクションが無効であっても、
Bitcoin の blockchain において、それが組み込まれることを阻止する方法はありません。
（Bitcoin のコンテクスト と Meta プロトコル のコンテクストは異なります。）
このように、もし、完全にセキュアな SPV meta プロトコルの実装 が存在したならば、
あるトランザクションが有効かどうかを判定するために、
Bitcoin の blockchain の一番最初まで全過程を遡ってスキャンする必要があるでしょう。現在、meta プロトコル の軽量実装は、データを提供する信用機関としてのサーバーに依存しており、
言うまでもなく、とりわけ暗号通貨の当初の目的の一つが「信用機関の必要性の消去」であるような状況下では、最良の結果であるとは到底言えません。


### スクリプリト言語による記述

たとえまったく拡張をせずとも、実は、Bitcoin プロトコルは「 smart contracts 」コンセプトの 機能的に弱いバージョン を簡単に実装したものなのです。
Bitcoin の UTXO は、「公開鍵」に保持されるだけでなく、
簡素なスタック・ベース・プログラミング言語で表現される少し複雑な「スクリプト」が、保持することもできます。
このパラダイムの下で、
トランザクションが、
スクリプト保持の UTXO を 入力値 とすれば、
スクリプトの記述内容を満たすデータが 出力値 となるように、
トランザクションの記述がされなければなりません。
さらには、
基本的な 公開鍵保有メカニズム（その公開鍵を使用したトランザクションがブロック内にあるかどうかを検証するメカニズム）
でさえ、スクリプトを通して実装されています。
そのスクリプトは、
ブロック生成の証である 楕円曲線署名 を入力値として受け取り、
トランザクションとその UTXO を所有するアドレス（公開鍵）に対してその署名を検証し、
検証が成功すれば 1 を返し、そうでなければ 0 を返します。
他にも、より複雑なスクリプトが様々な使用場面のために存在します。
例えば、
トランザクション検証の際、与えられた三つの鍵の組のうち二つの署名を必要とする スクリプト（マルチシグ multisig）や、
企業アカウントや、セキュリティの高い口座アカウント、商売におけるエスクローが必要な状況に役立つ 初期設定 を構築することができます。
スクリプトは、計算問題の答えに対する懸賞金の支払いにおいても使用され、
「もしあなたがこの額の Dogecoin のトランザクションを送信したという SPV proof を提供できるならば、この Bitcoin はあなたのものだ」といったようなことを記述したスクリプトでさえ構築可能です。
そして基本的には、分散型のクロス暗号通貨の取引が可能です。

しかしながら、Bitcoin に実装されたようなスクリプト言語にはいくつかの重要な制限があります。：

* **チューリング完全性の欠如** - 
つまるところ、Bitcoin スクリプト言語 は計算理論の大部分をサポートしていますが、ほぼ全てという訳ではありません。
サポートしていない代表的なものとして ループ が挙げられます。
これは、トランザクションの検証中に無限ループに陥る事を避けるために除外されました。
理論的には、プログラマにとってはこれは簡単に克服できる障害で、if文と一緒に基本コードを繰り返せば、あらゆるループを模倣できますが、
スクリプトを記録する（ブロックチェーン上の）スペースを極めて非効率に使用することになります。
たとえば、楕円曲線署名の代替アルゴリズムをスクリプト上に実装したならば、
全く同じである掛け算の命令セットが256回個別に記述されてしまいます。

* **値が定まらない問題** - 
UTXO を保持する スクリプトが、取引量をきめ細やかに管理する方法はありません。
たとえば、
もしも、神のみぞ知るような内容の契約の大きな取引だっとしたら、それは価格操作等の生じうるヘッジング契約となってしまうでしょう。
それは次のような状況です。
AとBがそれぞれ $1000 相当のBTC をスクリプトに提出し、スクリプトが30日後に$1000相当のBTCをAに残りをBに送るものとします。
30日後の 1 BTC の USドル価格 を決定するのには神託が必要となり、しかし、これは、現在利用可能な完全な中央集約型の方法でさえ、信用とインフラの観点で大きな改良がひつようとなります。
しかしながら、 UTXO は使うか使わないかの２択なので、
神託が提示しうるすべての価格を UTXO で表すには、2進数計算しかなく、そのため極めて非効率な UTXO の"ハッキング"が必要で、様々な残高の UTXO を用意する必要があります。
たとえば、2<sup>k</sup> の値をもつ UTXO を k = 0 ,..., 30 まで 31個 つくれば良いでしょう。 

* **状態の欠如** - UTXO は使うか、使われないかのどちらかを必ず決定してやらなければなりません。 
このため、内部に「状態」を保持する多層形式の契約やスクリプトを記述することができません。
このことにより、多分岐選択可能な契約や、分散型取引のオファー、２段階の暗号理論による決定プロトコル（セキュリティの高い計算問題の賞金を与える場合に必要です）をつくるのはとても困難となってしまいます。
さらに、
UTXO は単純な一度きりの契約をつくることにしか使用できず、分散型組織のような、より複雑な「状態」を保持する契約を記述できず、
meta プロトコルの実装を困難なものとします。
また、値の定まらない2進数状態では、「引き出し制限」が不可能となります。これは重要なアプリケーションであり、大きな弊害であると言えるでしょう。

* **Blockchainが見えない問題** - 
UTXO は、ノンス、タイムスタンプ、直前のブロックのハッシュといった blockchain のデータに対して盲目です。
このことにより、スクリプト言語が、ランダム性の観点で潜在的価値のあるソースを参照するのを防いでしまい、
ギャンブル・アプリケーションや他のカテゴリのいくつかを、厳しく制限してしまうことになります。

このように、
暗号通貨の上に進化型のアプリケーションを構築する方法を３つ見てきました。  
ひとつめは、新しい blockchain を Bitcoin を土台とした上につくり、スクリプト言語を使用し、meta プロトコルを実装する方法です。
ふたつめは、新しい blockchain をつくる方法で、その性質を決定するのに無限の自由が得られますが、
開発時間に関するコスト問題、スタートアップに関する問題とセキュリティー面の問題も同様に得られます。
みっつめは、Bitcoin のスクリプト言語を使用する方法で、開発や一般化は簡単ですが、
可能性が限られてくること、meta プロトコルの実装は簡単ですが、スケーラビリティの欠点に悩むことになります。

Ethereum では、
われわれは、代替となる骨格を築き上げ、
簡単な開発であっても、大きな成果物が得られ、
スマフォのようなライト・クライアントのもつ財産に対しても強固なものを提供し、
同時に、アプリケーションが 経済環境 と blockchain セキュリティ とを共有できるものを提供することを意図しています。


## Ethereum

Ethereum の目的は、分散型アプリケーションのための代替プロトコルを創造し、
大規模な分散型アプリケーションにとって、われわれが非常に役立つだろうと信じるところの、数々の修正を加え提供することであります。
ここにおいて、アプリの高速開発にかかる時間、小規模かつ滅多に使われないアプリに対するセキュリティ、他アプリ間の効率のよい相互作用を可能とすること、が重要視されます。
Ethereum は、基本的に究極の抽象基盤層となるものの構築により、以上の目的を果たします。
究極の抽象基盤層とは、埋め込み型チューリング完全なプログラム言語を伴う blockchain であり、
これにより、所有・トランザクションの形式・状態遷移関数に関する、任意の独自規則を創造することのできる機能を備えた
 smart contracts と 分散型アプリ をだれでも記述することができます。
Namecoin の骨格だけを抜き出した実装は、二行のコードで記述できます。
通貨や評判を管理するシステムは２０行以下で構築可能です。
「 Smart contracts 」と呼ばれる暗号理論で実装された「箱」は、値を保持し、ある条件が整った時にだけ、解錠できます。
この　smart contracts は Ethereum プラットフォーム上に構築可能であり、
チューリング完全性、値が定まる仕様、状態の保持および blockchain への参照が可能となるところから、
Bitcoin スクリプトにはない、広大かつより強力なプラットフォームとなります。


### Ethereum アカウント

Ethereum では、「状態」は、「アカウント」と呼ばれるオブジェクトから作り上げられ、各「アカウント」は、20 byte の アドレス と、アカウント間における値や情報の直接的やりとりである 状態遷移 を保持します。Ethereum アカウントは４つのフィールドを含みます。


* **nonce** 、各トランザクションの処理が一度きりであることを確約するためのカウンター
* アカウントの現在の **ether balance**
* アカウントの **contract code** （もし存在すれば）
* アカウントの **storage** （デフォルトは空）

Ether は、Ethereum における主要な内部暗号燃料であり、トランザクション手数料を支払うために使用されます。
一般的に、アカウントには二つの種類があります。
秘密鍵により管理される**externally owned accounts** と自身のコントラクトコードにより管理される**contract account**です。
EOA (externally owned account) はコードを持たず、EOAからトランザクションを生成し署名することによって メッセージ を送ることができます。contract (contract account) では、メッセージを受信した時はいつも保持コードをアクティベートし、内部ストレージを読み書き可能にし、メッセージを送信するもしくは新しいコントラクトを作る、といった内容のことが順番に実行されます。

Ethereum における contract は履行されるべきあるいは一緒にコンパイルされるべきものというよりかはむしろ、
Ethereum 実行環境を職場とする「自動金融エージェント」といったものに似ており、メッセージやトランザクションによって起動されたときには、いつもある特定のコードを実行し、自身の ether 残高と、なんども使う変数を把握するのに必要な key/value ストレージ を直接管理する権限を持っている、ということに注意してください。


### メッセージ と トランザクション

Ethereum において、「トランザクション」は、
EOA から送られたメッセージを貯蔵する 署名付データパッケージ を参照するために使用されます。 
トランザクションは、以下を含みます。

* メッセージの受領人
* 送信者を特定する署名
* 送信者から受領人へ送られる ether の量
* オプショナルデータフィールド（署名付きデータパッケージ）
* `STARTGAS` 値：トランザクションの実行にかかる 計算のステップ数 の最大値
* `GASPRICE` 値：送信者が支払う、１計算ステップあたりの手数料

最初の３つは、どんな暗号通貨にもある標準的な、トランザクションの「フィールド」です。
４つ目のデータフィールドは、デフォルトでは関数を持ちません。
しかし、Ethereum 仮想マシンは、contract が使用する opcode を保持する必要があり、その際このデータフィールドが使用されます。
opcodeとは、「 contract がデータにアクセスするのに使用する opcode（オペレーションコード）」です。
もし、contract が blockchain 上のドメイン登録サービスとして機能しているならば、
contract は投げられたデータをふたつの「フィールド」を保持するものとして解釈したがることでありましょう。
ひとつめのフィールドは、登録するドメインで、ふたつめのフィールドはIPアドレスです。
コントラクトは、 opcode によって、メッセージに含まれるこれらの値を読み込むことで、適切にストレージの中に配置することが可能となるわけです。

`STARTGAS` と `GASPRICE` のフィールドは Ethereum サービス に対するモデル拒否運動を封じ込める狙いがあります。
偶発的あるいは故意による無限ループや他の計算理論的に無駄なコードの消費を避けるために、
各トランザクションにはそれらが実行するコードの計算ステップ数の上限を設ける必要があります。
計算の基本ユニットは「 gas 」と呼びます。
たいてい、１計算ステップは 1 gas を消費します。
しかし、幾つかの命令では、計算量的に見てより高価であるため、
少しおおきな gas の量が必要とされます。
また、状態の一部として保持しなければならないデータ量が増えたりします。
トランザクションにデータを埋め込む際には、1 byte 毎に 5 gas の「手数料」もかかります。
「手数料」システムの意図するところは、攻撃者に対し、計算資源や帯域、ストレージを含めた、
彼らが消費する全リソースの量に比例した支払いを強要するためです。
このようにして、ネットワークが消費するどんなリソースにも、それが大量消費とつながるような状況を生み出す、
すべてのトランザクションに対して、消費量の増加に比例した gas の支払いを強要することとなります。



### Messages

contract は他の contract に対して「メッセージ」を送信することが可能です。
「メッセージ」はネットワークに対して配信されることがなく、
Ethereum 実行環境内でのみ存在します。メッセージは以下を含みます。

* メッセージの送信者 (implicit)
* メッセージの受信者
* メッセージと一緒に送信されるetherの量
* オプショナルデータフィールド
* `STARTGAS` 値

基本的には「メッセージ」はトランザクションのようなものですが、
contract により生成され、外部での動作はしない、という点で異なります。
メッセージは contract が `CALL` opcode を実行している時に生成され、
この opcode は「メッセージ」を生成し、実行します。
トランザクションのように、
メッセージは、そこに記述されたコードを実行する 受信者のアカウント へと導かれます。
このようにして、contract はEOAのやり方と全く同じ方法で、他の contract と関係性をもつことができます。

ただし、以下のことに注意してください。
トランザクションやコントラクトによって署名された gas の許容値は、そのトランザクションとトランザクション配下の実行ステップにおいて消費される gas の総量に適用されます。
たとえば、もし、外部管理者である A が B に対し、1000 gas と一緒にトランザクションを送信し、B は、C にメッセージを送信する前に 600 gas を消費し、C の内部実行として戻り値を返すまでに 300 gas が消費されたとすると、 B は、「ガス欠」とならないためには、もう 100 gas を使用することが可能です。（ ガス欠 となってしまうとエラーを返し、トランザクションは実行されません。）

### Ethereum の 状態遷移関数

![ethertransition.png](http://vitalik.ca/files/ethertransition.png?1)

Ethereum の 状態遷移関数, `APPLY(S,TX) -> S'` は次のように定義できます:


1. トランンザクションが 「 well-formed 」であるか（例えば、値が正しい数値であるか）チェックし、
署名が有効であれば、ノンスが送信者のアカウントのものと合致するかチェックします。もし、そうでなければ、エラーを返します。
2. トランザクションの手数料を `STARTGAS * GASPRICE` として計算し、署名から送信アドレスを決定します。
送信者のアカウントの残高から手数料を差し引き、送信者のノンスを次の値へとインクリメントします。
もし、残高不足であれば、エラーを返します。
3. `GAS = STARTGAS` として、`GAS` 値を初期化し、トランザクションにおける byteデータ量 のぶんだけ byte あたり一定量の gas を支払います。
4. 送信者のアカウントから受信者のアカウントにトランザクションの値を転送します。もし、受信者のアカウントが存在しないものであったならば、あたらしくつくります。もし、受信者のアカウントが contract であれば、すべての実行が完了するか、あるいは ガス欠 になるまで contract のコードを実行します。
5. もし、送信者が十分なお金を持っていなかったり、 ガス欠 のために、値の転送が失敗した場合には、手数料の支払いを除いて、全状態を元に戻し、手数料はマイナーのアカウントに加えます。
6. そうでなければ、余った全ての gas を全て送信者に返し、消費した gas は採掘者に支払われる手数料として送信します。


たとえば、contract コード が以下であるような場合を考えましょう。

    if !self.storage[calldataload(0)]:
        self.storage[calldataload(0)] = calldataload(32)

実際は、contract コードは低級EVM言語（アセンブラ）であることに注意してください。
このコードは Ethereum における高級言語であるひとつである Serpent 言語で書かれており、コードを確約したものにするために、
低級EVMコードへコンパイルすることが可能です。
さらに次に記す状況を想定しましょう。
この contract のストレージは空の状態から始まり、
* 10 ether の値と、 
* 0.001 ether / gas の gasprice で 2000 gas 、そして
* 64バイトのデータ（0-31バイトが数字`2`を表し、32~63バイト番地が`CHARLIE`という string を表しているものとします。）

の３つが、トランザクションとともに送信されるものとします。
この場合、状態遷移関数のプロセスは以下のようになります。

1. トランザクションが有効かつ well-formed であるか確認する。
2. トランザクション送信者が、最低限 2000 * 0.001 = 2 ether を所持しているか確認する。
もし、所持していれば、2 ether を送信者のアカウントから差し引く。
3. gas の量を gas = 2000; として初期化します。トランザクションのバイト長が 170 byte であるとすると、byte あたりの手数料が 5 であったことから、850 を差し引くことなり、1150 gas が残ります。
4. 送信者のアカウントから 10 ether を差し引き、それを送信先である contract アカウントに加えます。 
5. コードを走らせます。今回はとてもシンプルです。
まず contract は自身のストレージにおける `2` 番目の項目が使われているか確認し、
未使用であることを確認し、ストレージの `2` 番地に `CHARLIE` という値をセットします。

この操作で、187 gas を消費するとしましょう、すると残りの gas は 1150 - 187 = 963 となります。

6. 963 * 0.001 = 0.963 ether を送信者のアカウントに返金し、結果として出てきた「状態」を返します。

もしも、トランザクションの受信側に contract がなかったら、当該トランザクションにおける全手数料は、たんに、トランザクションのバイト長に 与えられた `GASPRICE` の値をかけたものとなり、トランザクションと一緒に送られたデータは全く関係のないものとなってしまうでしょう。


Note that messages work equivalently to transactions in terms of reverts: 
もしメッセージが gas を使い果たしてしまったらば、そのメッセージあるいはそのメッセージが引き金となるすべての実行処理がもとにもどされてしまいますが、その「親」の実行に関しては、やり直しになる必要がありません。
これは、contract が他の contract を呼ぶことに関して「安全」であることを意味し、これは、A が G gasもって B を呼び出すと、A による実行はたかだか G gas 分であることが保証されている、と捉えることができます。

最後に、contract を生成する opcode である `CREATE` があることに注意してください。
その実行メカニズムは一般的に言って `CALL` に似ていますが、実行結果があたらしく作られたコントラクトのコードを返すという点を除いて同じになります。

補足：contract が実際に信用あるものとして、機能するかどうかについてですが、
例として２人間の賭博をあげますと、二人のお金を預けることになるコントラクトはきちんと仕事をするという保証が必要です。
片一方が作成し、騙し取るということが可能に思われます。しかし、これは簡単に解決できます。片方により作成されたコントラクトの公開鍵はわかっているので、そのコードが実行する内容は明るみにでており、エミュレーターを使って、きちんと動作することを確認することで、簡単にコントラクトの安全性を逐一確認することができます。


### コード実行

Ethereum の contract コードは低級スタック・ベース・バイトコード言語で書かれており、「 Ethereum 仮想マシンコード 」や「 EVM code 」などと呼ばれております。
そのコードは一連のbyte列から構成されており、各 byte はひとつの命令を表しております。
一般的に、コード実行とは、プログラムカウンターが現在示すところの命令を実行してはプログラムカウンターを１つインクリメントする繰り返しにより構成される、無限ループであり、エラーや `STOP` あるいは `RETURN` といった命令が検出されるまで終わることがありません。

EVM における 命令 はデータを貯蔵するために必要な 三種類の スペース にアクセスします。

* **stack**, 後入れ先出しのコンテナで、push と pop という二つの命令により値を出し入れします。
* **Memory**, 無限拡張および無限展開可能なバイト配列
* **storage**, contract が保持する長期保存用ストレージで 、Key/value の貯蔵庫。スタックやメモリでは計算実行後毎にリセットされるのに対し、storage では長期間、値が保持される。

コードも、受信したメッセージにおける 値・送信者・データ にアクセス可能です。また ブロックヘッダ のデータにも同様にアクセスできます。コードは出力として byte 配列のデータ を戻り値として返すことも出来ます。

EVM コード における、形だけの実装が施された実行モデルは、驚くほどシンプルです。Ethereum 仮想マシン が動作しているとき、
ネットワーク全体における、全仮想マシン計算状態 は次のタプルにより決定されます。`(block_state, transaction, message, code, memory, stack, pc, gas)`
ここで、
 `block_state` は、全アカウントを保持し、残高やストレージといったデータをひきこんだ「 global な状態 」を表します。
実行の開始時毎に、「 命令 」は、変数`pc`番目の byte コードを取ってきます。`pc >= len(code)`の条件下では 0 となります。
各命令はタプルに対して、どのように影響するのかという点に関して、独自の定義があります。
例えば、
`ADD` はスタックから、二つアイテムを引き出し(pop)、その合計をまたスタックへ押し込めます(push)。
`SSTORE`は上部からふたつのアイテムを pop の上、二つ目のアイテムを contract のストレージにおける、一つ目のアイテムが示す番地に格納します。
即時コンパイルによる EVM マシン実行最適化の方法はたくさんありにもかかわらず、Ethereum は基本的に、実装すると数百行ほどの byte コードスペースを費やします。

### Blockchain と 採掘

![apply_block_diagram.png](http://vitalik.ca/files/apply_block_diagram.png)

Ethereum の blockchain は多くの点で Bitcoin のそれと似ていますが、いくつか違う点があります。
bockchain のアーキテクチャに関する Ethereum と Bitcoin の違いは、次のようになります。
Bitcoin とは違い Ethereum のブロックはトランザクションのリストとブロック生成時点の 状態 のコピーを内部に保持しています。
脇道にそれますが、ブロック番号 と difficulty という、べつの二つの値もブロックに貯蔵されます。
Ethereum における基本的な、ブロック有効化 アルゴリズム は以下となります。 

1. ブロックの参照する 直前のブロック が存在し、それが有効であるかをチェックします。
2. タイムスタンプが 、直前のにおけるそれよりも値が大きく、15分さきの未来まで後出のものより値が小さいことを確認します。
3. ブロック番号、難易度、トランザクションのルート、Uncle のルート および ガスの上限（様々な、低級Ethereumの仕様概念）が有効であるか確認します。
4. ブロックの有効証である proof of work が有効であるか確認します。
5. 直前のブロックの最後の状態を `S[0]` とする。 
6. `TX` をトランザクション・リストとし、`n` 個のトランザクションを含むものとする。 
`0...n-1` までの全ての数に対し、`S[i+1] = APPLY(S[i], TX[i])` とする。
もし、アプリケーション群のどれか一つでもエラーを返したり、ブロックで消費される全 gas 量がこの段階で `GASLIMIT` を超過していたりすると、エラーを返します。
7. `S[N]` を `S_FINAL` としますが、採掘者へのブロック採掘に対する報酬も加えます。
8. 状態 `S_FINAL` の マークル木 の ルート がブロックヘッダにおいて与えられる 最終状態のルート と一致するか確かめます。
もしそうであれば、ブロックは有効で、そうでなければ、ブロックは無効です。

さっと目を通しただけでは、このアプローチはとても非効率に思うかもしれません。
なぜならば、このやり方では、各ブロックで 全ネットワークの状態 を保存する必要があるからです。
しかし、現実的には、Etherum の効率は、Bitcoin のそれと比べなければなりません。
というのは、ここで 保存される状態 は、そのデータ木構造に貯蔵され、ブロック毎に、その小さな部分木が変更される必要があります。
このようにすると、一般的に、隣り合う二つのブロック間では、木の大部分は同じとなるはずであり、
そうであるがゆえ、データは、一度貯蔵され、ポインタ（つまり部分木のハッシュ値）を使用して二度 引用符 が付加される、という形式をとります。
この「状態」の保存方法を達成するために「 パトリシア木 」と呼ばれる 特別なデータ木 が使われ、
パトリシア木は、マークル木のコンセプトに対して修正がなされており、ノードの挿入・削除が可能であり、
ただ変わるだけでなく、効率が良くなります。
さらに、全状態の情報が、最新のブロックに含まれますので、ブロックチェインの全履歴を保存する必要がありません。
これは、Bitcoin に適用されたら、5 - 20倍のスペース節約になる 技術戦略 です。

ひろく尋ねられるのが、「どこで contract が実行されるのか」という質問で、物理デバイス上でどこか？ということです。
この質問に対しては、シンプルな回答があります。
「 contract の実行プロセスは、その状態遷移関数の定義の一部であり、それ故ブロックの有効化アルゴリズムの一部となります。
なので、もしトランザクションがブロック `B` に付加されたならばそのトランザクションにより生まれるコード実行は全てのノードで実行されます。
その時点より未来において、ブロック`B`をダウンロードした全てのノードということです。」


## アプリケーション

一般的に、Ethereum 上には、3 種類のアプリケーションがあります。
一つ目のカテゴリーは、金融系のアプリケーションで、金銭を使用する契約に対し、導入・管理の強力な手段をユーザへ提供するものです。
これには、副次通貨、金融ディリバティブ、ヘッジング契約、預金、資産相続文書や、さらに言及しますと、労働契約書まるまる含めたものなどがあります。
二つ目のカテゴリは、準金融系アプリであり、非金融的事象の結果に対して金銭を絡めてくるようなもので、その良い例として、計算理論における難題に対し懸賞金を自動執行するようなアプリが挙げられます。
三つ目としては、オンライン選挙 や 分散型統治機構 があります。


### 証明書発行のシステム

ブロックチェイン上の 証明書発行システム (token system) には、多々のアプリケーションがあり、
USドルや金を表す副次通貨から、株式、スマートプロパティとして個人発行した証明書、堅牢で偽造不可な商品券、あるいは全くの無から新たに作られた貨幣証書でさえその範囲に含まれ、経済原理となる（人々の行動の動機付けとなる）ポイント(稼ぎ)のシステムとして使われます。

Ethereum 上で 証明書発行システム を実装するのは驚くほどに簡単です。
理解するために重要点は、
通貨や証明書システムといった基軸となるものはすべて、
あるひとつの操作をともなうデータベース だということです。
そのひとつの操作とは : 

```
A から X 単位を差し引き、それを B にやる
その時の条件として
(1) A は トランザクション以前に 少なくとも X 単位 を保持している
(2) トランザクションが A によって承認される
```

トークンシステムの実装するのにかかる手間は、このロジックを contract に実装するだけです。
トークンシステムの Serpent における実装の基本コードは以下のようになります:

    def send(to, value):
        if self.storage[msg.sender] >= value:
            self.storage[msg.sender] = self.storage[msg.sender] - value
            self.storage[to] = self.storage[to] + value

これは、基礎的に、このドキュメントの冒頭で説明した"銀行システム" の状態遷移関数の文字通りの実装となります。
このコードとは別に、初期化ステップとして通貨単位を共有するあるいはその他特例のために、数行必要となり、
理念としては、ある function は、他の contract に、あるアドレスの残高を探索してもらうために追加されるものですが、
コードの記述はこれで十分です。
理論的に、Ethereum 基盤の証明書発行システムで副次通貨としてふるまうものは、
潜在的に別の重要な特徴を持っています。それは Bitcoin 基盤の meta currency には無いもので、
副次通貨で直接トランザクションの手数料の支払いが可能だという機能です。
もしこれを実装すらならば、
手数料支払いに使用される ether を送信者に 再振込 する方法をとり、
contract は、その時の ether 残高を維持管理することになるかと思います。
手数料支払い時、および常駐のオークションにおいて副次通貨を再度売る時、に使用される、この内部保持されている副次通貨単位を集めることで、ether の残高を再度満たすことになるでしょう。
ユーザはこのため ether でアカウントをアクティベートする必要がありますが、
一度 ether が確認されると、contract がその度ごとに再度振込をするので、再利用可能となるでしょう。


### Financial derivatives and Stable-Value Currencies

Financial derivatives are the most common application of a "smart contract", and one of the simplest to implement in code. The main challenge in implementing financial contracts is that the majority of them require reference to an external price ticker; for example, a very desirable application is a smart contract that hedges against the volatility of ether (or another cryptocurrency) with respect to the US dollar, but doing this requires the contract to know what the value of ETH/USD is. The simplest way to do this is through a "data feed" contract maintained by a specific party (eg. NASDAQ) designed so that that party has the ability to update the contract as needed, and providing an interface that allows other contracts to send a message to that contract and get back a response that provides the price.

Given that critical ingredient, the hedging contract would look as follows:

1. Wait for party A to input 1000 ether.
2. Wait for party B to input 1000 ether.
3. Record the USD value of 1000 ether, calculated by querying the data feed contract, in storage, say this is $x.
4. After 30 days, allow A or B to "reactivate" the contract in order to send $x worth of ether (calculated by querying the data feed contract again to get the new price) to A and the rest to B.

Such a contract would have significant potential in crypto-commerce. One of the main problems cited about cryptocurrency is the fact that it's volatile; although many users and merchants may want the security and convenience of dealing with cryptographic assets, they many not wish to face that prospect of losing 23% of the value of their funds in a single day. Up until now, the most commonly proposed solution has been issuer-backed assets; the idea is that an issuer creates a sub-currency in which they have the right to issue and revoke units, and provide one unit of the currency to anyone who provides them (offline) with one unit of a specified underlying asset (eg. gold, USD). The issuer then promises to provide one unit of the underlying asset to anyone who sends back one unit of the crypto-asset. This mechanism allows any non-cryptographic asset to be "uplifted" into a cryptographic asset, provided that the issuer can be trusted.

In practice, however, issuers are not always trustworthy, and in some cases the banking infrastructure is too weak, or too hostile, for such services to exist. Financial derivatives provide an alternative. Here, instead of a single issuer providing the funds to back up an asset, a decentralized market of speculators, betting that the price of a cryptographic reference asset (eg. ETH) will go up, plays that role. Unike issuers, speculators have no option to default on their side of the bargain because the hedging contract holds their funds in escrow. Note that this approach is not fully decentralized, because a trusted source is still needed to provide the price ticker, although arguably even still this is a massive improvement in terms of reducing infrastructure requirements (unlike being an issuer, issuing a price feed requires no licenses and can likely be categorized as free speech) and reducing the potential for fraud.

### Identity and Reputation Systems

The earliest alternative cryptocurrency of all, [Namecoin](http://namecoin.org/), attempted to use a Bitcoin-like blockchain to provide a name registration system, where users can register their names in a public database alongside other data. The major cited use case is for a [DNS](http://en.wikipedia.org/wiki/Domain_Name_System) system, mapping domain names like "bitcoin.org" (or, in Namecoin's case, "bitcoin.bit") to an IP address. Other use cases include email authentication and potentially more advanced reputation systems. Here is the basic contract to provide a Namecoin-like name registration system on Ethereum:

    def register(name, value):
        if !self.storage[name]:
            self.storage[name] = value

The contract is very simple; all it is is a database inside the Ethereum network that can be added to, but not modified or removed from. Anyone can register a name with some value, and that registration then sticks forever. A more sophisticated name registration contract will also have a "function clause" allowing other contracts to query it, as well as a mechanism for the "owner" (ie. the first registerer) of a name to change the data or transfer ownership. One can even add reputation and web-of-trust functionality on top.

### Decentralized File Storage

Over the past few years, there have emerged a number of popular online file storage startups, the most prominent being Dropbox, seeking to allow users to upload a backup of their hard drive and have the service store the backup and allow the user to access it in exchange for a monthly fee. However, at this point the file storage market is at times relatively inefficient; a cursory look at various [existing solutions](http://online-storage-service-review.toptenreviews.com/) shows that, particularly at the "uncanny valley" 20-200 GB level at which neither free quotas nor enterprise-level discounts kick in, monthly prices for mainstream file storage costs are such that you are paying for more than the cost of the entire hard drive in a single month. Ethereum contracts can allow for the development of a decentralized file storage ecosystem, where individual users can earn small quantities of money by renting out their own hard drives and unused space can be used to further drive down the costs of file storage.

The key underpinning piece of such a device would be what we have termed the "decentralized Dropbox contract". This contract works as follows. First, one splits the desired data up into blocks, encrypting each block for privacy, and builds a Merkle tree out of it. One then makes a contract with the rule that, every N blocks, the contract would pick a random index in the Merkle tree (using the previous block hash, accessible from contract code, as a source of randomness), and give X ether to the first entity to supply a transaction with a simplified payment verification-like proof of ownership of the block at that particular index in the tree. When a user wants to re-download their file, they can use a micropayment channel protocol (eg. pay 1 szabo per 32 kilobytes) to recover the file; the most fee-efficient approach is for the payer not to publish the transaction until the end, instead replacing the transaction with a slightly more lucrative one with the same nonce after every 32 kilobytes.

An important feature of the protocol is that, although it may seem like one is trusting many random nodes not to decide to forget the file, one can reduce that risk down to near-zero by splitting the file into many pieces via secret sharing, and watching the contracts to see each piece is still in some node's possession. If a contract is still paying out money, that provides a cryptographic proof that someone out there is still storing the file.

### Decentralized Autonomous Organizations

The general concept of a "decentralized autonomous organization" is that of a virtual entity that has a certain set of members or shareholders which, perhaps with a 67% majority, have the right to spend the entity's funds and modify its code. The members would collectively decide on how the organization should allocate its funds. Methods for allocating a DAO's funds could range from bounties, salaries to even more exotic mechanisms such as an internal currency to reward work. This essentially replicates the legal trappings of a traditional company or nonprofit but using only cryptographic blockchain technology for enforcement. So far much of the talk around DAOs has been around the "capitalist" model of a "decentralized autonomous corporation" (DAC) with dividend-receiving shareholders and tradable shares; an alternative, perhaps described as a "decentralized autonomous community", would have all members have an equal share in the decision making and require 67% of existing members to agree to add or remove a member. The requirement that one person can only have one membership would then need to be enforced collectively by the group.

A general outline for how to code a DAO is as follows. The simplest design is simply a piece of self-modifying code that changes if two thirds of members agree on a change. Although code is theoretically immutable, one can easily get around this and have de-facto mutability by having chunks of the code in separate contracts, and having the address of which contracts to call stored in the modifiable storage. In a simple implementation of such a DAO contract, there would be three transaction types, distinquished by the data provided in the transaction:

* `[0,i,K,V]` to register a proposal with index `i` to change the address at storage index `K` to value `V`
* `[0,i]` to register a vote in favor of proposal `i`
* `[2,i]` to finalize proposal `i` if enough votes have been made

The contract would then have clauses for each of these. It would maintain a record of all open storage changes, along with a list of who voted for them. It would also have a list of all members. When any storage change gets to two thirds of members voting for it, a finalizing transaction could execute the change. A more sophisticated skeleton would also have built-in voting ability for features like sending a transaction, adding members and removing members, and may even provide for [Liquid Democracy](http://en.wikipedia.org/wiki/Delegative_democracy)-style vote delegation (ie. anyone can assign someone to vote for them, and assignment is transitive so if A assigns B and B assigns C then C determines A's vote). This design would allow the DAO to grow organically as a decentralized community, allowing people to eventually delegate the task of filtering out who is a member to specialists, although unlike in the "current system" specialists can easily pop in and out of existence over time as individual community members change their alignments.

An alternative model is for a decentralized corporation, where any account can have zero or more shares, and two thirds of the shares are required to make a decision. A complete skeleton would involve asset management functionality, the ability to make an offer to buy or sell shares, and the ability to accept offers (preferably with an order-matching mechanism inside the contract). Delegation would also exist Liquid Democracy-style, generalizing the concept of a "board of directors".

### Further Applications

**1. Savings wallets**. Suppose that Alice wants to keep her funds safe, but is worried that she will lose or someone will hack her private key. She puts ether into a contract with Bob, a bank, as follows:

* Alice alone can withdraw a maximum of 1% of the funds per day.
* Bob alone can withdraw a maximum of 1% of the funds per day, but Alice has the ability to make a transaction with her key shutting off this ability.
* Alice and Bob together can withdraw anything.

Normally, 1% per day is enough for Alice, and if Alice wants to withdraw more she can contact Bob for help. If Alice's key gets hacked, she runs to Bob to move the funds to a new contract. If she loses her key, Bob will get the funds out eventually. If Bob turns out to be malicious, then she can turn off his ability to withdraw.

**2. Crop insurance**. One can easily make a financial derivatives contract but using a data feed of the weather instead of any price index. If a farmer in Iowa purchases a derivative that pays out inversely based on the precipitation in Iowa, then if there is a drought, the farmer will automatically receive money and if there is enough rain the farmer will be happy because their crops would do well. This can be expanded to natural disaster insurance generally.

**3. A decentralized data feed**. For financial contracts for difference, it may actually be possible to decentralize the data feed via a protocol called "[SchellingCoin](http://blog.ethereum.org/2014/03/28/schellingcoin-a-minimal-trust-universal-data-feed/)". SchellingCoin basically works as follows: N parties all put into the system the value of a given datum (eg. the ETH/USD price), the values are sorted, and everyone between the 25th and 75th percentile gets one token as a reward. Everyone has the incentive to provide the answer that everyone else will provide, and the only value that a large number of players can realistically agree on is the obvious default: the truth. This creates a decentralized protocol that can theoretically provide any number of values, including the ETH/USD price, the temperature in Berlin or even the result of a particular hard computation.

**4. Smart multisignature escrow**. Bitcoin allows multisignature transaction contracts where, for example, three out of a given five keys can spend the funds. Ethereum allows for more granularity; for example, four out of five can spend everything, three out of five can spend up to 10% per day, and two out of five can spend up to 0.5% per day. Additionally, Ethereum multisig is asynchronous - two parties can register their signatures on the blockchain at different times and the last signature will automatically send the transaction.

**5. Cloud computing**. The EVM technology can also be used to create a verifiable computing environment, allowing users to ask others to carry out computations and then optionally ask for proofs that computations at certain randomly selected checkpoints were done correctly. This allows for the creation of a cloud computing market where any user can participate with their desktop, laptop or specialized server, and spot-checking together with security deposits can be used to ensure that the system is trustworthy (ie. nodes cannot profitably cheat). Although such a system may not be suitable for all tasks; tasks that require a high level of inter-process communication, for example, cannot easily be done on a large cloud of nodes. Other tasks, however, are much easier to parallelize; projects like SETI@home, folding@home and genetic algorithms can easily be implemented on top of such a platform.

**6. Peer-to-peer gambling**. Any number of peer-to-peer gambling protocols, such as Frank Stajano and Richard Clayton's [Cyberdice](http://www.cl.cam.ac.uk/~fms27/papers/2008-StajanoCla-cyberdice.pdf), can be implemented on the Ethereum blockchain. The simplest gambling protocol is actually simply a contract for difference on the next block hash, and more advanced protocols can be built up from there, creating gambling services with near-zero fees that have no ability to cheat.

**7. Prediction markets**. Provided an oracle or SchellingCoin, prediction markets are also easy to implement, and prediction markets together with SchellingCoin may prove to be the first mainstream application of [futarchy](http://hanson.gmu.edu/futarchy.html) as a governance protocol for decentralized organizations.

**8. On-chain decentralized marketplaces**, using the identity and reputation system as a base.

## Miscellanea And Concerns

### Modified GHOST Implementation

The "Greedy Heaviest Observed Subtree" (GHOST) protocol is an innovation first introduced by Yonatan Sompolinsky and Aviv Zohar in [December 2013](http://www.cs.huji.ac.il/~avivz/pubs/13/btc_scalability_full.pdf). The motivation behind GHOST is that blockchains with fast confirmation times currently suffer from reduced security due to a high stale rate - because blocks take a certain time to propagate through the network, if miner A mines a block and then miner B happens to mine another block before miner A's block propagates to B, miner B's block will end up wasted and will not contribute to network security. Furthermore, there is a centralization issue: if miner A is a mining pool with 30% hashpower and B has 10% hashpower, A will have a risk of producing a stale block 70% of the time (since the other 30% of the time A produced the last block and so will get mining data immediately) whereas B will have a risk of producing a stale block 90% of the time. Thus, if the block interval is short enough for the stale rate to be high, A will be substantially more efficient simply by virtue of its size. With these two effects combined, blockchains which produce blocks quickly are very likely to lead to one mining pool having a large enough percentage of the network hashpower to have de facto control over the mining process.

As described by Sompolinsky and Zohar, GHOST solves the first issue of network security loss by including stale blocks in the calculation of which chain is the "longest"; that is to say, not just the parent and further ancestors of a block, but also the stale descendants of the block's ancestor (in Ethereum jargon, "uncles") are added to the calculation of which block has the largest total proof of work backing it. To solve the second issue of centralization bias, we go beyond the protocol described by Sompolinsky and Zohar, and also provide block rewards to stales: a stale block receives 87.5% of its base reward, and the nephew that includes the stale block receives the remaining 12.5%. Transaction fees, however, are not awarded to uncles.

Ethereum implements a simplified version of GHOST which only goes down seven levels. Specifically, it is defined as follows:

* A block must specify a parent, and it must specify 0 or more uncles
* An uncle included in block B must have the following properties:
  * It must be a direct child of the kth generation ancestor of B, where 2 <= k <= 7.
  * It cannot be an ancestor of B
  * An uncle must be a valid block header, but does not need to be a previously verified or even valid block
  * An uncle must be different from all uncles included in previous blocks and all other uncles included in the same block (non-double-inclusion)
* For every uncle U in block B, the miner of B gets an additional 3.125% added to its coinbase reward and the miner of U gets 93.75% of a standard coinbase reward.

This limited version of GHOST, with uncles includable only up to 7 generations, was used for two reasons. First, unlimited GHOST would include too many complications into the calculation of which uncles for a given block are valid. Second, unlimited GHOST with compensation as used in Ethereum removes the incentive for a miner to mine on the main chain and not the chain of a public attacker.

### Fees

Because every transaction published into the blockchain imposes on the network the cost of needing to download and verify it, there is a need for some regulatory mechanism, typically involving transaction fees, to prevent abuse. The default approach, used in Bitcoin, is to have purely voluntary fees, relying on miners to act as the gatekeepers and set dynamic minimums. This approach has been received very favorably in the Bitcoin community particularly because it is "market-based", allowing supply and demand between miners and transaction senders determine the price. The problem with this line of reasoning is, however, that transaction processing is not a market; although it is intuitively attractive to construe transaction processing as a service that the miner is offering to the sender, in reality every transaction that a miner includes will need to be processed by every node in the network, so the vast majority of the cost of transaction processing is borne by third parties and not the miner that is making the decision of whether or not to include it. Hence, tragedy-of-the-commons problems are very likely to occur.

However, as it turns out this flaw in the market-based mechanism, when given a particular inaccurate simplifying assumption, magically cancels itself out. The argument is as follows. Suppose that:

1. A transaction leads to `k` operations, offering the reward `kR` to any miner that includes it where `R` is set by the sender and `k` and `R` are (roughly) visible to the miner beforehand.
2. An operation has a processing cost of `C` to any node (ie. all nodes have equal efficiency)
3. There are `N` mining nodes, each with exactly equal processing power (ie. `1/N` of total)
4. No non-mining full nodes exist.

A miner would be willing to process a transaction if the expected reward is greater than the cost. Thus, the expected reward is `kR/N` since the miner has a `1/N` chance of processing the next block, and the processing cost for the miner is simply `kC`. Hence, miners will include transactions where `kR/N > kC`, or `R > NC`. Note that `R` is the per-operation fee provided by the sender, and is thus a lower bound on the benefit that the sender derives from the transaction, and `NC` is the cost to the entire network together of processing an operation. Hence, miners have the incentive to include only those transactions for which the total utilitarian benefit exceeds the cost.

However, there are several important deviations from those assumptions in reality:

1. The miner does pay a higher cost to process the transaction than the other verifying nodes, since the extra verification time delays block propagation and thus increases the chance the block will become a stale.
2. There do exist nonmining full nodes.
3. The mining power distribution may end up radically inegalitarian in practice.
4. Speculators, political enemies and crazies whose utility function includes causing harm to the network do exist, and they can cleverly set up contracts where their cost is much lower than the cost paid by other verifying nodes.

(1) provides a tendency for the miner to include fewer transactions, and (2) increases `NC`; hence, these two effects at least partially cancel each other out. (3) and (4) are the major issue; to solve them we simply institute a floating cap: no block can have more operations than `BLK_LIMIT_FACTOR` times the long-term exponential moving average. Specifically:

    blk.oplimit = floor((blk.parent.oplimit * (EMAFACTOR - 1) + floor(parent.opcount * BLK_LIMIT_FACTOR)) / EMA_FACTOR)

`BLK_LIMIT_FACTOR` and `EMA_FACTOR` are constants that will be set to 65536 and 1.5 for the time being, but will likely be changed after further analysis.

There is another factor disincentivizing large block sizes in Bitcoin: blocks that are large will take longer to propagate, and thus have a higher probability of becoming stales. In Ethereum, highly gas-consuming blocks can also take longer to propagate both because they are physically larger and because they take longer to process the transaction state transitions to validate. This delay disincentive is a significant consideration in Bitcoin, but less so in Ethereum because of the GHOST protocol; hence, relying on regulated block limits provides a more stable baseline.

### Computation And Turing-Completeness

An important note is that the Ethereum virtual machine is Turing-complete; this means that EVM code can encode any computation that can be conceivably carried out, including infinite loops. EVM code allows looping in two ways. First, there is a `JUMP` instruction that allows the program to jump back to a previous spot in the code, and a `JUMPI` instruction to do conditional jumping, allowing for statements like `while x < 27: x = x * 2`. Second, contracts can call other contracts, potentially allowing for looping through recursion. This naturally leads to a problem: can malicious users essentially shut miners and full nodes down by forcing them to enter into an infinite loop? The issue arises because of a problem in computer science known as the halting problem: there is no way to tell, in the general case, whether or not a given program will ever halt.

As described in the state transition section, our solution works by requiring a transaction to set a maximum number of computational steps that it is allowed to take, and if execution takes longer computation is reverted but fees are still paid. Messages work in the same way. To show the motivation behind our solution, consider the following examples:

* An attacker creates a contract which runs an infinite loop, and then sends a transaction activating that loop to the miner. The miner will process the transaction, running the infinite loop, and wait for it to run out of gas. Even though the execution runs out of gas and stops halfway through, the transaction is still valid and the miner still claims the fee from the attacker for each computational step.
* An attacker creates a very long infinite loop with the intent of forcing the miner to keep computing for such a long time that by the time computation finishes a few more blocks will have come out and it will not be possible for the miner to include the transaction to claim the fee. However, the attacker will be required to submit a value for `STARTGAS` limiting the number of computational steps that execution can take, so the miner will know ahead of time that the computation will take an excessively large number of steps.
* An attacker sees a contract with code of some form like `send(A,contract.storage[A]); contract.storage[A] = 0`, and sends a transaction with just enough gas to run the first step but not the second (ie. making a withdrawal but not letting the balance go down). The contract author does not need to worry about protecting against such attacks, because if execution stops halfway through the changes get reverted.
* A financial contract works by taking the median of nine proprietary data feeds in order to minimize risk. An attacker takes over one of the data feeds, which is designed to be modifiable via the variable-address-call mechanism described in the section on DAOs, and converts it to run an infinite loop, thereby attempting to force any attempts to claim funds from the financial contract to run out of gas. However, the financial contract can set a gas limit on the message to prevent this problem.

The alternative to Turing-completeness is Turing-incompleteness, where `JUMP` and `JUMPI` do not exist and only one copy of each contract is allowed to exist in the call stack at any given time. With this system, the fee system described and the uncertainties around the effectiveness of our solution might not be necessary, as the cost of executing a contract would be bounded above by its size. Additionally, Turing-incompleteness is not even that big a limitation; out of all the contract examples we have conceived internally, so far only one required a loop, and even that loop could be removed by making 26 repetitions of a one-line piece of code. Given the serious implications of Turing-completeness, and the limited benefit, why not simply have a Turing-incomplete language? In reality, however, Turing-incompleteness is far from a neat solution to the problem. To see why, consider the following contracts:

    C0: call(C1); call(C1);
    C1: call(C2); call(C2);
    C2: call(C3); call(C3);
    ...
    C49: call(C50); call(C50);
    C50: (run one step of a program and record the change in storage)

Now, send a transaction to A. Thus, in 51 transactions, we have a contract that takes up 2<sup>50</sup> computational steps. Miners could try to detect such logic bombs ahead of time by maintaining a value alongside each contract specifying the maximum number of computational steps that it can take, and calculating this for contracts calling other contracts recursively, but that would require miners to forbid contracts that create other contracts (since the creation and execution of all 26 contracts above could easily be rolled into a single contract). Another problematic point is that the address field of a message is a variable, so in general it may not even be possible to tell which other contracts a given contract will call ahead of time. Hence, all in all, we have a surprising conclusion: Turing-completeness is surprisingly easy to manage, and the lack of Turing-completeness is equally surprisingly difficult to manage unless the exact same controls are in place - but in that case why not just let the protocol be Turing-complete?

### Currency And Issuance

The Ethereum network includes its own built-in currency, ether, which serves the dual purpose of providing a primary liquidity layer to allow for efficient exchange between various types of digital assets and, more importantly, of providing a mechanism for paying transaction fees. For convenience and to avoid future argument (see the current mBTC/uBTC/satoshi debate in Bitcoin), the denominations will be pre-labelled:

* 1: wei
* 10<sup>12</sup>: szabo
* 10<sup>15</sup>: finney
* 10<sup>18</sup>: ether

This should be taken as an expanded version of the concept of "dollars" and "cents" or "BTC" and "satoshi". In the near future, we expect "ether" to be used for ordinary transactions, "finney" for microtransactions and "szabo" and "wei" for technical discussions around fees and protocol implementation; the remaining denominations may become useful later and should not be included in clients at this point.

The issuance model will be as follows:

* Ether will be released in a currency sale at the price of 1000-2000 ether per BTC, a mechanism intended to fund the Ethereum organization and pay for development that has been used with success by other platforms such as Mastercoin and NXT. Earlier buyers will benefit from larger discounts. The BTC received from the sale will be used entirely to pay salaries and bounties to developers and invested into various for-profit and non-profit projects in the Ethereum and cryptocurrency ecosystem.
* 0.099x the total amount sold (60102216 ETH) will be allocated to the organization to compensate early contributors and pay ETH-denominated expenses before the genesis block.
* 0.099x the total amount sold will be maintained as a long-term reserve.
* 0.26x the total amount sold will be allocated to miners per year forever after that point.

| Group  | At launch | After 1 year | After 5 years
| ------------- | ------------- |-------------| ----------- |
| Currency units  | 1.198X | 1.458X  |  2.498X |
| Purchasers  | 83.5% | 68.6%  | 40.0% |
| Reserve spent pre-sale | 8.26% | 6.79% | 3.96% |
| Reserve used post-sale | 8.26% | 6.79% | 3.96% |
| Miners | 0% | 17.8% | 52.0% |

**Long-Term Supply Growth Rate (percent)**

[SPV in bitcoin]

_Despite the linear currency issuance, just like with Bitcoin over time the supply growth rate nevertheless tends to zero_

The two main choices in the above model are (1) the existence and size of an endowment pool, and (2) the existence of a permanently growing linear supply, as opposed to a capped supply as in Bitcoin. The justification of the endowment pool is as follows. If the endowment pool did not exist, and the linear issuance reduced to 0.217x to provide the same inflation rate, then the total quantity of ether would be 16.5% less and so each unit would be 19.8% more valuable. Hence, in the equilibrium 19.8% more ether would be purchased in the sale, so each unit would once again be exactly as valuable as before. The organization would also then have 1.198x as much BTC, which can be considered to be split into two slices: the original BTC, and the additional 0.198x. Hence, this situation is _exactly equivalent_ to the endowment, but with one important difference: the organization holds purely BTC, and so is not incentivized to support the value of the ether unit.

The permanent linear supply growth model reduces the risk of what some see as excessive wealth concentration in Bitcoin, and gives individuals living in present and future eras a fair chance to acquire currency units, while at the same time retaining a strong incentive to obtain and hold ether because the "supply growth rate" as a percentage still tends to zero over time. We also theorize that because coins are always lost over time due to carelessness, death, etc, and coin loss can be modeled as a percentage of the total supply per year, that the total currency supply in circulation will in fact eventually stabilize at a value equal to the annual issuance divided by the loss rate (eg. at a loss rate of 1%, once the supply reaches 26X then 0.26X will be mined and 0.26X lost every year, creating an equilibrium).

Note that in the future, it is likely that Ethereum will switch to a proof-of-stake model for security, reducing the issuance requirement to somewhere between zero and 0.05X per year. In the event that the Ethereum organization loses funding or for any other reason disappears, we leave open a "social contract": anyone has the right to create a future candidate version of Ethereum, with the only condition being that the quantity of ether must be at most equal to `60102216 * (1.198 + 0.26 * n)` where `n` is the number of years after the genesis block. Creators are free to crowd-sell or otherwise assign some or all of the difference between the PoS-driven supply expansion and the maximum allowable supply expansion to pay for development. Candidate upgrades that do not comply with the social contract may justifiably be forked into compliant versions.

### Mining Centralization

The Bitcoin mining algorithm works by having miners compute SHA256 on slightly modified versions of the block header millions of times over and over again, until eventually one node comes up with a version whose hash is less than the target (currently around 2<sup>192</sup>). However, this mining algorithm is vulnerable to two forms of centralization. First, the mining ecosystem has come to be dominated by ASICs (application-specific integrated circuits), computer chips designed for, and therefore thousands of times more efficient at, the specific task of Bitcoin mining. This means that Bitcoin mining is no longer a highly decentralized and egalitarian pursuit, requiring millions of dollars of capital to effectively participate in. Second, most Bitcoin miners do not actually perform block validation locally; instead, they rely on a centralized mining pool to provide the block headers. This problem is arguably worse: as of the time of this writing, the top three mining pools indirectly control roughly 50% of processing power in the Bitcoin network, although this is mitigated by the fact that miners can switch to other mining pools if a pool or coalition attempts a 51% attack.

The current intent at Ethereum is to use a mining algorithm where miners are required to fetch random data from the state, compute some randomly selected transactions from the last N blocks in the blockchain, and return the hash of the result. This has two important benefits. First, Ethereum contracts can include any kind of computation, so an Ethereum ASIC would essentially be an ASIC for general computation - ie. a better CPU. Second, mining requires access to the entire blockchain, forcing miners to store the entire blockchain and at least be capable of verifying every transaction. This removes the need for centralized mining pools; although mining pools can still serve the legitimate role of evening out the randomness of reward distribution, this function can be served equally well by peer-to-peer pools with no central control.

This model is untested, and there may be difficulties along the way in avoiding certain clever optimizations when using contract execution as a mining algorithm. However, one notably interesting feature of this algorithm is that it allows anyone to "poison the well", by introducing a large number of contracts into the blockchain specifically designed to stymie certain ASICs. The economic incentives exist for ASIC manufacturers to use such a trick to attack each other. Thus, the solution that we are developing is ultimately an adaptive economic human solution rather than purely a technical one.

### Scalability

One common concern about Ethereum is the issue of scalability. Like Bitcoin, Ethereum suffers from the flaw that every transaction needs to be processed by every node in the network. With Bitcoin, the size of the current blockchain rests at about 15 GB, growing by about 1 MB per hour. If the Bitcoin network were to process Visa's 2000 transactions per second, it would grow by 1 MB per three seconds (1 GB per hour, 8 TB per year). Ethereum is likely to suffer a similar growth pattern, worsened by the fact that there will be many applications on top of the Ethereum blockchain instead of just a currency as is the case with Bitcoin, but ameliorated by the fact that Ethereum full nodes need to store just the state instead of the entire blockchain history.

The problem with such a large blockchain size is centralization risk. If the blockchain size increases to, say, 100 TB, then the likely scenario would be that only a very small number of large businesses would run full nodes, with all regular users using light SPV nodes. In such a situation, there arises the potential concern that the full nodes could band together and all agree to cheat in some profitable fashion (eg. change the block reward, give themselves BTC). Light nodes would have no way of detecting this immediately. Of course, at least one honest full node would likely exist, and after a few hours information about the fraud would trickle out through channels like Reddit, but at that point it would be too late: it would be up to the ordinary users to organize an effort to blacklist the given blocks, a massive and likely infeasible coordination problem on a similar scale as that of pulling off a successful 51% attack. In the case of Bitcoin, this is currently a problem, but there exists a blockchain modification [suggested by Peter Todd](http://sourceforge.net/p/bitcoin/mailman/message/31709140/) which will alleviate this issue.

In the near term, Ethereum will use two additional strategies to cope with this problem. First, because of the blockchain-based mining algorithms, at least every miner will be forced to be a full node, creating a lower bound on the number of full nodes. Second and more importantly, however, we will include an intermediate state tree root in the blockchain after processing each transaction. Even if block validation is centralized, as long as one honest verifying node exists, the centralization problem can be circumvented via a verification protocol. If a miner publishes an invalid block, that block must either be badly formatted, or the state `S[n]` is incorrect. Since `S[0]` is known to be correct, there must be some first state `S[i]` that is incorrect where `S[i-1]` is correct. The verifying node would provide the index `i`, along with a "proof of invalidity" consisting of the subset of Patricia tree nodes needing to process `APPLY(S[i-1],TX[i]) -> S[i]`. Nodes would be able to use those nodes to run that part of the computation, and see that the `S[i]` generated does not match the `S[i]` provided.

Another, more sophisticated, attack would involve the malicious miners publishing incomplete blocks, so the full information does not even exist to determine whether or not blocks are valid. The solution to this is a challenge-response protocol: verification nodes issue "challenges" in the form of target transaction indices, and upon receiving a node a light node treats the block as untrusted until another node, whether the miner or another verifier, provides a subset of Patricia nodes as a proof of validity.

## Conclusion

The Ethereum protocol was originally conceived as an upgraded version of a cryptocurrency, providing advanced features such as on-blockchain escrow, withdrawal limits, financial contracts, gambling markets and the like via a highly generalized programming language. The Ethereum protocol would not "support" any of the applications directly, but the existence of a Turing-complete programming language means that arbitrary contracts can theoretically be created for any transaction type or application. What is more interesting about Ethereum, however, is that the Ethereum protocol moves far beyond just currency. Protocols around decentralized file storage, decentralized computation and decentralized prediction markets, among dozens of other such concepts, have the potential to substantially increase the efficiency of the computational industry, and provide a massive boost to other peer-to-peer protocols by adding for the first time an economic layer. Finally, there is also a substantial array of applications that have nothing to do with money at all.

The concept of an arbitrary state transition function as implemented by the Ethereum protocol provides for a platform with unique potential; rather than being a closed-ended, single-purpose protocol intended for a specific array of applications in data storage, gambling or finance, Ethereum is open-ended by design, and we believe that it is extremely well-suited to serving as a foundational layer for a very large number of both financial and non-financial protocols in the years to come.

## Notes and Further Reading

#### Notes

1. A sophisticated reader may notice that in fact a Bitcoin address is the hash of the elliptic curve public key, and not the public key itself. However, it is in fact perfectly legitimate cryptographic terminology to refer to the pubkey hash as a public key itself. This is because Bitcoin's cryptography can be considered to be a custom digital signature algorithm, where the public key consists of the hash of the ECC pubkey, the signature consists of the ECC pubkey concatenated with the ECC signature, and the verification algorithm involves checking the ECC pubkey in the signature against the ECC pubkey hash provided as a public key and then verifying the ECC signature against the ECC pubkey.
2. Technically, the median of the 11 previous blocks.
3. Internally, 2 and "CHARLIE" are both numbers, with the latter being in big-endian base 256 representation. Numbers can be at least 0 and at most 2<sup>256</sup>-1.

#### Further Reading

1. Intrinsic value: http://bitcoinmagazine.com/8640/an-exploration-of-intrinsic-value-what-it-is-why-bitcoin-doesnt-have-it-and-why-bitcoin-does-have-it/
2. Smart property: https://en.bitcoin.it/wiki/Smart_Property
3. Smart contracts: https://en.bitcoin.it/wiki/Contracts
4. B-money: http://www.weidai.com/bmoney.txt
5. Reusable proofs of work: http://www.finney.org/~hal/rpow/
6. Secure property titles with owner authority: http://szabo.best.vwh.net/securetitle.html
7. Bitcoin whitepaper: http://bitcoin.org/bitcoin.pdf
8. Namecoin: https://namecoin.org/
9. Zooko's triangle: http://en.wikipedia.org/wiki/Zooko's_triangle
10. Colored coins whitepaper: https://docs.google.com/a/buterin.com/document/d/1AnkP_cVZTCMLIzw4DvsW6M8Q2JC0lIzrTLuoWu2z1BE/edit
11. Mastercoin whitepaper: https://github.com/mastercoin-MSC/spec
12. Decentralized autonomous corporations, Bitcoin Magazine: http://bitcoinmagazine.com/7050/bootstrapping-a-decentralized-autonomous-corporation-part-i/
13. Simplified payment verification: https://en.bitcoin.it/wiki/Scalability#Simplifiedpaymentverification
14. Merkle trees: http://en.wikipedia.org/wiki/Merkle_tree
15. Patricia trees: http://en.wikipedia.org/wiki/Patricia_tree
16. GHOST: http://www.cs.huji.ac.il/~avivz/pubs/13/btc_scalability_full.pdf
17. StorJ and Autonomous Agents, Jeff Garzik: http://garzikrants.blogspot.ca/2013/01/storj-and-bitcoin-autonomous-agents.html
18. Mike Hearn on Smart Property at Turing Festival: http://www.youtube.com/watch?v=Pu4PAMFPo5Y
19. Ethereum RLP: https://github.com/ethereum/wiki/wiki/%5BEnglish%5D-RLP
20. Ethereum Merkle Patricia trees: https://github.com/ethereum/wiki/wiki/%5BEnglish%5D-Patricia-Tree
21. Peter Todd on Merkle sum trees: http://sourceforge.net/p/bitcoin/mailman/message/31709140/
