This document exists to collate information once a fortnight (biweekly) from the various different aspects/projects taking place in Ethereum which will allow everyone to better understand what everyone else is working on.


###Current Status of the project

What is the overall current of status of the Ethereum project? What is the next goal?


##Clients/Protocol 

###CPP - Gavin Wood

_What's the current status of what you're working on?_	
Nearing release.

_What progress has been made in the last two weeks?_  
Two weeks closer to release.

_What are the next steps?_  
Further preparation for release.

###Go/Mist - Jeffrey Wilcke

_What's the current status of what you're working on?_	

_What progress has been made in the last two weeks?_	

_What are the next steps?_  

###Node Ethereum - Martin Becze

_What's the current status of what you're working on?_	
* Networking is the biggest issue ATM. 
* RPC compliance

_What progress has been made in the last two weeks?_	
* adding rlpx
 + Wrote DHT code (https://github.com/ethereum/ethereumjs-dht)
* Testing
 + Fixed a several VM bugs caught be the tests
* Finalized a new ethereumjs-lib version
 + Unified block/tx/code processing API
 + simplified browser shims
 + Cleaned up Docs

_What are the next steps?_  
* RLPx
 + added encryption
* More RPC tests

###Pythereum - Heiko

_Status_
PV 52 (currently no interop w/ cpp/go)

_What's the current status of what you're working on?_  
We are currently in a major cleanup and refactoring phase with the goals to:

* replace threading by an event loop architecture

* add rlpx/devp2p https://github.com/heikoheiko/pydevp2p

* split pyetherum up into three repositories, the base library (vm, chain, ...), the networking stack (should work w/o the pyeth lib), and the actual python based client

_What progress has been made in the last two weeks?_	
* devp2p discovery & rlpx is mostly done but not yet integrated with the pyethereum client

* Solidity & ABI support were added

* restructured vm tests

_What are the next steps?_  
refactoring sprint to restore interop based on the new architecture

_Other news?_  
Jannik is doing a two month internship in the Berlin office and helps to clean up and document the code.

###POC8 Protocol
_What's the current status of what you're working on?_  
PoC8 protocol is done.

_What progress has been made in the last two weeks?_  
PoC8 has been released.

_What are the next steps?_  
PoC9.

###POC9 Protocol
_What's the current status of what you're working on?_    
There are a number of changes that security auditors have suggested, and we are currently in the process of implementing them. We are also in the process of finalizing our PoW implementation.

_What progress has been made in the last two weeks?_  
Work on mining, and security audit meetings in which we discussed some of the upcoming protocol changes.

_What are the next steps?_  
Finish it.

###Mix - Arkadiy Paronyan

_What's the current status of what you're working on?_  
First public version was released with poc8 recently. Currently working on new features.	

_What progress has been made in the last two weeks?_  
Done major UI redesign for poc8 release.
Deploy contracts to network story has been  implemented.
Various issues for Windows and OS X platforms resolved.

_What are the next steps?_  
Solidity source level debugging story.

###Swarm - Daniel Nagy

_What's the current status of what you're working on?_ 	
It is in an advanced prototype stage, fully functional, but generating much more than necessary traffic over the network.

_What progress has been made in the last two weeks?_	
Integration with the newest developments in the go client, especially, but not exclusively the new peer discovery system that would allow us to optimize network traffic and client load.

_What are the next steps?_  
Besides merging with the main development branch, which requires unification of command line and configuration options with Whisper (i.e. a similar set of configuration options should be available for Swarm), some documentation needs to be written in order to help people play with it.

###Whisper - Gavin Wood

_What's the current status of what you're working on?_	

_What progress has been made in the last two weeks?_	

_What are the next steps?_  

###Yellow Paper (recent changes) - Gavin Wood

_What's the current status of what you're working on?_ 	 

_What progress has been made in the last two weeks?_	

_What are the next steps?_  

###Dev P2P - Felix Lange

_What's the current status of what you're working on?_	

_What progress has been made in the last two weeks?_	

_What are the next steps?_  


##Languages

###Solidity - Christian Reitwiessner

_What's the current status of what you're working on?_	
Solidity is now able to compile the multisig wallet contract (preliminary version: https://github.com/chriseth/dapp-bin/blob/compilingWallet/wallet/wallet_work_in_progres.sol).

_What progress has been made in the last two weeks?_	
In the last two weeks, we completed enums, visibility specifiers, accessors and a first byte array type. Especially the "external" visibility specifier and the byte array made it possible to "store" call data for later use and implement the forwarding feature of the multisig wallet.

_What are the next steps?_  
The next big features are array types and debugging support. In general, we will probably focus less on generic language features and more on usability.

###Serpent - Vitalik Buterin

_What's the current status of what you're working on?_ 	
Serpent is close to full-featured, and is the backbone of many projects including augur and btcrelay. Strings and arrays are fully supported, as is the PoC8 contract ABI.

_What progress has been made in the last two weeks?_	
Testing and bug fixing mostly. Also, the pyethereum.tester environment has been substantially improved, making it highly interoperable with serpent functions (and solidity functions).

_What are the next steps?_  
Perhaps proper strong typing, we'll see. But ultimately I would like to see serpent become a community project, and have many others and not just myself maintaining and contributing code.


##Release

###System testing project - Sven Ehlert

_What's the current status of what you're working on?_	
* Infrastructure to automatically 
 + provision N (5..200) ec2 instances 
 + with the latest go-client from buildbot 
 + create deterministic configurations for all N clients
 + run basic test scenarios
 + log events in a computer readable json format
 + collect logs in a central elasticsearch database
 + automatically analyze the logs to decide on scenario success

* Implemented scenarios:
 + Start clients
 + Connect clients

_What progress has been made in the last two weeks?_	
* finalized log event spec (for the first scenarios) https://github.com/ethereum/system-testing/wiki/Log-Events
* added support for multiple clients
* added  go client
* first working scenarios (start/connect clients)

_What are the next steps?_  
* More Basic Scenarios
 + Chain consensus scenario with mining clients
 + TX propagation scenario

* Automatic triggering of all scenarios based on new buildbot builds
* Automatic notification of devs in case of failures 

Later advanced scenarios as listed here: https://github.com/ethereum/system-testing/issues?q=is%3Aopen+is%3Aissue+label%3A%22test+scenario%22

###Testing - Christophe Jentzsch

_What's the current status of what you're working on?_	
After the creation of the tools for the block tests, I am now actually writing different tests to ensure that all clients have the same definition on a valid block (in form of an RLP). 

_What progress has been made in the last two weeks?_	
Beside the creation of some transaction tests, Block tests have been added (https://github.com/ethereum/tests/wiki/Block-Tests).   

_What are the next steps?_  
I will add Blockchain tests, which probably will be the last type of tests. With this we have VM tests, state tests, transaction tests, block tests and blockchain tests. This will be sufficient to test almost eveything written in the YP.

###Build System - Caktux

_What's the current status of what you're working on?_ 	 
The build system has received major upgrades and is really starting to cover every aspect of the build process and packaging of the different projects on all platforms. OSX users now benefit from pre-compiled "bottles" using brew along with readily available binaries of Mist and AlethZero for the less technically inclined. There are also packages for Ubuntu for both projects, although the upgrade Qt 5.4 still has unresolved issues. The build server also uses https only for obvious reasons.
Pull requests are now being checked across 7 different builders with even more to come, assuring much better quality code merges.

_What progress has been made in the last two weeks?_	
Half of what is mentioned above, let's say it's all recent enough :) Joris Bontje has also made a very nice looking and useful Buildboard at http://ethereum-buildboard.meteor.com/

_What are the next steps?_  
Full integration tests, from contract deployments, transactions to those, and full assertions of expected results in a DApp's UI. This should be in place by the end of the week if not today the 24th.

###Audit - Jutta

_What's the current status of what you're working on?_	
We are currently in the middle of the security audit, our main auditor is looking at the design and the code base, further auditors are looking at focus topics like networking. We are in continuous exchange, responding to raised issues, providing further required information.  

_What progress has been made in the last two weeks?_	
Ongoing development of the p2p/networking protocol and PoW algorithm required some recent rearrangement of the auditing schedule. Issues raised by auditors and submitted by bounty hunters have been mitigated. 

_What are the next steps?_  
We will focus on the networking and p2p/networking protocol to minimize delays.  

###Dapps - Fabian/AVSA

_What's the current status of what you're working on?_	

_What progress has been made in the last two weeks?_	

_What are the next steps?_  

###ET Hash - Matthew

_What's the current status of what you're working on?_  
We are on Revision 16 now.

_What progress has been made in the last two weeks?_	 
There is now a go client, an opencl client.

_What are the next steps?_  
We are working on integration with the Go client and hopefully other clients prior to launch.

###Vapor - Aaron Davis (Kumavis)

_What is it?_  
Dapp runtime environment + contract browser webapp
think MIST built on node-ethereum

_What's the current status of what you're working on?_	 
Early stages. Currently working on basic PoC.

_What progress has been made in the last two weeks?_  
Decided on tools and created multi-target build system.
Started RPC communication with node-ethereum.
Initial wireframes and logos generated by designer (Jef Cavens)

_What are the next steps?_  
Finish basic identity management and wallet view.
Package node-ethereum with native builds.
Build Dapp runtime environment.

###Launch - Vinay
_Launch Date?_  
Ask me after Zug!

_Which Apps will be available?_  
No idea, I just got hired and I don't know anything yet!

_Which Exchanges will be integrated?_   
I'll ask George!

##Leadership

###Foundation - Joseph Lubin

_What's the current status of what you're working on?_ 	

_What progress has been made in the last two weeks?_	

_What are the next steps?_  

##Communications 

###Meetups - Texture

_What's the current status of what you're working on?_	
Comms has been focused on ethereum.org and bounty sites for the last month and a half. I was tasked with design and front end development. Have moved back to meetups full time now. Working on Ethereum 101, Genesis Block Party, regrouping and strategizing for launch.  

_What progress has been made in the last two weeks?_	
Have moved away from design and development. Developed idea for the Genesis Block Party and have begun to work with meetup organizers to have a day of meetups around launch. Group skypes have occurred every thursday, ramping up to daily meetings. 

_What are the next steps?_  
Concretize the Block Party, Finish the slides for 101 series. Get swag together for launch parties. 

###Upcoming Events - Texture

_What's the current status of what you're working on?_	

_What progress has been made in the last two weeks?_	

_What are the next steps?_  

###Comms - George Hallam

_What's the current status of what you're working on?_	
Currently the comms team is working on the new Ethereum website (which is our main focus until complete in the next week or so). We're also continuing maintain the various social media outlets Ethereum utilises to update and interface with the community to reach out to various companies, journalists etc to spread the word of Ethereum. Ken Kappler continues to write various tutorials (which will continue to be released as we reach stability) and content for the future Ethereum Academy website. Unfortunately Stephan Tual has been taken ill and will be unavailable for a week or so :( (get well soon!) 

_What progress has been made in the last two weeks?_  
Having completed the bounty website just before, Ian and Konstantine are nearing completion of the Ethereum.org website with content now being provided by myself, Gavin and Vitalik. 
In the last two weeks we have also been working on reaching out to various exchanges and possible parters for Ethereum as we move forward to the genesis release.	

_What are the next steps?_  
Release the website! Continue reaching out to various entites that could potentially align themselves with Ethereum. We also have raw video footage that will be processed once Ian is free for doing the website design.

###Ether Academy Website/Ethdev Website - Ken Kappler

_What's the current status of what you're working on?_  
Jr Bedard (of ether.fund) has been contracted to build a new Code Academy type site for teaching developers to build smart contracts.	

_What progress has been made in the last two weeks?_	
Development is continuing nicely, the framework of handling user accounts is almost in place.

_What are the next steps?_  
Design decisions, content creation.

###In the Media - George Hallam

_What's the current status of what you're working on?_  
Putting together various responses for journalists (spectator, coin telegraph etc) that have been in contact. Working with companies using ethereum for joint press releases/coverage.	

_What progress has been made in the last two weeks?_  
We had a great article in Vice/Motherboard... (motherboard.vice.com/read/smart-contracts-sound-boring-but-theyre-more-disruptive-than-bitcoin

_What are the next steps?_  
ojn





##(Legacy)

### Testnet
- Bootstrapping node: poc-7.ethdev.com:30303
- Chain explorer: [poc-7.ethdev.com](http://poc-7.ethdev.com) (is down)
- Eth Protocol Version:45

### Protocol Updates
* Eth Protocol Version update to eth:45
* minGasPrice removed from header
* tx gas costs to be dependent on data values
* Additional price on instructions that require memcopies (*COPY)
* Variable EXP cost

See [poc-7 etherpad](https://ethereum.etherpad.mozilla.org/14) for details.

## Clients
### AlethZero

### Mist

###pyethereum
* added latest poc7 changes 
* debugging (no consensus yet)
* planned the future [architecture](https://github.com/ethereum/pyethereum/issues/189) 

### JS

### Java

##Projects

* Continuous Integration: 
* Serpent 2.0: 
* Solidity: 
* Security Audit:
* Stress Testing: New [repository](https://github.com/ethereum/system-testing) with planning happening in the [wiki](https://github.com/ethereum/system-testing/wiki)
* IDE: 
* DEVp2p: [RLPX spec](https://github.com/ethereum/cpp-ethereum/wiki/RLPX:-Streaming-RLP) almost done.
* JIT Compiler: New [binary interface documentation](https://github.com/ethereum/wiki/wiki/EVM-JIT-Binary-Interface), new [repository](https://github.com/ethereum/evmjit), new Skype channel 'EVM JIT'


##People

##Ecosystem

