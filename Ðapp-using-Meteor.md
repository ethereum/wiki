This tutorial will show you how to setup a Meteor app to be used as a Ðapp and probably answer a few questions on why Meteor should used.

## FAQ

### Isn't Meteor a full stack framework, how does that fit into Ðapp development

True, Meteor is a full stack framework and its main improvement is realtime web applications, but Meteor is also the first framework (i know of), which fully embraced *s*ingle *p*age *a*pp (SPA) development and provided all necessary tools.

5 reasons why Meteor is a perfect fit:

1. Its purely written in JS and has all the tools a SPA needs (Templating engine, Model, on-the-fly compiling, bundling)
2. You get a development environment, which has live reload, CSS injection and support for many pre-compilers (LESS, Coffeescript, etc) out of the box
3. You can get all frontend code as single `index.html` with one `js` and `css` file plus your assets, using [meteor-build-client](https://github.com/frozeman/meteor-build-client). You can then host it everywhere or simple run the `index.html` itself or distribute it later on *swarm*.
4. It embraces full reactivity, which make building consistent interface much easier (similar to angualr.js `$scope` or binding)
5. It has a great model called Minimongo, which gives you a mongoDB like interface for a reactive in-memory database, which can also be [auto-persistet to localstorage](https://atmospherejs.com/frozeman/persistent-minimongo)

### Do i need to host my Ðapp on a server?

No, using [meteor-build-client](https://github.com/frozeman/meteor-build-client) you can get all the static assets of you Ðapp to run without any server, though if you use a router like [iron-](https://atmospherejs.com/iron/router) or [flow-router](https://atmospherejs.com/meteorhacks/flow-router), you need to use hash (`index.html#!/mypath`) routes instead of clean HTML5 pushstate routes.

***

## creating a Ðapp

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

- [ethereum:tools](https://atmospherejs.com/ethereum/tools) - This package gives you the `EthTools` object with a set of formatting an conversion functions and template helpers for ether.
- [ethereum:elements](https://atmospherejs.com/ethereum/elements) - A set of interface elements specifically made for ethereum, see this [Demo](http://ethereum-elements.meteor.com) for more.
- [ethereum:accounts](https://atmospherejs.com/ethereum/accounts) - Gives you the reactive `EthAccounts` collection with all current available ethereum accounts, where balances will be automatically updated.
- [ethereum:blocks](https://atmospherejs.com/ethereum/blocks) - Gives you the reactive `EthBlocks` collection with the latest 50 blocks. To get the lastest block use `EthBlocks.latest` (It will also have the latest default gasPrice)
- [frozeman:template-var](https://atmospherejs.com/frozeman/template-var) - Gives you the `TemplateVar` object, that allows you to set reactive variables, which are template instance specific. See the [readme](https://atmospherejs.com/frozeman/template-var) for more.
- [frozeman:persistent-minimongo](https://atmospherejs.com/frozeman/persistent-minimongo) - Allows you to auto persist your minimongo collection in local storage

## Adding basics to your Ðapp

// Add folder structure expl, and set web3 provider, start geth

## Using ethereum:elements

## Bundling you Ðapp

