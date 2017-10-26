
***
<!doctype>
<html>

<head>
<script type="text/javascript" src="../node_modules/bignumber.js/bignumber.min.js"></script>
<script type="text/javascript" src="../dist/web3-light.js"></script>
<script type="text/javascript">
   
    var Web3 = require('web3');
    var web3 = new Web3();
    web3.setProvider(new web3.providers.HttpProvider());
    function watchBalance() {
        var coinbase = web3.eth.coinbase;
        var originalBalance = web3.eth.getBalance(coinbase).toNumber();
        document.getElementById('coinbase').innerText = 'coinbase: ' + coinbase;
        document.getElementById('original').innerText = ' original balance: ' + originalBalance + '    watching...';
        web3.eth.filter('latest').watch(function() {
            var currentBalance = web3.eth.getBalance(coinbase).toNumber();
            document.getElementById("current").innerText = 'current: ' + currentBalance;
            document.getElementById("diff").innerText = 'diff:    ' + (currentBalance - originalBalance);
        });
    }
</script>
</head>
<body>
    <h1>coinbase balance</h1>
    <button type="button" onClick="watchBalance();">watch balance</button>
    <div></div>
    <div id="coinbase"></div>
    <div id="original"></div>
    <div id="current"></div>
    <div id="diff"></div>
</body>
</html>> 1. 
***
> 1. * `_****_`