---
name: NatSpec Determination
category: 
---

Publishing and finding the [NatSpec] (https://github.com/ethereum/wiki/wiki/Ethereum-Natural-Specification-Format) documentation for a contract is an important part of the Ethereum system. There are two pieces to this puzzle: the first is for a given client to be able to determine, given a call to a contract, what a **trustworthy** NatSpec documentation hash for the contract is; the second is to find the actual NatSpec documentation body given its content hash.

### Trusted Content Determination

The former, trusted content determination, will ultimately be accomplished through a sophisticated reputation system. In the meantime, we (the Ethereum foundation) will maintain our own curated repository of trusted NatSpec documentation hashes in a contract. The contract is trivial, just providing a mapping from contract code hash to NatSpec JSON file hash. It can be found [here](https://github.com/ethereum/dapp-bin/blob/master/NatSpecReg/contract.sol). We, alone, will retain the key updating and adding entries to this contract.

This contract will have a specific address on the PoC-9 & Frontier testnet, probably referenced from the Ethereum services contract.

### Content Publishing and Distribution

The latter, content publishing and distribution, will ultimately be accomplished through the "Swarm" subsystem or IPFS. Until then, we will piggy back on the existing workaround for content-based publication and distribution; the [URL Hint](https://github.com/ethereum/wiki/wiki/URL-Hint-Protocol) system.

