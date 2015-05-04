# Greeter Contract

Now that you’ve mastered the basics of Ethereum, let’s move into your first serious contract. It’s a big open territory and sometimes you might feel lonely, so our first order of business will be to create a little automatic companion to greet you whenever you feel lonely. We’ll call him the “Greeter”.

```
contract greeter {
	function greet(bytes32 input) returns (bytes32) {
		if (input == "") { return "Hello, World"; }
		return input; 
	}
}
```

As you can see, the Greeter is an intelligent digital entity that lives on the blockchain and is able to have conversations with anyone who interacts with it, based on it’s input. It might not be a talker, but it’s a great listener.

Before you are able to upload it to the network, you need two things: the compiled code, and the Application Binary Interface, which is a sort of user guide on how to interact with the contract.

The first you can get by using a compiler. While there are better tools being built right now, we suggest you use this [online solidity compiler](https://chriseth.github.io/cpp-ethereum/) for now. Follow the instructions and copy the output code where it says "bytecode". Do not close the compiler tab, you’ll need it later! Once you have the compiled code, go back to your Geth console and store it in a local variable:

```
> var greeterCode = "0x608180600c6000396000f3006000357c010000000000000000000000000000000000000000000000000000000090048063e9ebeafe14602e57005b60376004356041565b8060005260206000f35b600060008214604e576075565b7f48656c6c6f2c20576f726c6400000000000000000000000000000000000000009050607c565b819050607c565b91905056"
```

Notice that you must put the bytecode between quotes and prepend it with "0x", so the system will know that this is a hexadecimal string. Now type these commands on the console:

```
> var primaryAccount = eth.accounts[0]
> var greeterAddress = eth.sendTransaction({data: greeterCode, from: primaryAccount}); 
```

You might be asked for your password. You are choosing from which account will pay for the transaction. Wait a minute for your transaction to be picked up and then type:

```
> eth.getCode(greeterAddress)
```

This will return the code of your contract. If it returns “0x”, it means that your transaction has not been picked up yet. Wait a little bit more. If it takes more than five minutes for your transaction to be mined, your gas price might have been too low, or maybe you are not connected to the network.

After your code has been accepted, `eth.getCode(codeAddress)` will return a long string of numbers. If that’s the case, congratulations, your little Greeter is live! If the contract is created again (by performing another eth.sendTransaction), it will be published to a new address. To cleanup old contracts, the SUICIDE opcode can be called.

Now that your contract is live on the network, anyone can interact with it by instantiating a local copy. But in order to do that, your computer needs to know how to interact with it, which is what the Application Binary Interface (ABI) is for. Go back to the compiler page (if you don’t have the code, just go back to the beginning of the section just to regenerate it, you won’t need to upload the greeter again). Copy the code that says "interface" and store it in a variable on the console:

`> var greeterABI =  [{ YOURABIHERE  }];`

Notice the code isn't enclosed by quotes, it's because it's not a string, but an object. It will look probably like this:

```
> var greeterABI = [{ "constant": false, "inputs": [{ "name" : "input", "type": "bytes32" }], "name": "greet", "outputs" : [{ "name": "", "type": "bytes32" }], "type": "function" }]
```

Now type the following commands:

```
> greeterContract = eth.contract(greeterABI)
> greeterInstance = new greeterContract(greeterAddress)
```

Your instance is ready. In order to call it, just type the following command in your terminal:

`> greeterInstance.greet.call("");`

If your greeter returned "Hello World" then congratulations, you just created your first digital conversationalist bot!  Try again with: 

`> greeterInstance.greet.call("hi");`

**Try for yourself**: You can experiment changing its parameters to make it smarter. You could have it charge ether for its profound advice by adding:

`if (msg.value>0) { return "Thanks!"; }`

Before the last return statement.
