

### Merkle Patricia Tree 仕様書

Merkle Patricia tree は、あらゆる、「 ( key, value ) の帳簿 」を保存するのに使用できる、
暗号学的認証が付与されたデータ構造を提供しますが、
この仕様書においては、key 及び value は string 文字列 に制限します。
(この制約を解除するには、単に他のデータ型におけるシリアル化の形式を用いるだけです。)
この データ木 は完全に決定的です。というのは、
全く同じ ( key, value ) の帳簿を持った Patricia tree は最後のバイトに至るまで正確に同じであり、
それ故同じ root hash を持つ事が保証されており、また、O(log(n)) という優秀な計算時間内でデータの挿入、検索、削除が可能であり、
red black tree のような複雑な比較基盤のデータ構造よりも、簡単に理解しコード化する事ができます。

<br>
<br>

### はじめに: Basic Radix Trees

基本的な radix tree においては、すべてのノードは次のように表現されます :

    [ value, i0, i1 ... in]

ここで i0 ... in は、あるアルファベットの記号列を表しており、
そのアルファベット記号単体を表すノードへのパスとなる値が格納されます。
（アルファベットにはしばしば2進数もしくは16進数が用いられます。）
value には、そのノードを終端とするときの値が格納されています。
i0, i1 ... in の中の値は NULL もしくは 別のノードへの
（ Ethereum においては他ノードのハッシュ値への）ポインタ です。
これで、基本的な (key,value) store が構成できます。

例を示します。
tree において、現在 dog に対応する値を知りたいとすると、
まず、dog を該当アルファベットに
（ここでは16進数を使用して 646f67 に）
変換します。
そして、646f67 というパスに従って木を下っていき、
パスの最後にたどり着いたら、そこで値を読みます。
つまりまずはじめに、root node を得るために key/ralue store の中で root hash を探します。
そして、次に root node において６のノードへの値を参照し、一つパスを下ります。
そして、次はそこで４のノードへの値を参照し、そこで６のノードへのを参照するという具合に繰り返し、
root -> 6 -> 4 -> 6 -> f -> 6 -> 7 というパスを辿り、そこで結果として返すノードの値を参照します。

radix tree における更新と削除の操作はとても簡単で、概ね次のように定義されます。

```py
    def update(node,key,value):
        if key == '':
            curnode = db.get(node) if node else [ NULL ] * 17
            newnode = curnode.copy()
            newnode['value'] = value
        else:
            curnode = db.get(node) if node else [ NULL ] * 17
            newnode = curnode.copy()
            newindex = update(curnode[key[0]],key[1:],value)
            newnode[key[0]] = newindex
        db.put(hash(newnode),newnode)
        return hash(newnode)

    def delete(node,key):
        if key == '' or node is NULL:
            return NULL
        else:
            curnode = db.get(node)
            newnode = curnode.copy()
            newindex = delete(curnode[key[0]],key[1:])
            newnode[key[0]] = newindex
            if len(filter(x -&gt; x is not NULL, newnode)) == 0:
                return NULL
            else:
                db.put(hash(newnode),newnode)
                return hash(newnode)
```




われわれの提供する radix tree における "Merkle" 的な役割とは次の事実に由来します。
ノードを表す決定論的な暗号学的ハッシュ値は、ノードへのポインタとして使われているという事実であり、
それは、伝統的な radix tree のc言語による実装時における 32/64bit で表されるメモリ内への格納のやり方とはむしろ異なります。
これにより、データ構造に対する暗号学的に保証された認証形式が与えられます。
というのは、与えられた trie における root hash が公に共有されていたとすると、
「ある value が与えられたときに、ある key が特定される」という証明を皆が提供することができます。
というのは、これは、与えられた value からのパスを遡っていくことで、
root にたどり着いた時 key がなんであるかを提供することができます。
ある攻撃者が、実際には存在しない (key,value) ペアの証明を提供することはできません。
というのは root hash は最終的にその下にある全てのハッシュに依存しているので、
すべての修正が root hash に影響を与えます。

しかしながら、radix tree にはよく知られた制限があります。
それは、その非効率的な側面です。
もし、いま、ただ一つの (key, value) ペア を store したいとし、その key の長さが数百文字とすると、
radix tree では一文字を格納する１ノードを store するのに1キロバイト以上のスペースが必要となり、
また、各参照および削除ごとに数百ステップもかかってしまいます。
ここで紹介する Patricia tree (パトリシア木) はこの問題を解決します。


<br>
<br>

### 仕様: Compact encoding of hex sequence with optional terminator

16進文字列の「コンパクト符号化」における伝統的方法は、バイナリへの変換です。
というのはつまり 0f1248 という16進文字列は \[15,18,72\]の3バイトとなります。
（あるいは string として \x0f\x18H と表現されます。）
しかしこのやり方では、ある小さな問題に直面します。
符号化したい16進文字列の長さが奇数だとしたらどうなるのでしょうか？
その場合、いわば 0f1248 と f1248 の区別ができません。
この問題に加えて、Merkle Patricia tree を用いたわれわれのアプリケーションにおいて、詳細は後に述べますが、
16進文字に加え、終端ノードであることを示す &quot;終端記号&quot; T を加えた17文字を
アルファベットとして扱うといった仕様の追加が必要となってきます。
終端記号は一度だけ登場し、文字列の最後に置かれます。
違う角度から見てみましょう。
アルファベットに 終端記号 T を加えるのでなく、
その代わりに終端記号の存在を表す bit を与え、
それを、与えられたノードの種類を表す 1 bit として扱うという考えに至ります。
この考え方では、（仕様の詳細は後で述べますが）他ノードのハッシュ値だけでなく、終端ノードを符号化することができます。
（終端ノードにおいては、value には実際に使う値が格納されています。）

これら双方の問題を解決するために、
生成されるバイトストリームにおける最初の「ニブル（4bit)」に二つのフラグを符号化します。
それは、（記号 T を無視した上で）長さが奇数であるかを表したフラグと、
ノードが終端状態かどうかを表したフラグです。
これらのフラグは、16進文字列の先頭のニブルの重要な下位 ２bit の中にそれぞれを配置します。
符号化する前の16進文字列の長さが偶数の場合は、
符号化した後の16進文字列の長さが偶数とするために先頭から2番目の「ニブル」を
（値０として）導入しなければなりません。
この様にすることで偶奇両方のすべての数のバイトで表現が可能となります。
これは、次のようにコード化を行います。

```py
    def compact_encode(hexarray):
        term = 1 if hexarray[-1] == 16 else 0
        if term: hexarray = hexarray[:-1]
        oddlen = len(hexarray) % 2
        flags = 2 * term + oddlen
        if oddlen:
            hexarray = [flags] + hexarray
        else:
            hexarray = [flags] + [0] + hexarray
        // hexarray now has an even length whose first nibble is the flags.
        o = ''
        for i in range(0,len(hexarray),2):
            o += chr(16 * hexarray[i] + hexarray[i+1])
        return o
```

Examples:

    &gt; [ 1, 2, 3, 4, 5 ]
    '\x11\x23\x45'
    &gt; [ 0, 1, 2, 3, 4, 5 ]
    '\x00\x01\x23\x45'
    &gt; [ 0, 15, 1, 12, 11, 8, T ]
    '\x20\x0f\x1c\xb8'
    &gt; [ 15, 1, 12, 11, 8, T ]
    '\x3f\x1c\xb8'



<br>
<br>

### 仕様: Merkle Patricia Tree

マークル・パトリシア木は、
データ構造にある種の複雑性を加えることにより、
非効率性の問題を解決します。
マークル・パトリシア木におけるノードは次の３つに分類できます。


1. NULL (空文字列として表現される)
2. アイテムが２つの配列 `[ key, v ]` (kv node)
3. アイテムが17つの配列 `[ v0 ... v15, vt ]` (diverge node)


このアイデアは以下のとおりです。
一つずつしか要素をもたない（分岐枝を持たない）複数のノードの連なりの代わりにそれらをひとつにまとめて
 kv node `[ key, value ]` 配置します。
ここで、key はまとめられたパスに対し上述の符号化を行った文字列を与え、
value は、そのパスの先にあるノードのハッシュ値です。

このコンセプトに対し、もうひとつ変化を加えます。
今、内部ノードにおいて、もはや value を持つ事が不可能で、
子ノードをもたないリーフノードだけがvalueを持つことができるという状況です。
そこで、完全に一般化して、この key/value store に、'dog' と 'doge' のような　key　を同時に保存したいので、
他ノードへのパスとなる &quot;途中の&quot; 値が決して存在しないようにして終端記号(16)をアルファベットに対し追加します。
(つまり終端記号に格納されている値はパスでなく実際のvalueとなります)

kv node (アイテムが2つの配列`[ key, v]`) において, `v` は value または node です。

* `v` が value のとき、key はニブルが終端記号のフラグを **伴う** コンパクト符号化の戻り値です。
* `v` が node のとき、key はニブルが終端記号のフラグを **伴わない** コンパクト符号化の戻り値です。

diverge node (アイテムが17つの配列`[ v0 .... v15, vt ]`) において、

* v0 から v15 までのアイテムは常に node か 空白 です。
* vt は常に value か 空白 です。

v0...v15 には value が保存できないため、そうしたい場合、
終端記号フラグを伴う 空列 のコンパクト符号化の結果をkeyとするkvノード を代わりに保存します。

ここに Merkle Patricia tree における node 取得 の拡張コードを示します:

```py
    def get_helper(node,key):
        if key == []: return node
        if node = '': return ''
        curnode = rlp.decode(node if len(node) < 32 else db.get(node))
        if len(curnode) == 2:
            (k2, v2) = curnode
            k2 = compact_decode(k2)
            if k2 == key[:len(k2)]:
                return get(v2, key[len(k2):])
            else:
                return ''
        elif len(curnode) == 17:
            return get_helper(curnode[key[0]],key[1:])

    def get(node,key):
        key2 = []
        for i in range(len(key)):
            key2.push(int(ord(key) / 16))
            key2.push(ord(key) % 16)
        key2.push(16)
        return get_helper(node,key2)
```

Example: ('dog', 'puppy'), ('horse', 'stallion'), ('do', 'verb'), ('doge', 'coin') の４つのペアを含む
マークルパトリシア木があるとします。はじめに key を16進数形式に変換します:

    [ 6, 4, 6, 15, 16 ] : 'verb'
    [ 6, 4, 6, 15, 6, 7, 16 ] : 'puppy'
    [ 6, 4, 6, 15, 6, 7, 6, 5, 16 ] : 'coin'
    [ 6, 8, 6, 15, 7, 2, 7, 3, 6, 5, 16 ] : 'stallion'

さて、マークルパトリシア木を構築してみましょう:

    ROOT: [ '\x16', A ]
    A: [ '', '', '', '', B, '', '', '', C, '', '', '', '', '', '', '', '' ]
    B: [ '\x00\x6f', D ]
    D: [ '', '', '', '', '', '', E, '', '', '', '', '', '', '', '', '', 'verb' ]
    E: [ '\x17', F ]
    F: [ '', '', '', '', '', '', G, '', '', '', '', '', '', '', '', '', 'puppy' ]
    G: [ '\x35', 'coin' ]
    C: [ '\x20\x6f\x72\x73\x65', 'stallion' ]

ここで、node の参照は node の内部において（隠蔽されて）行われます。
その内包関数（隠蔽関数）は、`H(rlp.encode(x))` で与えられます。
ここで `H(x) = sha3(x) if len(x) >= 32 else x` となり (この式は python三項演算子 による記述です) 、また
 `rlp.encode` は [RLP](https://github.com/ethereum/wiki/wiki/%5BJapanese%5D-RLP) 符号化関数です

 trie を更新するとき、
永続的に探索できるテーブル（state データベース）として
 node の文字列の長さが32より大きい場合は、 (sha3(x), x) の key/value ペアを保存する必要があります。
しかし、それよりも小さい場合は何も保存する必要がありません。というのは、 (x, x) のペアは恒等関数であるので、
参照するまでもありません。
（上図においては、ROOT,A .. G に当たる部分が sha3(x) にあたり、その右にあるものをRLP符号化したものが x にあたります。）
