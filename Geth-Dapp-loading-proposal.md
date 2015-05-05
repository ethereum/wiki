To have a simple way to load and start Dapps vinay and I came up with a great idea:

1. Dapp packages can be downloaded as .zip/.rlp (they will contain a `swarm.json`, with all the file paths and hashes, [see here](https://github.com/ethereum/go-ethereum/wiki/URL-Scheme#server-config-examples))

2. run `$ geth install mydapp.zip`, which will extract, verify and copy the dapp locally somewhere

3. run `$ geth start mydapp` will start a node, with the correct options (rpc, corsdomain etc) and start a local server which points with its root into the dapps folder and resolves path and files through the `swarm.json`

4. goto `http://localhost:5555` to see you dapp running

