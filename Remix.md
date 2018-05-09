---
name: Remix
---
# Remix
Remix, previously known as Browser Solidity, is a web browser based IDE that allows you to write Solidity smart contracts, then deploy and run the smart contract.

You can run Remix from your web browser by navigating to https://ethereum.github.io/browser-solidity/, or by installing and running in on your local computer.


## Local Installation

Instead of running Remix from https://ethereum.github.io/browser-solidity/, you can download the latest package from https://github.com/ethereum/browser-solidity . You will have to switch to the branch **gh-pages**. Download the .zip file with a name similar to **remix-0f851e3.zip** into a directory on your computer. Unzip the .zip file. Load index.html in your browser.

The advantage of running Remix from your local computer is that it can communicate with an Ethereum node client running on your local machine via the Ethereum JSON-RPC API. You can then execute your smart contracts in Remix while connected to your local development blockchain, the Testnet blockchain, or the Mainnet blockchain.

## The Remix Screen

Whether you are running Remix from it's website, or from your local installation, you should see a screen resembling the one below.
![](https://theethereum.wiki/w/images/7/76/RemixMain.png)

# Taking Remix For A Run

## Sample Source Code
Following is the source code for a simple example that you can copy and paste into the left section of your Remix screen.

```
pragma solidity ^0.4.8;

contract Hello  {
    
    // A string variable
    string public greeting;
    
    // Events that gets logged on the blockchain
    event GreetingChanged(string _greeting);
    
    // The function with the same name as the class is a constructor
    function Hello(string _greeting) {
        greeting = _greeting;
    }

    // Change the greeting message
    function setGreeting(string _greeting) {
        greeting = _greeting;
        
        // Log an event that the greeting message has been updated
        GreetingChanged(_greeting);
    }

    // Get the greeting message
    function greet() constant returns (string _greeting) {
       greeting = _greeting;
    }
}
```
## Deploy Sample Source Code
On the right hand side, enter ```"Hello, World!"``` into the input box to the right of the **Create** button. Click on the **Create** button and your contract will be deployed to the JavaScript EVM. The constructor in the source code will be executed with the ```"Hello, World!"``` string passed as the parameter.

Your screen should resemble:

![](https://theethereum.wiki/w/images/4/46/RemixDeploy.png)

The first light blue block with the heading **greeting** shows that after you deployed the contract, the **greeting** variable has been set to ```"Hello, World!"```.

The second light blue block with the heading **greet** shows the results of executing the ```greet()``` function.


## Execute Sample Source Code Function
Scroll down the right hand side of the page and enter ```"Hello from BokkyPooBah!"``` into the input box to the right of **setGreeting** Click on the **setGreeting** button and the ```setGreeting(...)``` function will be executed with the ```"Hello from BokkyPooBah!"``` string passed as the parameter. You will also see the ```GreetingChanged``` event logged in the results.

![](https://theethereum.wiki/w/images/5/54/RemixSetGreeting.png)

If you now click on the **greeting** and **greet** button, the new value of the ```greeting``` will be displayed as shown below:

![](https://theethereum.wiki/w/images/f/f1/RemixSetGreetingView.png)

# Some Remix Options

## The Remix Settings Tab
The following screenprint shows the Remix Settings tab. Use this section to change the Solidity version and whether to enable or disable optimisation. You can also switch off the Auto Compile.

![](https://theethereum.wiki/w/images/7/74/RemixSetting.png)

## The Remix Transactions Tab
The following screenprint shows the Remix Transactions tab. Use this section to select from one of your Accounts, specify the amount of Gas supplied for the transaction and the amount of ethers to send from your specified account with the transaction.

![](https://theethereum.wiki/w/images/6/6e/RemixTransaction.png)

## The Remix Environment Tab
The following screenprint shows the Remix Environment tab. The default setting is to deploy and run your smart contract in the local JavaScript EVM. You can also select Web3 and connect to your local node via JSON-RPC.

![](https://theethereum.wiki/w/images/4/48/RemixEnvironment.png)
