# Welcome to Mix IDE!

This guide provides a very simple and quick introduction to the Mix IDE workflow by walking you through the creation of a simple ÐApp. Once you are done with this tutorial, you will have a general knowledge of how to create and run applications in the IDE.

Note that the software is in still in proof-of-concept state. Things are changing rapidly and this tutorial might not be up to date. If that is the case [please open an issue](https://github.com/ethereum/cpp-ethereum/issues) or [edit the wiki](https://github.com/ethereum/wiki/wiki/Mix%3A-The-DApp-IDE/_edit). 

## Getting started.

This tutorial assumes you have [C++ Ethereum installed](https://gavofyork.gitbooks.io/turboethereum/content/chapter1.html), especially for Windows and MacOS, just grab the latest binary from the [release page](https://github.com/ethereum/mix/releases).

## Creating a new project

Let's create a simple ÐApp that will allow user to store and query personal movie ratings.

In the IDE, choose `File > New Project`. Enter the project name "MovieRatings" and a path for the project file. To the left there is a project items list with two items added by default: Sample and index.html. Contract contains [Solidity](https://github.com/ethereum/wiki/wiki/Solidity-Tutorial) contract code, and index.html is for the front-end. You can add new contract files to the project using file menu. All files will be copied to the project directory. By default a sample contract is created.

Select Sample and enter the text for the rating contract:

```js
	contract Rating {
		function setRating(bytes32 _key, uint256 _value) {
			ratings[_key] = _value;
		}
		mapping (bytes32 => uint256) public ratings;
	}
```

Check [Solidity tutorial](https://github.com/ethereum/wiki/wiki/Solidity-Tutorial) for solidity reference.

Now select `index.html` and enter the following html code:
```html
	<!doctype>
	<html>
	<head>
	<script type="text/javascript">
	function getRating() {
        var param = document.getElementById("query").value;
        var res = contracts["Rating"].contract.ratings(param);
        document.getElementById("queryres").innerText = res;
    }

	function setRating() {
        var key = document.getElementById("key").value;
		var value = parseInt(document.getElementById("value").value);
        var res = contracts["Rating"].contract.setRating(key, value);
    }
	</script>
	</head>
	<body bgcolor="#E6E6FA">
    	<h1>Ratings</h1>
		<div>
			Store:
        	<input type="string" id="key">
			<input type="number" id="value">
			<button onclick="setRating()">Save</button>
    	</div>
		<div>
			Query:
	        <input type="string" id="query" onkeyup='getRating()'>
			<div id="queryres"></div>
		</div>
	</body>
	</html>
```
Note that Mix exposes the following objects into the global window context:
* [`web3`](https://github.com/ethereum/wiki/wiki/JavaScript-API#web3) - Ethereum JavaScript API 

* `contracts` - A collection of contract objects. A key to the collection is the contract name. A value is an object with the following properties:
 * `contract` - Contract object instance (created as in `web3.eth.contract`)
 * `address` - Contract address from the last deployed state (see below)
 * `interface` - Contract ABI

Check the [JavaScript API Reference](https://github.com/ethereum/wiki/wiki/JavaScript-API) for further information.

Select `File > Save` to save project files. You should see the web preview in the web preview pane.

## Setting up scenario

Now we need to configure a scenario for debugging. Mix has its own blockchain that is reset on each debugging session. Scenarios are used to get the blockchain to a point where it is possible to make transactions and calls to a contract. A scenario is defined by a sequence of transactions that create DApp and all dependencies on the blockchain and set up DApp initial storage. 

If the scenario panel on the right side is not open, open it by pressing `F7` or selecting `Windows > Show right view` from the menu. 
A default scenario is already created (which contains the transaction that deploys the Sample contract). We don't need this anymore. Click on the green checkbox on the left side of the transaction. This will delete the transaction on the next rebuild. The rebuild button is blinking, which means that the scenario needs to be rebuilt in order to apply changes. Click on it.

At this point we have a clean blockchain. Click on the icon "add Transaction..." which is represented by a flying blue plane. Be sure to have selected "Create Contract" and that the contract "Rating" is selected. Apply changes. 
The contract is now deployed and you can use it from the web preview.
Let's create a new transaction. Click on "add Transaction...", then select "Transact with Contract". The deployed contract "Rating - O" should be selected, this is the one which has been previously deployed. You now want to call the function "setRating". 
Select the function and give some parameters (for instance Titanic for the first parameter and an integer for the second). Apply changes.

Now let's test out contract. Type "Titanic" in the web preview query input and you should see the result returned. Enter a name and a rating in store fields and click `Save` to add a new rating. (If you get the error "web3.js - line 3748 - Uncaught invalid address", see [this discussion of how to resolve it](https://forum.ethereum.org/discussion/3530/web3-js-line-3748-uncaught-invalid-address).) Note that all  transactions and calls made to the contract during state deployment and debugging session are recorded into the scenario panel to the right (you can hide calls by unselecting the option "Scenario > Display calls" in the mix menu).
Now it is possible to debug a transaction. Expand one transaction (by clicking on the arrow on the right side) and on "Debug Transaction".

## Debugging

Mix currently supports assembly level contract code debugging. Source level debugging is a work in progress.

### Assembly level debugging

Double-click a `setRating` transaction in the transaction log to debug it. The VM assembly code is loaded into the assembly view and the execution slider is reset to a first position. You can navigate the execution using the slider and/or step buttons. At any execution point the following information is available:

* VM stack. 
* Call stack - Grows when contract is calling into another contract. Double click a stack frame to view the machine state in that frame
* Storage - Storage data associated with the contract
* Memory - Machine memory allocated up to this execution point
* Call data - Transaction or call parameters

See the [Ethereum Yellow Paper](http://gavwood.com/Paper.pdf) for VM instruction description.

## Deployment to network

This feature allows users to deploy the current project as a Dapp in the main blockchain.
This will deploy contracts and register frontend resources.

The deployment process includes three steps: 
 
 - **Deploy contract**:
This step will deploy contracts in the main blockchain.

 - **Package dapp**:
This step is used to package and upload frontend resources.

 - **Register**:
To render the Dapp, the Ethereum browser (Mist or AlethZero) needs to access this package. This step will register the URL where the resources are stored.

To Deploy your Dapp, Please follow these instructions:

Click on `Deploy`, `Deploy to Network`.
This modal dialog displays three parts (see above):
 
 - **Deploy contract**

 - *Select Scenario*

"Ethereum node URL" is the location where a node is running, there must be a node running in order to initiate deployment.

"Pick Scenario to deploy" is a mandatory step. Mix will execute transactions that are in the selected scenario (all transactions except transactions that are not related to contract creation or contract call). Mix will display all the transactions in the panel below with all associated input parameters.

"Gas Used": depending on the selected scenario, Mix will display the total gas used.

 - *Deploy Scenario*

"Deployment account" allows selecting the account that Mix will use to execute transactions.

"Gas Price" shows the default gas price of the network. You can also specify a different value. 

"Deployment cost": depending on the value of the gas price that you want to use and the selected scenario, this will display the amount of ether that the deployment needs.

"Deployed Contract": before any deployment, this part is empty. This will be filled once the deployment is finished by all contract addresses that have been created.

"Verifications". This will show the number of verifications (number of blocks generated on top of the last block which contains the last deployed transactions). Mix keeps track of all the transactions. If one is missing (unvalidated), it will be displayed in this panel.  

- **Package dapp**

- *Generate local package*

The action "Generate Package" will create the package.dapp in the specified folder.

"Local package Url" the content of this field can be pasted directly in AlethZero in order to use the dapp before uploading it.

- *Upload and share package*

This step has to be done outside of Mix. The package.dapp file has to be hosted by a server in order to be available to all users.

"Copy Base64" will copy the base64 value of the package to the clipboard.

"Host in pastebin.com" will open pastebin.com in a browser (you can then host your package as base64).

- **Register**

"Root Registrar address" is the account address of the root registrar contract.

"Http URL" is the url where resources are hosted (pastebin.com or similar).

"Ethereum URL" is the url that users will use in AlethZero or Mist to access your dapp.

"Formatted Ethereum URL" is the url that users will use in AlethZero or Mist to access your dapp.

"Gas Price" shows the default gas price of the network. You can also specify a different value.

"Registration Cost" will display the amount of ether you need to register your dapp url.
