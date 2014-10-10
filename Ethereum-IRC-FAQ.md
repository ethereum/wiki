Answers to questions about #Ethereum on [Freenode IRC](https://freenode.net/)

<!-- Generated with marked-toc https://github.com/jonschlinkert/marked-toc -->
<!-- toc -->

* [Ethereum](#ethereum)
  * [Where can I learn more about Ethereum?](#where-can-i-learn-more-about-ethereum)
* [IRC](#irc)
  * [How can I join the Ethereum IRC channels?](#how-can-i-join-the-ethereum-irc-channels)
  * [Where can I find the Ethereum IRC logs?](#where-can-i-find-the-ethereum-irc-logs)
  * [Where can I learn about the ZeroGox bot?](#where-can-i-learn-about-the-zerogox-bot)
* [Clients](#clients)
  * [Where can I find official releases?](#where-can-i-find-official-releases)
  * [How to install development builds?](#how-to-install-development-builds)
  * [How to install the clients from source?](#how-to-install-the-clients-from-source)
* [Mining](#mining)
  * [How can I mine Ether?](#how-can-i-mine-ether)
  * [How to get free testnet Ether?](#how-to-get-free-testnet-ether)
* [Contracts](#contracts)
  * [Where can I learn about contract development?](#where-can-i-learn-about-contract-development)
  * [Where can I learn Serpent?](#where-can-i-learn-serpent)
  * [Where can I learn LLL?](#where-can-i-learn-lll)
  * [Where can I learn Mutan?](#where-can-i-learn-mutan)
  * [Where can I learn Solidity?](#where-can-i-learn-solidity)
  * [How to test contracts?](#how-to-test-contracts)
  * [How to deploy contracts automatically?](#how-to-deploy-contracts-automatically)
  * [Where to find example contracts?](#where-to-find-example-contracts)
* [ÐApps](#Ðapps)
  * [Where can I learn about the Ethereum APIs?](#where-can-i-learn-about-the-ethereum-apis)
  * [Where can I learn about ÐApp development?](#where-can-i-learn-about-Ðapp-development)
  * [Where can I find ÐApp development tools?](#where-can-i-find-Ðapp-development-tools)
  * [Where can I find example ÐApps?](#where-can-i-find-example-Ðapps)

<!-- toc stop -->

## Ethereum

### Where can I learn more about Ethereum?

Sites

+ [Ethereum.org](https://ethereum.org)
+ [Ethereum Forums](https://forum.ethereum.org)
+ [Ethereum Wiki](https://github.com/ethereum/wiki/wiki)
+ [Ethereum FAQ](https://github.com/ethereum/wiki/wiki/FAQ)

Papers

+ [The White Paper][WhiteHTML] ([HTML][WhiteHTML], [PDF][WhitePDF])
+ [The Yellow Paper][YellowPDF]

Projects

+ [cpp-ethereum](https://github.com/ethereum/cpp-ethereum/) ([@gavofyork](https://github.com/gavofyork), [@programmerTim](https://github.com/programmerTim), [@caktux](https://github.com/caktux))
+ [go-ethereum](https://github.com/ethereum/go-ethereum) ([@obscuren](https://github.com/obscuren), [@maran](https://github.com/maran))
+ [pyethereum](https://github.com/ethereum/pyethereum) ([@vbuterin](https://github.com/vbuterin), [@heikoheiko](https://github.com/heikoheiko), [@chenhouwu](https://github.com/chenhouwu))
+ [ethereumj](https://github.com/ethereum/ethereumj) ([@romanman](https://github.com/romanman), [@nicksavers](https://github.com/nicksavers))
+ [ethereumjs-lib](https://github.com/ethereum/ethereumjs-lib) ([@ethers](https://github.com/ethers), [@wanderer](https://github.com/wanderer))

[WhitePDF]: https://www.ethereum.org/pdfs/EthereumWhitePaper.pdf
[WhiteHTML]: https://github.com/ethereum/wiki/wiki/White-Paper
[YellowPDF]: http://gavwood.com/paper.pdf

## IRC

### How can I join the Ethereum IRC channels?

+ [Chat with the ethereum dev community on IRC!](https://forum.ethereum.org/discussion/1495/chat-with-the-ethereum-dev-community-on-irc)

### Where can I find the Ethereum IRC logs?

+ [The ZeroGox logs](https://zerogox.com/bot/log)

### Where can I learn about the ZeroGox bot?

+ [The ZeroGox bot](https://zerogox.com/bot)

## Clients

### Where can I find official releases?

+ [Releases for AlethZero](https://github.com/ethereum/cpp-ethereum/releases)
+ [Releases for Mist](https://github.com/ethereum/go-ethereum/releases)
+ [Releases for Pyethereum](https://github.com/ethereum/pyethereum/releases)

### How to install development builds?

+ Homebrew
  + [Homebrew Ethereum](https://github.com/caktux/homebrew-ethereum) ([@caktux](https://github.com/caktux))
+ Guides
  + [AlethZero super easy install guide for OSX](https://forum.ethereum.org/discussion/1388/alethzero-super-easy-install-guide-for-osx) ([@stephantual](https://github.com/stephantual))
  + [Go-Ethereum simple build guide for OSX](http://forum.ethereum.org/discussion/905/go-ethereum-cli-ethereal-simple-build-guide-for-osx-now-with-one-line-install) ([@stephantual](https://github.com/stephantual))
+ Builds
  + [Ethdev Buildbot](http://build.ethdev.com/waterfall)

### How to install the clients from source?

+ [Building AlethZero (C++)](https://github.com/ethereum/cpp-ethereum/wiki)
+ [Building Mist (Go)](https://github.com/ethereum/go-ethereum/wiki/Building-Ethereum%28Go%29)
+ [Installing Pyethereum (Python)](https://github.com/ethereum/pyethereum#quickstart)
+ [Installing EthereumJ (Java)](https://github.com/ethereum/ethereumj#maven)
+ [Installing Ethereumjs-lib (JavaScript for Browser and Node)](https://github.com/ethereum/ethereumjs-lib#install)

## Mining

### How can I mine Ether?

With AlethZero

+ To process transactions
  + Disable "Debug" > "Force Mining"
  + Click "Mine"
+ To force mine (Use sparingly, unless stress testing)
  + Enable "Debug" > "Force Mining"
  + Click "Mine"

With the eth client

```
# Only force mine to acquire ether or stress test
$ eth --force-mining --mining on [YOUR OPTIONS...]
```

### How to get free testnet Ether?

+ [ZeroGox Wei Faucet](https://zerogox.com/ethereum/wei_faucet) ([@caktux](https://github.com/caktux))

## Contracts

### Where can I learn about contract development?

+ Articles
  + [Ethereum Development Tutorial](https://github.com/ethereum/wiki/wiki/Ethereum-Development-Tutorial)
+ Videos
  + [Ethereum](https://www.youtube.com/user/ethereumproject/videos)
  + [EtherCasts](https://www.youtube.com/user/EtherCasts/videos)

### Where can I learn Serpent?

+ Specifications
  + [The Serpent Language](https://github.com/ethereum/wiki/wiki/Serpent)
+ Examples
  + [Vitalik's Serpent examples](https://github.com/ethereum/serpent/tree/master/examples)
+ Tutorials
  + [Pyethereum and Serpent Programming Guide](https://blog.ethereum.org/2014/04/10/pyethereum-and-serpent-programming-guide/)
+ Videos
  + [Learn Ethereum with Vitalik](https://www.youtube.com/watch?v=nXYDfLCLmMs)

### Where can I learn LLL?

+ Specifications
  + [The LLL Language](https://github.com/ethereum/cpp-ethereum/wiki/LLL-PoC-6)
+ Examples
  + [LLL examples for PoC 6](https://github.com/ethereum/cpp-ethereum/wiki/LLL%20Examples%20for%20PoC%205)
+ Videos
  + [Programming Society with Asm](https://www.youtube.com/watch?v=xO1AxsYAkU8)

### Where can I learn Mutan?

+ Specifications
  + [The Mutan Language](https://github.com/ethereum/go-ethereum/wiki/Mutan-0.6)
+ Examples
  + [Mutan 0.6 Examples](https://github.com/ethereum/go-ethereum/wiki/Mutan-0.6-Examples)
+ Videos
  + [Writing Smart Contracts with Mutan and Go Ethereum](https://www.youtube.com/playlist?list=PLJqWcTqh_zKFQy2LlvkzHR2cEEMTvUWj8)

### Where can I learn Solidity?

+ Specifications
  + [Solidity, Docs and ABI](https://github.com/ethereum/cpp-ethereum/wiki/Solidity%2C-Docs-and-ABI)

### How to test contracts?

+ [pyethereum.tester](https://github.com/ethereum/pyethereum/blob/master/tests/test_contracts.py)

### How to deploy contracts automatically?

+ [Ethereum Package Manager](https://github.com/project-douglas/epm)

### Where to find example contracts?

+ Serpent
  + [By Vitalik Buterin](https://github.com/ethereum/serpent/tree/master/examples) ([@vbuterin](https://github.com/vbuterin))
  + [By Rob Myers](https://github.com/robmyers/artworld-ethereum) ([@robmyers](https://github.com/robmyers))
  + [By Tyler Florez](https://github.com/qualiabyte/ethereum-contracts) ([@qualiabyte](https://github.com/qualiabyte))
+ LLL
  + [By Gavin Wood](https://github.com/ethereum/cpp-ethereum/wiki/LLL%20Examples%20for%20PoC%205) ([@gavofyork](https://github.com/gavofyork))
  + [By Dennis Mckinnon](https://github.com/dennismckinnon/Ethereum-Contracts) ([@dennismckinnon](https://github.com/dennismckinnon))
  + [By Project Douglas](https://github.com/project-douglas/eris/tree/master/contracts) ([@project-douglas](https://github.com/project-douglas))
  + [By Doug A.](https://github.com/d11e9/g3) ([@dlle9](https://github.com/d11e9))
+ Mutan
  + [By Jeffrey Wilcke](https://github.com/ethereum/go-ethereum/wiki/Mutan-0.6-Examples) ([@obscuren](https://github.com/obscuren))
+ CLL
  + [By Joris Bontje](https://github.com/jorisbontje/cll-sim) ([@jorisbontje](https://github.com/jorisbontje))

## ÐApps

### Where can I learn about the Ethereum APIs?

+ [The PoC 6 API for C++](https://github.com/ethereum/cpp-ethereum/wiki/Client-Development-with-PoC-6)
+ [The PoC 5 API for Go](https://github.com/ethereum/go-ethereum/wiki/PoC-5-Public-Go-API)
+ [The PoC 6 API for JavaScript](https://github.com/ethereum/cpp-ethereum/wiki/PoC-6-JS-API)
+ [The PoC 6 API for QML](https://github.com/ethereum/go-ethereum/wiki/QML-PoC6-API)

### Where can I learn about ÐApp development?

+ [Your first dapp, under one hour](http://forum.ethereum.org/discussion/1402/how-to-get-started-your-first-dapp-under-one-hour) ([@stephantual](https://github.com/stephantual))
+ [Writing Your Own Currency](http://hidskes.com/blog/2014/05/21/ethereum-dapp-development-for-web-developers/) ([@maran](https://github.com/maran))

### Where can I find ÐApp development tools?

Official

+ [AlethZero GUI Client (C++)](https://github.com/ethereum/cpp-ethereum/wiki/Using-AlethZero)
+ [Eth Client (C++)](https://github.com/ethereum/cpp-ethereum/wiki/Using-Ethereum-CLI-Client)
+ [LLLC Compiler (C++)](https://github.com/ethereum/cpp-ethereum/blob/develop/lllc/main.cpp)
+ [Ethereum Client (Go)](https://github.com/ethereum/go-ethereum/wiki/Command-Line-Options)
+ [Mist Browser (Go)](https://github.com/ethereum/go-ethereum)
+ [Pyeth (Python)](https://github.com/ethereum/pyethereum#interacting-with-the-network)
+ [PyethClient (Python)](https://github.com/ethereum/pyethereum#running-the-client)
+ [Serpent Compiler (Python)](https://github.com/ethereum/wiki/wiki/Serpent)

Community

+ [C3D](https://github.com/project-douglas/c3d) ([@project-douglas](https://github.com/project-douglas))
+ [Emacs LLL Mode](https://github.com/robmyers/lll-mode) ([@robmyers](https://github.com/robmyers))
+ [Emacs Serpent Mode](https://github.com/robmyers/serpent-mode) ([@robmyers](https://github.com/robmyers))
+ [EPM](https://github.com/project-douglas/epm) ([@project-douglas](https://github.com/project-douglas))
+ [EPM Sublime Plugin](https://github.com/project-douglas/epm-sublime) ([@project-douglas](https://github.com/project-douglas))
+ [Ethos Browser](https://github.com/projectdnet/ethos) ([@projectdnet](https://github.com/projectdnet))
+ [MintChalk](http://www.mintchalk.com/) ([@mintchalk](https://github.com/mintchalk))
+ [Poly-Eth](https://github.com/projectdnet/poly-eth) ([@projectdnet](https://github.com/projectdnet))

### Where can I find example ÐApps?

+ [dapp-bin](https://github.com/ethereum/dapp-bin) ([@ethereum](https://github.com/ethereum))
+ [GavCoin](http://gavwood.com/gavcoin.html) ([@gavofyork](https://github.com/gavofyork))
+ [JeffCoin](https://github.com/obscuren/jeffcoin) ([@obscuren](https://github.com/obscuren))
+ [Chronos](https://github.com/mquandalle/chronos) ([@mquandalle](https://github.com/mquandalle))
+ [Artworld-Ethereum](https://github.com/robmyers/artworld-ethereum) ([@robmyers](https://github.com/robmyers))
+ [Eris](https://github.com/project-douglas/eris) ([@project-douglas](https://github.com/project-douglas), [@compleatang](https://github.com/compleatang), [@dennismckinnon](https://github.com/dennismckinnon))
+ [Occam's Run](https://github.com/d11e9/Occams-Run) ([@d11e9](https://github.com/d11e9))