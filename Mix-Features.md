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
         return mem
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
