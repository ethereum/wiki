---
name: License
category: 
---

Overview

The Ethereum Foundation ensures three principles concerning the funds it uses to develop Ethereum:

- it is both open source software and Free software after the definition of the Free Software Foundation (so-called FOSS);
- no special treatment is given to any single entity concerning the copyright of the software, the Foundation included;
- source-code will not be distributed ahead of binaries.

However, this is not where the story ends; there are many different licences available that conform to these rules. After considerable discussion, both internal and external together with The Ethereum software collection is distributed under several licences, partly to reflect the different thinking of the minds behind different pieces of software and partly to reflect the need to adapt to real-world issues and opportunities and lay out a strategy to provide the best possible future for the Ethereum community.

### The Core

Ethereum の core (核となる部分)には、分散型大衆決定マシンのエンジンとネットワークを担うコードとethereumをサポートする
全てのライブラリが含まれています。:
* libethereum
* libp2p
* libdevcore
* libethcore
* livevm
* libevmface

Ethereum core は、最もライセンスフリーである概念のもとでリリース予定であり、できるだけ多様な環境で Ethereum が使用されること願っております。閉鎖的な環境においてリリースされる、修正や拡張が必要な場合においても、同様に、フリーソフトとして使用していただくことを願っております。

In this way, while we have not arrived at a final licence, we expect to select one of the MIT licence, the MPL licence or the LGPL licence. If the latter is chosen, it will come with an amendment allowing it to be linked to be statically linked to software for which source code is not available.

In this way, the core of Ethereum, be it C++ or Go, will be available for use in any commercial environment, closed or open source. 

### The Applications

Ethereum の application は以下を含め、GNUライセンスのもとで配布予定です。
* Solidityコンパイラ ( Libsolidity , solc ) 
* AlethZero 
* Mix

This is to reflect the fact that these pieces of software tend to not, by nature, need to be amalgamated or augmented into a larger, closed-source, whole. There is however, much to be gained through many different members of the Ethereum-community being incentivised to develop on and build out such software.

In this way we hope our initial version of Solidity and Mix lay down the foundation for others to build upon and improve in the true Free/open-source software manner.

### The Middleware

Ethereum の middleware は、以下の３つを含み、Affero licence のもとで配布予定です。これはLGPライセンスの一種のようなものです。
* Javascriptで書かれた ethereum.js 
* webthree libraries
* eth (command line client) 
 
We wish to allow free development of technologies by allowing linking to arbitrary software, but again would like to incentivise feeding of back-end integration work back into the community, especially regarding the interoperability with legacy systems.