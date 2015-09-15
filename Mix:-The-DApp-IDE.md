# Welcome to Mix IDE!

This guide provides a very simple and quick introduction to the Mix IDE workflow by walking you through the creation of a simple ÐApp. Once you are done with this tutorial, you will have a general knowledge of how to create and run applications in the IDE.

Note that the software is in still in proof-of-concept state. Things are changing rapidly and this tutorial might not be up to date. If that is the case [please open an issue](https://github.com/ethereum/cpp-ethereum/issues) or [edit the wiki](https://github.com/ethereum/wiki/wiki/Mix%3A-The-DApp-IDE/_edit). 

## Getting started.

This tutorial assumes you have [C++ Ethereum installed](https://github.com/ethereum/cpp-ethereum/wiki).

## Creating a new project

Let's create a simple ÐApp that will allow user to store and query personal movie ratings.

In the IDE, choose `File > New Project`. Enter the project name "MovieRatings" and a path for the project file. To the left there is a project items list with two items added by default: Contract and index.html. Contract contains [Solidity](https://github.com/ethereum/wiki/wiki/Solidity-Tutorial) contract code, and index.html is for the front-end. You can add new contract files to the project using file menu. All files will be copied to the project directory.

Select Contract and enter the text for the rating contract:

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

## Setting up state

Now we need to configure a state for debugging. Mix has its own blockchain that is reset on each debugging session. States are used to get the blockchain to a point where it is possible to make transactions and calls to a contract. A state is defined by a sequence of transactions that create DApp and all dependencies on the blockchain and set up DApp initial storage. 

If the debugger pane on the right side is not open, open it by pressing `F7` or selecting `Windows > Show right view` from the menu. At the top of the right pane you can see a state selector and a transaction log. The Default state is already created. Add a transaction by clicking the `Add Tx` button. Make sure the function called `Rating` is selected from the Contract dropdown. This is the contract constructor, running this transaction will effectively deploy the contract on a blockchain. Close the windows by pressing `OK`.

Add another transaction with `Add Tx` button. This time select a `Transact with Contract` transaction and enter parameter values, e.g. `Titanic` for _key and `4` for _value. This will add a single rating key-value pair to the state. Close the state editor with `OK`. Now once you press `Add Block...` the blockchain will be cleared and a state will be re-deployed. You can see the Pending transactions move to Block 1 in the transaction log.

## Transaction log

Now let's test out contract. Type "Titanic" in the web preview query input and you should see the result returned. Enter a name and a rating in store fields and click `Save` to add a new rating. Note that all  transactions and calls made to the contract during state deployment and debugging session are recorded into Transaction log to the right. Double click on any entry to load the execution into the debugger. There is also a mine button `Add Block...` to instantly mine a new block on the chain and put all pending transactions there.

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
This will deploy contracts and register front end resources.

The deployment process includes two steps: 
 - **The Deployment of contracts**:
This step will deploy contracts in the main blockchain and package front end resources of the current project. After this operation the package (package.dapp) will be available inside the deployment directory.

 - **The Registration of front end resources**:
To render the Dapp, the Ethereum browser (Mist or AlethZero) needs to access this package. This step will register the URL where the resources are stored.

To Deploy your Dapp, Please follow these instructions:

Click on `Deploy`, `Deploy to Network`.
This modal dialog displays two parts, We will focus on the first part (Deployment) for now:
 
 **The Deployment of contracts**
 - 4 Buttons: `Help` to access to the WikiPage, `Open Package Folder` to open the deployment directory (this button is only enable is the package is built), `Copy Base64 conversion to ClipBoard` to copy the Base64 value of the built package (this button is only enable is the package is built), `Exit` to close this modal dialog.   
 - `Root Registrar address` is the address of the root registrar contract (used to link the Dapp with resources.
 - `Account used to deploy` allows users to select the Ethereum account to use to deploy.
 - `Amount of gas to use..` is the amount of gas that the deployment process will use to deploy contracts.
 - `Ethereum Application URL` is the address that users should use in Mist (or AlethZero) to access to the Dapp. in italic, you can check the formatted Dapp URL (which will be used by the Ethereum browser)
(ex: eth/user1/app1).
 - `Web Application Resources URL` is the URL where the front resources (html/js/...) will be stored.
 - 1 button to start the deployment process (The checkbox `Deploy Contract(s)` is disabled and checked if this is the first time the contract is deployed. If not you can choose to repackage the resources without redeploying the contract by unchecking this option).

Click on `Deploy contract(s) and Package resources files` (last button) and the deployment is executed.
Then many options/actions will be enabled: 
 - `Open Package Folder`, `Copy Base64 conversion to ClipBoard`, `Deploy Contract(s)` 
 - All inputs associated with the second step.

**Host your web application**

There are many places where to host front end resources, you just need to find a web server. One other way (and very easy way) could be to use the pastebin.com service:
 - Follow the first deployment step to deploy contract(s). 
 - The `Copy Base64 conversion to ClipBoard` icon will be enable (on top of the modal dialog), click on this icon to copy the Base64 content into the clipboard.
 - Go to pastebin.com and paste the content into the `New Paste` input. Then click on `Submit'.
 - Go through the Captcha.
 - Copy the address targeted by the link `Raw` (on the top of the page).
 - Use this address in the `Web Application Resources URL` field.
 
**Registration of front end resources**
 - `URL Hint contract address` is the address of the contract which is used to store the URL where the resources are.
 - `Web Application Resources URL` is the URL from where the Ethereum browser will retrieve resources.

Click on `Register hosted Web Application` and Mix will register the front end resources on the Ethereum network.

Users can now use  Mist or AlethZero to access to the Dapp, using the Ethereum URL (ex: eth/user1/app1).
