# Welcome to Mix IDE!

This guide provides a very simple and quick introduction to the Mix IDE workflow by walking you through the creation of a simple ÐApp. Once you are done with this tutorial, you will have a general knowledge of how to create and run applications in the IDE.

Note that the software is in still in proof-of-concept state. Thing are changing rapidly and this tutorial might nob be up to date with the latest version. In such case please open an issue on [github](https://github.com/ethereum/cpp-ethereum/issues) 

## Getting started.

This tutorial assumes you have Mix release binary or source build with Qt 5.4. See [wiki](https://github.com/ethereum/cpp-ethereum/wiki) for building instructions.

## Creating a new project

Let's create a simple DApp that will allow user to store and query personal movie ratings.

In the IDE, choose `File > New Project`. Enter the contract name "MovieRaings" and a path for the project file. A new empty project will be created. To the left there is a project items list with two items added by default: Contract and index.html. Contract contains solidity contract code, and index.html is for the front end web page. You can add new contract files to the project using file menu. All files will be copied to the project directory.

Select Contract and enter the text for the rating contract:

	contract Rating {
		function setRating(string32 _key, uint256 _value) {
			ratings[_key] = _value;
		}
		mapping (string32 => uint256) public ratings;
	}

Check [Solidity tutorial](https://github.com/ethereum/wiki/wiki/Solidity-Tutorial) for solidity reference.

Now select index.html and enter the following html code:
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
Select `File > Save` to save project files. You should see the web preview in the web preview pane.

## Setting up state

Now we need to configure a state for debugging. Mix has its own blockchain that is reset on each debugging session. States are used to get the blockchain to a point where it is possible to make transactions and calls to a contract. A state is defined by a sequence of transactions that create DApp and all dependencies on the blockchain and set up DApp initial storage. 
Open the debugger pane by pressing `F7` or selecting `Windows > Show right` view from the menu. At the top of the right pane you can see a state selector and a transaction log. The Default state is already created with two transaction that deploy basic standard contracts: Config and NameReg. Edit that state by clicking `Edit State` button. Add a transaction by clicking the `+` button. Select the function called `Rating` from the Function combo box. This is the contract constructor, running this transaction will effectively deploy the contract on a blockchain. Close the windows by pressing `OK` and add another transaction with `+`. This time select a setContract function and enter parameter values, e.g. `Titanic` for key and `4` for value, and select `OK`. This will add a single rating key-value pair to the state. Close the state editor with `OK`. Now once you press `F5` the blockchain will be cleared and a state will be re-deployed. You can see the state transactions running in the transaction log.

## Transaction log

Now let's test out contract. Type "Titanic" in the web preview query input and you should see the result returned. Enter a name and a rating in store fields and click `Save` to add a new rating. Note that all  transactions and calls made to the contract during state deployment and debugging session are recorded into Transaction log to the right. Double click on any entry to load the execution into the debugger. There is also a mine button to instantly mine a new block on the chain and put all pending transactions there.

## Debugging

## Deployment to network