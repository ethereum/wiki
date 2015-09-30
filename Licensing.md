---
name: Licensing
category: 
---

Overview

The Ethereum Foundation ensures three principles concerning the funds it uses to develop Ethereum:

- it is both open source software and Free software after the definition of the Free Software Foundation (so-called FOSS);
- no special treatment is given to any single entity concerning the copyright of the software, the Foundation included;
- source-code will not be distributed ahead of binaries.

However, this is not where the story ends; there are many different licenses available that conform to these rules. After considerable discussion, both internal and external together with The Ethereum software collection is distributed under several licenses, partly to reflect the different thinking of the minds behind different pieces of software and partly to reflect the need to adapt to real-world issues and opportunities and lay out a strategy to provide the best possible future for the Ethereum community.

### The Core

The core of Ethereum includes the consensus engine, the networking code and any supporting libraries. For C++, this includes libethereum, libp2p, libdevcore, libdevcrypto, libethcore, libevm and libevmface.

The core of Ethereum will be released under the most liberal of licenses. This reflects our desire to have Ethereum used in as many diverse environments as possible, even those which, for various reasons can require modifications or augmentations to the software which cannot be released to the public.

In this way, while we have not arrived at a final license, we expect to select one of the MIT license, the MPL license or the LGPL license. If the latter is chosen, it will come with an amendment allowing it to be linked to be statically linked to software for which source code is not available.

In this way, the core of Ethereum, be it C++ or Go, will be available for use in any commercial environment, closed or open source. 

### The Applications

The applications of Ethereum, including the Solidity compiler (libsolidity, solc), AlethZero and Mix will be distributed under the GNU General Public License. This is to reflect the fact that these pieces of software tend to not, by nature, need to be amalgamated or augmented into a larger, closed-source, whole. There is however, much to be gained through many different members of the Ethereum-community being incentivised to develop on and build out such software.

In this way we hope our initial version of Solidity and Mix lay down the foundation for others to build upon and improve in the true Free/open-source software manner.

### The Middleware

The middleware of Ethereum, including the Javascript-based ethereum.js, the webthree libraries and eth (the command line client) will be distributed under an Affero license, likely the LGPL variant of it. We wish to allow free development of technologies by allowing linking to arbitrary software, but again would like to incentivise feeding of back-end integration work back into the community, especially regarding the interoperability with legacy systems.