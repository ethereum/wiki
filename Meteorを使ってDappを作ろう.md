このチュートリアルはÐapp用Meteorアプリのセットアップ方法です。そしてこのチュートリアルは、「なぜMeteorが使われるべきなのか」という、いくつかの疑問にも答えることでしょう。

1. [マイÐappを作成](#マイÐappを作成)
2. [マイÐappを始めよう](#マイÐappを始めよう)
3. [マイÐappに接続](#マイÐappに接続)
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

### Connect your Ðapp
To connect our dapp we need to start `geth` with the right CORS headers in another terminal:

```bash
$ geth --rpc --rpccorsdomain "http://localhost:3000"
```

We also need to set the provider. Ideally we create a file in our lib folder called `init.js` and add the following line:

```js
if(typeof web3 === 'undefined')
    web3 = new Web3(new Web3.providers.HttpProvider('http://localhost:8545'));
```

### Run your Ðapp

Now we can run our Ðapp by simply running:

```bash
$ meteor
```

If we go to `http://localhost:3000`, we should see a website appear and if we open the browser console we can use the web3 object to query the geth node:

```js
> web3.eth.accounts
['0xfff2b43a7433ddf50bb82227ed519cd6b142d382']
```

## Add Ðapp styles

If you want your Ðapp to nicely fit later into Mist and have follow the official look use the [dapp-styles css css/less framework](https://atmospherejs.com/ethereum/dapp-styles).

*Note that they are under heavy development and the class names and elements may change.*

To add it simple add the following packages to your Ðapp:

```bash
$ meteor add less
$ meteor add ethereum:dapp-styles
```

Now rename you `myDapp.css` to `myDapp.less` and add the following line inside:

```css
// libs
@import '{ethereum:dapp-styles}/dapp-styles.less';
```

Now you can use all dapp-styles classes and also overwrite all variables of the framework. You can find them [in the repo](https://github.com/ethereum/dapp-styles/blob/master/constants.import.less). Overwrite them by copying them to your `myDapp.less` file and set different values.

<a name="use-ethereum-elements"></a>
## ethereum:elementsを使う

To make your life as a Ðapp developer easier we provide some packages that help you build Ðapps faster.

If you add the recommended packages above you should have the [ethereum:tools](https://atmospherejs.com/ethereum/tools), [ethereum:accounts](https://atmospherejs.com/ethereum/accounts) and [ethereum:blocks](https://atmospherejs.com/ethereum/blocks) packages available.

These 3 packages give you the `EthTools`, `EthAccounts` and `Ethblocks` objects, which give you formatter functions,  a collection with the accounts from `web3.eth.accounts` (with auto updated balance) and a collection of the last 50 blocks.

Most of these functions are reactive so they should make building interfaces a breeze.

### Example usage

If you look into you `myDapp.html` you will find the `hello` template.
Just add a helper called `{{currentBlock}}` some where between the `<template name="hello">..</template>` tags.

Now open the `myDapp.js` and add after the `counter: function..` the `currentBlock` helper:
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

Then initialize EthBlocks by adding `EthBlocks.init();` after `Session.setDefault('counter', 0);`

If you now check your Ðapp in the browser you should see the latest block number, which will increase once you mine.


*For more examples please checkout the packages readmes and the [demo](http://ethereum-elements.meteor.com) ([source](https://github.com/frozeman/meteor-ethereum-elements-demo)) for more.*

## Ðapp code structure

*This tutorial won't go into building apps with Meteor. For this please refer to the [Meteor's tutorials](https://www.meteor.com/tutorials/blaze/creating-an-app), [A list of good resources](https://www.meteor.com/tools/resources), [EventMinded](https://www.eventedmind.com) (payed tutorials) or books like [Building Single-page Web Apps with Meteor](https://www.packtpub.com/web-development/building-single-page-web-apps-meteor) or [Discover Meteor](http://discovermeteor.com).*


TODO
Short:
- put ethereum related stuff into `client/lib/ethereum/somefile.js`
- use `myCollection.observe({added: func, changed: func, removed: func})` to communicate to ethereum, keep ethereum logic out of your app as much as possible. This way you just write and read from your reactive collections and the observe functions will handle the rest (e.g. sendTransactions)
- Filters etc will add logs etc to your collections. So you keep all the callback mess out of your app logic.

For an example see the [Ethereum-Wallet](https://github.com/ethereum/meteor-dapp-wallet).

## Bundle your Ðapp

To bundle your Ðapp into a local standalone file use [meteor-build-client](https://github.com/frozeman/meteor-build-client):

```bash
$ npm install -g meteor-build-client
$ cd myDapp
$ meteor-build-client ../build --path ""
```

This will put your Ðapps static files into the build folder, above your `myDapp` folder.

The last option `--path` will make the linking of all files relative, allowing you to start the app by simply clicking the `build/index.html`.

Be aware that when running your app on the `file://` protocol, you won't be able to use client side routing, due to web security. Later in mist you will be able to use client side routing, as dapps are served over the `eth://` protocol.

In the future you will be able to simply upload your Ðapp on swarm.