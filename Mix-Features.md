---
name: Mix Features
category: 
---

# Block Mining

Hit `File`->`New Project`.

Type contract:

```javascript
contract Test {
}
```

Hit `File`->`Save`.

Hit `Deploy`->`Deploy 'Default'` to deploy contract.

Then for index.html:

```html
<html>
<head>
<script>
var web3 = parent.web3;
var theContract = parent.contract;
</script>
</head>
<body>
	number: <span id="n"></span>
<script>
	function update() {
		document.getElementById('n').innerHTML = web3.eth.number;
	}
	web3.eth.watch('chain').happened(update);
</script>
</body>
</html>
```

Hit `File`->`Save`.

In the HTML view, you'll see:

```
number: 1
```

Hit `Deploy`->`Mine`.

The HTML view will update to:

```
number: 2
```

# Parameters in Contract Constructor

Write a Contract containing parameters in the constructor:

```javascript
contract Test {
       uint mem;
       Test(uint _a, uint _b) { mem = _a; }
       get() returns (uint)
       {
         return mem;
       }
}
```

Press `F7` => `Edit State`
The transaction named 'Constructor' should appear in the transaction lists.

Press `Edit` (on `Constructor`)
This modal dialog should allow users to fill in input parameters (_a and _b).

Press `+` to add a new transaction. Select the function `get` and save.

Press `F5` (or run the current editing state if this is not the default state).

Verify that the return value of `get` is displayed and is this is the good one => in the transaction log (column `returned`).

# Deploy to Network

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

# Account Management

When a new state is created, it is possible to add new accounts which can be used to send transaction.
It is not possible to delete an account if this one is used by a transaction.

By default, one account is created, this account will be used to deploy standards contract like `Config` and `NameReg` but can also be used in other transactions.

For each created account users have to specify a balance. When users start to debugging, the state will 
be initialized with all the configured accounts/balances.

When a transaction is edited, users can select which account to set as the sender.

# Logs Window

Clicking on the header status pane will open a pane which displays all logs generated by Mix.
There are 3 log levels: info, warning, error. 
Those logs comes from different part of Mix:
 - `JavaScript` displays logs coming from the Web preview, it shows error messages generated by the JavaScript engine and messages generated by the command `console.log('...')`.
 - `Run` displays logs coming from the execution of transactions.
 - `State` displays logs coming from directly from the state (State alteration, New block added).
 - `Compilation` displays logs generated by an error when Mix is trying to compile contracts.

On the header are several actions:
 - Clear the content of this window.
 - Copy the content to the Clipboard.
 - Select which log type to display (4 buttons).
 - Use the text input to filter displayed logs.

# Auto-completion for solidity source

Auto-completion in Mix is based on the code mirror plugins show-hint.js and anyword-hint.js (Not semantic)
It displays:
  - Solidity token (currency, keywords, stdContract, Time, Types).
  - Contract Name. 
  - Functions Name.
  - Words that are in the nearby code.  

# Error messages

When running a bunch of transactions, several error might happen.
One of the common error is that the current account does not have enough ether to execute a transaction.
Users should be aware of that and the status panel should display the amount of gas needed, and the current amount of ether that the user has given for this transaction.
Other important error messages will be displayed here.

# Highlight secondary error locations

If the current compilation error has secondary errors locations Mix shows those secondary errors.
In each secondary errors Mix shows where (document) the primary error is.