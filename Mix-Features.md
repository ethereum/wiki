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
This will deploy contracts and package/register front end resources.

Click on `Deploy`, `Deploy to Network`.
This modal dialog displays: 
 - A combobox to select the account to use.
 - `Ethereum Application URL` is the address that users should use in AlethZero to access to the Dapp.
(ex: eth/user1/app1).
 - `Web Application Resources URL` is the URL where the front resources (html/js/...) will be stored.
 - `Amount of gas to use..` is the amount of gas that the deployment process will use to deploy contracts.
 - `Package (Base64)` is filled only when the resources are packaged. This is the Base64 conversion of the current package.

The first step is to click on `Deploy contract / Package resources`. This step will:
 - Deploy contracts on the main blockchain.
 - Package the front end resources (with encryption).
 - Register the dapp into an owned registrar. (Basically a mapping between DappName (project title) => Hash of the front end package).

Users can now (if they need) click on `Package resources only`, this action will repackage the resources without redeploying contracts on the blockchain.

`Open Package Directory` will open the deployment directory in a file browser.

`Register Web Application` is mandatory, this action will associate the package with an http location (Web Application Resources URL). Used by AlethZero to retrieve the package and display the content in a webview.

Once the `Register Web Application` step is done, users can use AlethZero to access to the dapp, using the Ethereum URL (ex: eth/user1/app1).

# Account Management

When a new state is created, it is possible to add new accounts which can be used to send transaction.
It is not possible to delete an account if this one is used by a transaction.

By default, one account is created, this account will be used to deploy standards contract like `Config` and `NameReg` but can also be used in other transactions.

For each created account users have to specify a balance. When users start to debugging, the state will 
be initialized with all the configured accounts/balances.

When a transaction is edited, users can select which account to set as the sender.

