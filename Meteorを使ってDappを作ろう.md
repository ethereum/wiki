このチュートリアルはÐapp用Meteorアプリのセットアップ方法です。そしてこのチュートリアルは、「なぜMeteorが使われるべきなのか」という、いくつかの疑問にも答えることでしょう。

1. [自分のÐappを作成](#create-your-%C3%90app)
2. [自分のÐappをスタート](#start-your-%C3%90app)
3. [自分のÐappに接続](#connect-your-%C3%90app)
4. [自分のÐappを起動](#run-your-%C3%90app)
5. [Ðapp stylesを追加](#add-%C3%90app-styles)
6. [ethereum:elementsを使う](#using-ethereumelements)
7. [Ðappコードの構造](#%C3%90app-code-structure)
8. [自分のÐappをバンドル](#bundle-your-%C3%90app)


## FAQ

### Meteorはフルスタックフレームワークですよね？Ðapp開発にどうフィットしますか？

そのとおり。Meteorはフルスタックフレームワークで、そのメインの改良点といえばリアルタイムWebアプリケーションであるということです。しかし、Meteorはまた、完全なシングルページアプリケーション(SPA)開発を全面的に取り込み、全ての必要なツールを提供する最初のフレームワークでもあります。

Meteorが完璧にフィットする5つの理由:

1. ピュアなJSで書かれていて、SPAに必要な全てのツールを持っています。（テンプレートエンジン、モデル、on-the-flyコンパイリング、バンドリング）

2. ライブ・リロード、CSSインジェクションの機能を持ち、LESS・CoffeeScriptといった多くのプレコンパイラをサポートしている開発環境が手に入ります。

3. [meteor-build-client](https://github.com/frozeman/meteor-build-client)を使うことで、たった一枚の `index.html` と一つづつの `js` and `css` ファイル、それに加えてあなたのassetだけで、全てのフロントエンドのコードを手に入れることができます。そして、あなたはそれをどこにでもホスティングできますし、`index.html` 自体をシンプルに起動することもできますし、*swarm* 上で配布することもできます。

4. 完全なリアクティビティを持っており、一貫性のあるインターフェースをより簡単に作り上げます。（それはangular.jsの `$scope` や binding にも似ています）

5. Minimongoという素晴らしいモデルを持っています。それは、リアクティブなin-memoryデータベースのためのインターフェースのようなmongoDBです。それはまた、[localstorage](https://atmospherejs.com/frozeman/persistent-minimongo) ないし [indexedDB](https://atmospherejs.com/frozeman/persistent-minimongo2) に自動的に永続化する機能を持っています。

### 自分のÐappはサーバ上にホスティングする必要がありますか？

いいえ、 [meteor-build-client](https://github.com/frozeman/meteor-build-client) を使うことで、あなたのÐappをstaticなassetに変換し、サーバ無しで動かすことができるようになります。けれども、もしあなたが [iron-](https://atmospherejs.com/iron/router) や [flow-router](https://atmospherejs.com/meteorhacks/flow-router) のようなrouterを使うなら、あなたは HTML5のpushstate routesを使う代わりに、ハッシュ (`index.html#!/mypath`) routesを使う必要があります。

***

## Create your Ðapp

Install Meteor if don't have already:

```bash
$ curl https://install.meteor.com/ | sh
```

Then create an app:
```bash
$ meteor create myDapp
$ cd myDapp
```

Next add the web3 package:
```bash
$ meteor add ethereum:web3
```

I recommend also to add the following packages:

- [ethereum:dapp-styles](https://atmospherejs.com/ethereum/dapp-styles) - The LESS/CSS framework which gives your dapp a nice Mist-consistent look.
- [ethereum:tools](https://atmospherejs.com/ethereum/tools) - This package gives you the `EthTools` object with a set of formatting an conversion functions and template helpers for ether.
- [ethereum:elements](https://atmospherejs.com/ethereum/elements) - A set of interface elements specifically made for ethereum, see this [Demo](http://ethereum-elements.meteor.com) for more.
- [ethereum:accounts](https://atmospherejs.com/ethereum/accounts) - Gives you the reactive `EthAccounts` collection with all current available ethereum accounts, where balances will be automatically updated.
- [ethereum:blocks](https://atmospherejs.com/ethereum/blocks) - Gives you the reactive `EthBlocks` collection with the latest 50 blocks. To get the lastest block use `EthBlocks.latest` (It will also have the latest default gasPrice)
- [frozeman:template-var](https://atmospherejs.com/frozeman/template-var) - Gives you the `TemplateVar` object, that allows you to set reactive variables, which are template instance specific. See the [readme](https://atmospherejs.com/frozeman/template-var) for more.
- [frozeman:persistent-minimongo2](https://atmospherejs.com/frozeman/persistent-minimongo2) - Allows you to auto persist your minimongo collection in local storage


## Start your Ðapp

### A short excursion into Meteors folder structure

Meteor doesn't force you to have a specific folder structure, though some folders have specific meaning and will be treated differently when bundling/running your application.

Folders with specific treatment
- `client` - files in a folder called `client` will only be loaded by the client part of your app and as we are building a Ðapp, that's where most of our files go.
- `lib` - files in folders called `lib` will load before other files in the same folder. This is an ideal place your init files, libraries, or ethereum specific files.
- `public` - a folder called `public` contains assets meteor will make available on the root of your webserver (or later bundled Ðapp)
- There are a few more specific folders like `server`, `tests`, `packages`, etc. If you want to get to know them take a look at the [Meteor docs](http://docs.meteor.com/#/full/structuringyourapp)

So to build a Ðapp we ideally create the following folder structure in our `myDapp` folder:

```
- myDapp
   - client
      - lib
      - myDapp.html
      - myDapp.js
      - myDapp.css
   - public
```

**Note** The community provides also Meteor Ðapp Boilerplates like this one from Nick Dodson: https://github.com/SilentCicero/meteor-dapp-boilerplate

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

## Using ethereum packages

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