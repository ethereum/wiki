---
name: Mix Improvement Proposal
category: 
---

how it currently looks, its working well for very simple scripts, but its hard to impossible to use for any advanced javascript application, using require.js, common.js, bower, npm, angualr.js, ember, canjs, meteor or any more advanced js framework. As the current deploy method forces you to do things in a simplistic way (linking ethereum.js, contracts files and add the contract variable)

This also makes it hard to test your application as the deploy process is the one, which uploads the contracts as well and nobody want to deploy its app and contracts without being able to thoroughly test them on the main/test net beforehand.

Additionally not all dapps use one or more contracts, but might want to deploy contracts on the fly (like the wallet dapp) and need a simple way to get the compiled contract code.

So here a re my suggestions:

- Deploy should only deploy the app files (with the possibility to choose a to-deploy-folder), and can include the automated name reg stuff as well

- contracts should be deployed by right clicking them -> deploy contract, which then asks where to deploy (testnet, mainnet) and gives back the ABI and address (maybe in a `myContract-deployed.js`)

- contracts should also be compiled with right click -> compile contract, giving the compiled HEX string and ABI in an `myContract-compiled.js` (or just use one `myContract-mix.js`)

- the files in the sidebar should show the folder structure and not sort by type, as any advanced js application needs more than just an index.html and js file. (folders are good ;)

- the deployable file should be zip, not rlp so people can unpack it easily and discover the content which will be deployed. This also allows to understand it and write separate bundle scripts for them. **(But, might anyway become obsolete with swarm)**

I think this improvements, will allow for greater flexibility without compromising the use of mix.