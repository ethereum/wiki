---
name: Contract Metadata Docs
category: 
---

This is the main entry point for NatSpec and generally details a safe and efficient _standard_ for ethereum contract metadata distribution.

By metadata we mean all information related to a contract that is thought to be relevant to and immutably linked to a specific version of a contract on the ethereum blockchain.
This includes:

* Contract source code
* [ABI definition](https://github.com/ethereum/wiki/wiki/Ethereum-Contract-ABI) 
* [NatSpec user doc](https://github.com/ethereum/wiki/wiki/Ethereum-Natural-Specification-Format#user-documentation)
* [NatSpec developer's doc](https://github.com/ethereum/wiki/wiki/Ethereum-Natural-Specification-Format#developer-documentation)

These resources have their _standard specification_ in json format, ideally meant to be produced by IDE infrastructures or compilers directly.

For instance, the [solidity](https://github.com/ethereum/wiki/wiki/Solidity-Tutorial) compiler offers a `doxygen` style way of specifying natspec with inline smart comments. Upon compilation it creates both NatSpec user doc as well ABI definition. But note that there is nothing inherently solidity specific about these data, and other contract languages are encouraged to implement their NatSpec/ABI support potentially with IDE-s extending it.

Since DAPPs and IDEs will typically want to interact with these resources, standardising their deployment and distribution is important for a smooth ethereum experience. 

A specially important example of this is the **NatSpec transaction confirmation notice scheme**, which we will use to illustrate the point. However, the strategy described here trivially extends to arbitrary immutable metadata fixed to an ethereum contract. 

# Transaction Confirmation Notice

The NatSpec user doc allows contract creators to attach custom confirmation notices to each method.
A powerful feature of NatSpec is to provide templating which allows parts of the user notice to be instantiated depending on the parameters of the actual transaction sent to the contract. 

Trusted ethereum client implementations are required to call back from their backend instantiating the natspec transaction notice from actual transaction data and present it to the user for confirmation. 

This serves as a first line of defense against illegitimate transactions sent by malicious DAPPs in the user's name.

Surely, this is only feasible if 
- the node can trust the authenticity of metadata sources.
- nodes have secure reliable access to the metadata resources

Let's see how this is achieved.

## Metadata Authentication

**Proposal**
The metadocs are assumed to be in a single JSON structure called `cmd` file, that stands for _contract metadata doc_.

The `cmd` file's content is hashed and content hash is registered on a name registry via a contract on the ethereum blockchain under the code hash, see https://github.com/ethereum/dapp-bin/blob/master/NatSpecReg/contract.sol _Registering_ in this context will simply mean a key value pair is recorded in an immutable contract storage as a result of a transaction sent to the registry contract.

This provides a public immutable authentication for contract metadata, since:
- the authenticity of the link between the contract and metadata is secured by ethereum consensus
- the authenticity of actual metadata content is secured by content hashing
- the binding is tamper proof 


DAPP IDE environments are supposed to support the functionality, that when you create a contract, all its standard metadata is 
- bundled in a single `cmd` json file
- compiled source code is keccak hashed -> `Sha3(<evmcode>)`
- `cmd` json is keccak hashed -> `Sha3(<cmdjson>)`
- the metadata is registered with the contract by sending a transaction to a trusted name registry. The transaction simply records `Sha3(<evmcode>) -> Sha3(<cmdjson>)` as a key value pair in contract storage.

In order to avoid malicious agents hijacking metadata by overwriting the namereg entry, we propose the following business process:

- the link should be registered before a contract is deployed and is immutable from then on. 
- before the corresponding contract is deployed, we check if the correct metadata bundle is found in the registry and confirmed to a satisfying level of certainty (X blocks).

How it looks like in `Alethzero` is illustrated [here]|(
https://github.com/ethereum/wiki/wiki/NatSpec-Example)

## Metadata Access

In order to provide a robust (failure resistant, 100% uptime) service, decentralised file storage services can and will be used. In the case of content addressed systems like Swarm, accessing and fetching the resource you will only need the `cmd` content hash (using `BZZHASH(<cmdjson>`).

Until that point is reached, we will fall back to using HTTP distribution. To enable this we will include one or many URLHint contracts, which provide hints of URLs that allow downloading of particular content hashes. Find the contract in dapp-bin.

The content downloaded should be treated in many ways (and hashed) to discover what the content is. Possible ways include base 64 encoding, hex encoding and raw, and any content-cropping needed (e.g. a HTML page should have everything up to body tags removed).

It will be up to the dapp/content uploader to keep URLHint entries updated.

The address of the URLHint contract will be specified on an ad-hoc basis and users will be able to enter additional ones into their browser.

This functionality of uploading and registering location is supposed to be also supported by IDE and dev infrastructures. Developers (or IDE) are encouraged to register cmd location _before_ content hash registration (and therefore before actual contract creation) in order to avoid metadata hijacking. Note that if content hash registration is correct, hijacking the location cannot allow malicious content (since content hash authenticates the `cmd`), however it can still prevent your metadata from being accessible. Therefore 
- the url-hint contract storage should be by definition immutable
- content-hash to url-hint should be registered before contract creation

(surely with old web urls, we are always exposed to server failure/hacking, etc)

Read more [here](https://github.com/ethereum/wiki/wiki/NatSpec-Determination)

## Name registry contracts

Interoperability requires that clients know where to look to resolve a contract's metadata, i.e., which name registry to use. Currently, we solve this by 
- creating the two registry contracts after the genesis block 
- and hardwiring their addresses in the client code (e.g., in the natspec transaction confirmation notice module on the client side and the deployment module on the dev/IDE side).


