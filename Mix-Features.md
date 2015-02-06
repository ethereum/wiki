# Block Mining

Hit `File`->`New Project`.

Type contract:

```
contract Test {
}
```

Hit `File`->`Save`.

Hit `Deploy`->`Deploy 'Default'` to deploy contract.

Then for index.html:

```
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

