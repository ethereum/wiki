このチュートリアルはÐapp用Meteorアプリのセットアップ方法です。そしてこのチュートリアルは、「なぜMeteorが使われるべきなのか」という、いくつかの疑問にも答えることでしょう。

1. [マイÐappを作成](#マイÐappを作成)
2. [マイÐappを始めよう](#マイÐappを始めよう)
3. [マイÐappを接続](#マイÐappを接続)
4. [マイÐappを起動](#マイÐappを起動)
5. [Ðapp stylesを追加](#Ðapp stylesを追加)
6. [ethereum:elementsを使う](#use-ethereum-elements)
7. [Ðappコードの構造](#Ðappコードの構造)
8. [マイÐappをバンドル](#マイÐappをバンドル)


## FAQ

### Meteorはフルスタックフレームワークですよね？Ðapp開発にどうフィットしますか？

そのとおり。Meteorはフルスタックフレームワークで、そのメインの改良点といえばリアルタイムWebアプリケーションであるということです。しかし、Meteorはまた、完全なシングルページアプリケーション(SPA)開発を全面的に取り込み、全ての必要なツールを提供する最初のフレームワークでもあります。

Meteorが完璧にフィットする5つの理由:

1. ピュアなJSで書かれていて、SPAに必要な全てのツールを持っています。（テンプレートエンジン、モデル、on-the-flyコンパイリング、バンドリング）

2. ライブ・リロード、CSSインジェクションの機能を持ち、LESS・CoffeeScriptといった多くのプレコンパイラをサポートしている開発環境が手に入ります。

3. [meteor-build-client](https://github.com/frozeman/meteor-build-client)を使うことで、たった一枚の `index.html` と一つづつの `js` and `css` ファイル、それに加えてあなたのassetだけで、全てのフロントエンドのコードを手に入れることができます。そして、あなたはそれをどこにでもホスティングできますし、`index.html` 自体をシンプルに起動することもできますし、*swarm* 上で配布することもできます。

4. 完全なリアクティビティを持っており、一貫性のあるインターフェースをより簡単に作り上げます。（それはangular.jsの `$scope` や binding にも似ています）

5. Minimongoという素晴らしいモデルを持っています。それは、リアクティブなin-memoryデータベースのためのインターフェースのようなmongoDBです。それはまた、[localstorage](https://atmospherejs.com/frozeman/persistent-minimongo) ないし [indexedDB](https://atmospherejs.com/frozeman/persistent-minimongo2) に自動的に永続化する機能を持っています。

### マイÐappはサーバ上にホスティングする必要がありますか？

いいえ、 [meteor-build-client](https://github.com/frozeman/meteor-build-client) を使うことで、あなたのÐappをstaticなassetに変換し、サーバ無しで動かすことができるようになります。けれども、もしあなたが [iron-](https://atmospherejs.com/iron/router) や [flow-router](https://atmospherejs.com/meteorhacks/flow-router) のようなrouterを使うなら、あなたは HTML5のpushstate routesを使う代わりに、ハッシュ (`index.html#!/mypath`) routesを使う必要があります。

***

## マイÐappを作成

Meteorのインストールがまだならインストールして:

```bash
$ curl https://install.meteor.com/ | sh
```

Ðappプロジェクトを作りましょう:

```bash
$ meteor create myDapp
$ cd myDapp
```

次に web3 package を追加:

```bash
$ meteor add ethereum:web3
```

次のpackageの追加も推奨します:

- [ethereum:dapp-styles](https://atmospherejs.com/ethereum/dapp-styles) - 素晴らしいMistライクな見た目を提供する LESS/CSS framework。
- [ethereum:tools](https://atmospherejs.com/ethereum/tools) - Etherのための変換関数やテンプレートヘルパーを持つ `EthTools` オブジェクトを提供。
- [ethereum:elements](https://atmospherejs.com/ethereum/elements) - Ethereumのために特別に作られたUI要素。[デモ](http://ethereum-elements.meteor.com/)
- [ethereum:accounts](https://atmospherejs.com/ethereum/accounts) - 全ての利用可能なEthereumアカウントを持ったリアクティブな `EthAccounts` コレクション。Etherなどの残高が自動的に更新されます。
- [ethereum:blocks](https://atmospherejs.com/ethereum/blocks) - 最新50ブロックを持つリアクティブな `EthBlocks` コレクション。たとえば最新ブロックを取得するために `EthBlocks.latest` を使用できます。（それはまた最新のデフォルトgas価格も保持しています）
- [frozeman:template-var](https://atmospherejs.com/frozeman/template-var) - リアクティブ変数の利用が可能になる `TemplateVar` オブジェクト。テンプレートインスタンスに特化。[詳細](https://atmospherejs.com/frozeman/template-var)
- [frozeman:persistent-minimongo2](https://atmospherejs.com/frozeman/persistent-minimongo2) - minimongoコレクションをlocalstorageに自動的に永続化。


## マイÐappを始めよう

### ちょっと寄り道：Meteorのフォルダ構成
Meteorにはいくつか特別なフォルダがあって、あなたのアプリケーションのビルド時と起動時には特別な扱われ方をしますが、だからといって特別なフォルダ構成をあなたに強要するわけではありません。

特別な扱われ方をするフォルダ
- `client` - このフォルダのファイルはあなたのアプリのクライアント部分によってロードされます。Ðapp作成においてはファイルのほとんどがココに置かれるでしょう。
- `lib` - このフォルダは同じフォルダ内の他のファイルよりも先にロードされます。initファイルやライブラリ、もしくはEthereumの特別なファイルを置くのに適しています。
- `public` - MeteorにとってのDocumentRootのようなものです。
- 他にも `server`, `tests`, `packages` などといった特別なフォルダがいくつかあります。より深く知りたいなら [Meteor docs](http://docs.meteor.com/#/full/structuringyourapp) をご覧あれ。

というわけで、Ðappを作成するときには `myDapp` プロジェクトはこんなフォルダ構成が理想的です:

```
- myDapp
   - client
      - lib
      - myDapp.html
      - myDapp.js
      - myDapp.css
   - public
```

**Note** コミュニティではNick DodsonがMeteor Ðapp Boilerplatesを提供しています。 https://github.com/SilentCicero/meteor-dapp-boilerplate

### マイÐappを接続
dappを接続するためには、私達は正しいCORSヘッダを設定した `geth` をターミナルで起動する必要があります:

```bash
$ geth --rpc --rpccorsdomain "http://localhost:3000"
```

私達はまた、providerをセットする必要もあります。libフォルダに `init.js` というファイルを作り、次の行を追記すると良いでしょう:

```js
if(typeof web3 === 'undefined')
    web3 = new Web3(new Web3.providers.HttpProvider('http://localhost:8545'));
```

### マイÐappを起動

Ðappの起動はこんなにシンプルです:

```bash
$ meteor
```

`http://localhost:3000` にアクセスすると、ちょっとしたWebページが表示されます。そこでブラウザコンソールを開けば、gethノードにクエリを実行するための web3 オブジェクトを使うことができます:

```js
> web3.eth.accounts
['0xfff2b43a7433ddf50bb82227ed519cd6b142d382']
```

## Ðapp stylesを追加

ÐappのスタイルをMistや公式に合わせたいのなら、 [dapp-styles css css/less framework](https://atmospherejs.com/ethereum/dapp-styles) を使うべきです。

*Note それらは頻繁にアップデートされているのでclass名や要素は変わるかもしれません*

Ðapp stylesを使うためにやることは、単純に次のパッケージを追加するだけです:

```bash
$ meteor add less
$ meteor add ethereum:dapp-styles
```

さて、 `myDapp.css` を `myDapp.less` に変更し、次の行を追加しましょう:

```css
// libs
@import '{ethereum:dapp-styles}/dapp-styles.less';
```

これであなたはdapp-stylesのclassを使ったり、フレームワークの全てのstyleを上書きすることさえできます。全体は [in the repo](https://github.com/ethereum/dapp-styles/blob/master/constants.import.less) をご覧ください。あなたの `myDapp.less` ファイルにそれらをコピーして上書きし、値を変更してみましょう。

<a name="use-ethereum-elements"></a>
## ethereum:elementsを使う

皆さんがÐapp開発者になるための敷居を下げるべく、私達はÐappの作成をより速く行うことを手助けするためのいくつかのパッケージを提供しています。

上記で推奨したパッケージを追加するなら、こちらのパッケージも使うと良いでしょう:

- [ethereum:tools](https://atmospherejs.com/ethereum/tools)
- [ethereum:accounts](https://atmospherejs.com/ethereum/accounts)
- [ethereum:blocks](https://atmospherejs.com/ethereum/blocks)

これら3つのパッケージで `EthTools`, `EthAccounts` そして `Ethblocks` オブジェクトが使えるようになります。それらは、フォーマッター関数や `web3.eth.accounts` (自動更新される残高) のアカウントを持ったコレクション、そして最新50ブロックのコレクションを提供します。

これらの関数のほとんどはリアクティブなので、インターフェース開発は順調に進むことでしょう。

### 使用例

`myDapp.html` を見てみると、そこには `hello` というtemplateがあることに気づくでしょう。
`<template name="hello">..</template>` 内のどこかに `{{currentBlock}}` というヘルパーを追加してください。
そして `myDapp.js` を開いて、 `currentBlock` ヘルパーの `counter: function..` の後に次のようにコードを追加してください:

```js
Template.elements.helpers({
    counter: function () {
      ...
    },
    currentBlock: function(){
        return EthBlocks.latest.number;
    }
  });
```

それから、 `Session.setDefault('counter', 0);` の後に `EthBlocks.init();` を追加することでEthBlockを初期化してください。

今あなたのDappをブラウザでチェックすると、最新のブロック番号が見られるでしょう。その番号はあなたが採掘を開始すると増えていきます。

*詳しいサンプルは、パッケージのreadmeや [demo](http://ethereum-elements.meteor.com) ([source](https://github.com/frozeman/meteor-ethereum-elements-demo)) をご覧ください*

## Ðappコードの構造

*Meteorアプリをもっと知りたいなら、こちらを参照してください。
[Meteor's tutorials](https://www.meteor.com/tutorials/blaze/creating-an-app)
[A list of good resources](https://www.meteor.com/tools/resources)
[EventMinded](https://www.eventedmind.com) (payed tutorials)
[Building Single-page Web Apps with Meteor](https://www.packtpub.com/web-development/building-single-page-web-apps-meteor)
[Discover Meteor](http://discovermeteor.com).*

TODO
箇条書き:
- `client/lib/ethereum/somefile.js` にEthereum関係のツールを置く
- Ethereumと通信するために `myCollection.observe({added: func, changed: func, removed: func})` を使い、できるだけEthereumロジックと切り離しましょう。こうすることで、あなたはリアクティブ・コレクションからの読み書きに専念でき、Observe関数が残りを処理してくれます。 (e.g. sendTransactions)
- Filterなどはあなたのコレクションにログなどを追加してくれ、あなたのアプリケーション・ロジックをコールバック地獄から解放するでしょう。

例はこちら [Ethereum-Wallet](https://github.com/ethereum/meteor-dapp-wallet).

## マイÐappをバンドル

あなたのDappをローカルのスタンドアロンなファイルに束ねるために、[meteor-build-client](https://github.com/frozeman/meteor-build-client) を使いましょう:

```bash
$ npm install -g meteor-build-client
$ cd myDapp
$ meteor-build-client ../build --path ""
```

これはあなたのDappの静的ファイルをmyDappフォルダの上のbuildフォルダの中に生成します。

最後のオプションである  `--path` は全てのファイルのリンクを相対的にしてくれます。つまりあなたが `build/index.html` をクリックすることで簡単にアプリをスタートできるようになるということです。

あなたのアプリが `file://` プロトコルで動いていることに気をつけてください。Webセキュリティのためにあなたはクライアントサイドのルーティングを使うことはできません。いずれはDappが `eth://` プロトコルを通して提供されることで、Mist上でクライアントサイドのルーティングは使えるようになるでしょう。

将来、あなたは簡単にあなたのDappをswarm上にアップロードすることができるようになるでしょう。