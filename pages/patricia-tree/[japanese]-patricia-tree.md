---
name: Patricia Tree
category: 
---

### Merkle Patricia Tree の仕様

Merkle Patricia treeは、（キー、値）のすべての結合を保存することのできる、暗号学的な認証データ構造を与えるが, この資料の視点として、キーと値は文字列に制限する（この制限を解除するには、他のデータ種に対し直列化方式を用いるだけでよい)。 これらは、完全に決定的で、つまり、 同じ（キー、値）の結合は全く同じバイト末に至ることを保証し、故に同じroot hashを持ち、挿入、検索、削除にO(log(n))の効率の聖杯を与え, red-block treeのような、もっと複雑な比較ベースの代替よりずっと簡単に理解できる。

### 序文: 基本的なRadix Trees

基本的なradix tree（基数木）では、すべてのノードは、次のようである:

    [ value, i0, i1 ... in]

i0 ... は、アルファベットの（しばしばバイナリか16進での）シンボルを表現する。 valueは、ノードの終端の値で, i0 ... の値はNULLか、他のノードへのポインタ(我々のケースでは、ハッシュ値）である。 この形式は、基本的な（キー、値）の保存である; 例えば、もしtree中で現在dogにマッピングされている値に興味があるなら、まずdogをアルファベット（もし16進を使うなら646f67となる)に変換し、そのパスを、終わりになるまでずっと降りて行き、値を読む。すなわち、根のノードを得るために、まずキー/値の記録の根のハッシュを調べる,  そして1レベル下のノードを得るために、根のノード6を調べる, そして、そのノード４を調べる, そして、そのノード6を調べる, などなど,根 -> 6 -> 4 -> 6 -> f -> 6 -> 7のパスを一度たどるまで行い, 得たノードの値を調べ、その値を返す。 

radix treeの更新と削除は、簡単で、大まかには次のように定義される:

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

radix treeの"Merkle"部分は、C（訳注：C言語）で、伝統的にtreeの実装で起こるであろう、32ビットや64ビットのメモリの場所ではなく、ノードの決定的な暗号学的ハッシュがノードのポインタとして使われることに起因している 。このことで、データ構造への暗号学的な認証の方法が得られる; もし、与えられたtrieの根のハッシュが公に知られれば、（訳注：(キー・値)ノードから、根までの？）道の各ステップを上がっていくノードを提供することによりtrieが特定のキーに、与えられた値をもつことを、証明することがだれでも可能である。根のハッシュは、その下のすべてのハッシュに究極的には基づいており、故に、どのような変更でも、根のハッシュが変わるので、攻撃者が存在しない（キー、値）の対の証明を与えることは不可能である。

しかし、radix treesには、重要な制限がある: 効率性である。 もし、キーが数百文字の長さのたった１つの（キー、値）の結合を保存する場合, 1文字1レベルを保存するのに別の１キロバイト以上のスペースが必要となる, そして、それぞれの検索と削除は、数百ステップとなる。Patricia treeはこの問題を解決するために導入された。

### 仕様: 終端オプションありの16進Compact encoding

１６進文字列の伝統的なエンコード方法は、バイナリに変換することである - つまり、0f1248のような文字列は、[15, 18, 72] の３バイトである。しかし、この方法は１つの小さな問題がある: １６進文字列の長さが奇数だったら? このケースでは、0f1248とf1248を区別する方法がない。 加え、 Merkle Patricia treeの我々の適用では、１６進文字列が終わりに特別な  &quot;終端記号&quot;('T'と表示される)をもつ１６進文字列という、追加の特徴が必要である。 終端記号は、１度のみ、唯一最後に発生しうる。このことを考える上での代替の手段は、終端器号があることを考えず、その代わり、 終端器号の存在を示すビットを、与えられたノードが最終のノード、ここでの値は、他のノードのハッシュではなく、本当の値である、をエンコードしたことを示すビットとして扱うことである, 

これら問題を解決するため、 最後のバイトストリームの最初のニブル（4ビット）に対し、長さ('T'記号は無視)が奇数かどうかを示すものと、 終端の状態の、２つのフラグをエンコードする; それらは、それぞれ、最初のニブルの２つの最下位ビットに置かれる。 偶数の長さの16進文字列であれば, 16進文字列の長さが偶数であり、それ故バイトの全数が表現可能であることを保証するため、2番めのニブル（値はゼロ）を導入しなければならない。故に、次のエンコーディングを構築する:

    def compact_encode(hexarray):
        term = 1 if hexarray[-1] == 16 else 0
        if term: hexarray = hexarray[:-1]
        oddlen = len(hexarray) % 2
        flags = 2 * term + oddlen
        if oddlen:
            hexarray = [flags] + hexarray
        else:
            hexarray = [flags] + [0] + hexarray
        // hexarrayは最初のニブルがフラグとなった偶数の長さとなっている。
        o = ''
        for i in range(0,len(hexarray),2):
            o += chr(16 * hexarray[i] + hexarray[i+1])
        return o

例:

    ＞ [ 1, 2, 3, 4, 5 ]
    '\x11\x23\x45'
    ＞ [ 0, 1, 2, 3, 4, 5 ]
    '\x00\x01\x23\x45'
    ＞ [ 0, 15, 1, 12, 11, 8, T ]
    '\x20\x0f\x1c\xb8'
    ＞ [ 15, 1, 12, 11, 8, T ]
    '\x3f\x1c\xb8'

### メイン仕様: Merkle Patricia Tree

Merkle Patricia tree は、データ構造にいくつかの特別な複雑さを加えることにより、非効率な問題を解決する。Merkle Patricia treeのノードは、次のうちの1つである:

1. NULL (空文字を表現する)
2. 2項目の配列 [ キー,値 ]
3. 17項目の配列 [ v0 ... v15, vt ]

このアイデアは、ただ１つの要素をもつ長いノードのパスが発生した場合、[キー、値]ノードを作ることで下降をショートカットすることである、ここで、キーは上で述べたcompact encodingされた、下降する１６進のパスを与え、値は、標準のradix treeのようにただのノードのハッシュである。また、もうひとつ概念の変更を加える: 内部ノードは、もう値をもてない、ただ子を持たない葉のみが、値をもつことができる; しかし、完全に一般的に、キー／値の保存を'dog' と 'doge' を同時に保存できるようにしたいので、単純に終端器号(16)をアルファベットに追加する、そうすることで決してある値が別の値に&quot;立ち寄る&quot;ことがない。 ノードがノード内で参照される場合、もしlen(x) >= 32なら、H(rlp.encode(x)) ここで、H(x) = sha3(x) 、さもなければ xが含まれる 、そしてrlp.encode は [RLP](https://github.com/ethereum/wiki/wiki/%5BJapanese%5D-RLP) エンコード関数である。trieを更新する際,長さ>=32のノードを作成する際、永続的なルックアップテーブルにキー/値のペア (sha3(x), x) を保存せねばならない、しかし、もしノードがそれより短ければ、関数f(x) = xが可逆であるという明らかな理由により、何も保存する必要はない。 

ここで、２項目配列 `[ key, v ]`, `v` は、値またはサブノードでありうる、を考える。
* もし `v` が値なら、キーは終端を**含む**ニブルのリストの compact encodingの結果でなければならない。
* もし `v` がサブノードなら、キーは終端を**含まない**ニブルのリストの compact encodingの結果でなければならない。

17項目配列` [ v0 ... v15, vt ]`では, `v0...v15`のそれぞれのアイテムは常にサブノードまたはブランクであり, vtは、常に値かブランクである。故に、`v0...v15`の1アイテムに値を記録する代わりに、（キー、値）のサブノードを保存する、ここでキーは、終端を**含んだ**空のニブルのリストをcompact encodingした結果である。 

これが、 Merkle Patricia tree中のノードを取得するための、拡張したコードである:

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

例： ペア('dog', 'puppy'), ('horse', 'stallion'), ('do', 'verb'), ('doge', 'coin')を含むtreeを持つことを想定する。まず、キーを16進形式へ変換する:

    [ 6, 4, 6, 15, 16 ] : 'verb'
    [ 6, 4, 6, 15, 6, 7, 16 ] : 'puppy'
    [ 6, 4, 6, 15, 6, 7, 6, 5, 16 ] : 'coin'
    [ 6, 8, 6, 15, 7, 2, 7, 3, 6, 5, 16 ] : 'stallion'

そして、treeを構築する:

    ROOT: [ '\x16', A ]
    A: [ '', '', '', '', B, '', '', '', C, '', '', '', '', '', '', '', '' ]
    B: [ '\x00\x6f', D ]
    D: [ '', '', '', '', '', '', E, '', '', '', '', '', '', '', '', '', 'verb' ]
    E: [ '\x17', F ]
    F: [ '', '', '', '', '', '', G, '', '', '', '', '', '', '', '', '', 'puppy' ]
    G: [ '\x35', 'coin' ]
    C: [ '\x20\x6f\x72\x73\x65', 'stallion' ]
