---
name: Fortnight in Ethereum Template
category: 
---

This document exists to collate information once a fortnight (biweekly) from the various different aspects/projects taking place in Ethereum which will allow everyone to better understand what everyone else is working on.


###Current Status of the project

What is the overall current of status of the Ethereum project? What is the next goal?


##Clients/Protocol 

###CPP

**What's the current status of what you're working on?**  	
Nearing release of PoC 9 (Mar 6):
* new PoW
* structured logging
* peer selection strategy for node discovery
* rlpx wireline protocol (interop w/Go client)

**What progress has been made in the last two weeks?**  
Release of PoC 8:
* Improved networking & peer discovery
* evm testing
* updates for compliance w/YP
* rpc/ethereum-js updates, bug fixes, interop
* dapp loading
* continued development of solidity features
* continued development for chromium support
* updates for natspec support

**What are the next steps?**  
Further preparation for release.

###Go/Mist

**What's the current status of what you're working on?**	
Improving networking encryption, optimising block processing, refining Mist UI

**What progress has been made in the last two weeks?**	
Release of PoC 8 including:

 * Improved UI and UX

 * Multithreaded miner

 * Updated RPC interface

 * Improved networking & peer discovery

 * Moved from WebKit to Chromium rendering engine (QtWebEngine)

**What are the next steps?**  
Finalising command-line interface and polishing Mist components in preparation for beta

###Node Ethereum

**What's the current status of what you're working on?**	
* Networking is the biggest issue ATM. 
* RPC compliance

**What progress has been made in the last two weeks?**	
* adding rlpx
 + Wrote DHT code (https://github.com/ethereum/ethereumjs-dht)
* Testing
 + Fixed a several VM bugs caught be the tests
* Finalized a new ethereumjs-lib version
 + Unified block/tx/code processing API
 + simplified browser shims
 + Cleaned up Docs

**What are the next steps?**  
* RLPx
 + added encryption
* More RPC tests

###Pythereum

**Status**
PV 52 (currently no interop w/ cpp/go)

**What's the current status of what you're working on?**  
We are currently in a major cleanup and refactoring phase with the goals to:

* replace threading by an event loop architecture

* add rlpx/devp2p https://github.com/heikoheiko/pydevp2p

* split pyetherum up into three repositories, the base library (vm, chain, ...), the networking stack (should work w/o the pyeth lib), and the actual python based client

**What progress has been made in the last two weeks?**	
* devp2p discovery & rlpx is mostly done but not yet integrated with the pyethereum client

* Solidity & ABI support were added

* restructured vm tests

**What are the next steps?**  
refactoring sprint to restore interop based on the new architecture

**Other news?**  
Jannik is doing a two month internship in the Berlin office and helps to clean up and document the code.

###POC8 Protocol
**What's the current status of what you're working on?**  
PoC8 protocol is done.

**What progress has been made in the last two weeks?**  
PoC8 has been released.

**What are the next steps?**  
PoC9.

###POC9 Protocol
**What's the current status of what you're working on?**    
There are a number of changes that security auditors have suggested, and we are currently in the process of implementing them. We are also in the process of finalizing our PoW implementation.

**What progress has been made in the last two weeks?**  
Work on mining, and security audit meetings in which we discussed some of the upcoming protocol changes.

**What are the next steps?**  
Finish it.

###Mix

**What's the current status of what you're working on?**  
First public version was released with poc8 recently. Currently working on new features.	

**What progress has been made in the last two weeks?**  
Done major UI redesign for poc8 release.
Deploy contracts to network story has been  implemented.
Various issues for Windows and OS X platforms resolved.

**What are the next steps?**  
Solidity source level debugging story.

###Swarm

**What's the current status of what you're working on?** 	
It is in an advanced prototype stage, fully functional, but generating much more than necessary traffic over the network.

**What progress has been made in the last two weeks?**	
Integration with the newest developments in the go client, especially, but not exclusively the new peer discovery system that would allow us to optimize network traffic and client load.

**What are the next steps?**  
Besides merging with the main development branch, which requires unification of command line and configuration options with Whisper (i.e. a similar set of configuration options should be available for Swarm), some documentation needs to be written in order to help people play with it.

###Whisper

**What's the current status of what you're working on?**	

**What progress has been made in the last two weeks?**	

**What are the next steps?**  

###Yellow Paper (recent changes)

**What's the current status of what you're working on?** 	 
We're almost done implementing the new RLPx protocol
in go-ethereum.

**What progress has been made in the last two weeks?**	
We have released the node discovery part of RLPx in PoC 8
and it seems to be working for most people.
We ran various simulations of peer connecting strategies to study their influence on network well-formedness. https://docs.google.com/spreadsheets/d/1Xsg0367XibBCg1HegKoInrarLmU_ozwlBLPatv0hhEY/edit?usp=sharing

**What are the next steps?**  
We will implement the remaining parts of the protocol (Encryption
Handshake, authenticated packets) and define a peer selection
strategy.

###Dev P2P

**What's the current status of what you're working on?**	
We're almost done implementing the new RLPx protocol
in go-ethereum.

**What progress has been made in the last two weeks?**	
We have released the node discovery part of RLPx in PoC 8
and it seems to be working for most people.

**What are the next steps?**  
We will implement the remaining parts of the protocol (Encrytion
Handshake, authenticated packets) and define a peer selection
strategy.

##Languages

###Solidity

**What's the current status of what you're working on?**	
Solidity is now able to compile the multisig wallet contract (preliminary version: https://github.com/chriseth/dapp-bin/blob/compilingWallet/wallet/wallet_work_in_progres.sol).

**What progress has been made in the last two weeks?**	
In the last two weeks, we completed enums, visibility specifiers, accessors and a first byte array type. Especially the "external" visibility specifier and the byte array made it possible to "store" call data for later use and implement the forwarding feature of the multisig wallet.

**What are the next steps?**  
The next big features are array types and debugging support. In general, we will probably focus less on generic language features and more on usability.

###Serpent

**What's the current status of what you're working on?** 	
Serpent is close to full-featured, and is the backbone of many projects including augur and btcrelay. Strings and arrays are fully supported, as is the PoC8 contract ABI.

**What progress has been made in the last two weeks?**	
Testing and bug fixing mostly. Also, the pyethereum.tester environment has been substantially improved, making it highly interoperable with serpent functions (and solidity functions).

**What are the next steps?**  
Perhaps proper strong typing, we'll see. But ultimately I would like to see serpent become a community project, and have many others and not just myself maintaining and contributing code.


##Release

###System testing project

**What's the current status of what you're working on?**	
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

**What progress has been made in the last two weeks?**	
* finalized log event spec (for the first scenarios) https://github.com/ethereum/system-testing/wiki/Log-Events
* added support for multiple clients
* added  go client
* first working scenarios (start/connect clients)

**What are the next steps?**  
* More Basic Scenarios
 + Chain consensus scenario with mining clients
 + TX propagation scenario

* Automatic triggering of all scenarios based on new buildbot builds
* Automatic notification of devs in case of failures 

Later advanced scenarios as listed here: https://github.com/ethereum/system-testing/issues?q=is%3Aopen+is%3Aissue+label%3A%22test+scenario%22

###Testing

**What's the current status of what you're working on?**	
After the creation of the tools for the block tests, I am now actually writing different tests to ensure that all clients have the same definition on a valid block (in form of an RLP). 

**What progress has been made in the last two weeks?**	
Beside the creation of some transaction tests, Block tests have been added (https://github.com/ethereum/tests/wiki/Block-Tests).   

**What are the next steps?**  
I will add Blockchain tests, which probably will be the last type of tests. With this we have VM tests, state tests, transaction tests, block tests and blockchain tests. This will be sufficient to test almost eveything written in the YP.

###Build System

**What's the current status of what you're working on?** 	 
The build system has received major upgrades and is really starting to cover every aspect of the build process and packaging of the different projects on all platforms. OSX users now benefit from pre-compiled "bottles" using brew along with readily available binaries of Mist and AlethZero for the less technically inclined. There are also packages for Ubuntu for both projects, although the upgrade Qt 5.4 still has unresolved issues. The build server also uses https only for obvious reasons.
Pull requests are now being checked across 7 different builders with even more to come, assuring much better quality code merges.

**What progress has been made in the last two weeks?**	
Half of what is mentioned above, let's say it's all recent enough :) Joris Bontje has also made a very nice looking and useful Buildboard at http://ethereum-buildboard.meteor.com/

**What are the next steps?**  
Full integration tests, from contract deployments, transactions to those, and full assertions of expected results in a DApp's UI. This should be in place by the end of the week if not today the 24th.

###JSON RPC

**What's the current status of what you're working on?**	

**What progress has been made in the last two weeks?**	

**What are the next steps?**  

###Security Audit

**What's the current status of what you're working on?**	
We are currently in the middle of the security audit, our main auditor is looking at the design and the code base, further auditors are looking at focus topics like networking. We are in continuous exchange, responding to raised issues, providing further required information.  

**What progress has been made in the last two weeks?**	
Ongoing development of the p2p/networking protocol and PoW algorithm required some recent rearrangement of the auditing schedule. Issues raised by auditors and submitted by bounty hunters have been mitigated. 

**What are the next steps?**  
We will focus on the networking and p2p/networking protocol to minimize delays.  

###Dapps

**What's the current status of what you're working on?**	
Improving and streamlining the ethreuem.js APIand RPC endpoints.

**What progress has been made in the last two weeks?**	
Build a full featured whisper chat dapp and the wallet dapp interface.

**What are the next steps?**  
Finalising the wallet app, when the ethereum.js api and RPC is working properly.

###ET Hash

**What's the current status of what you're working on?**  
We are on Revision 16 now.

**What progress has been made in the last two weeks?**	 
There is now a go client, an opencl client.

**What are the next steps?**  
We are working on integration with the Go client and hopefully other clients prior to launch.

###Vapor

**What is it?**  
Dapp runtime environment + contract browser webapp
think MIST built on node-ethereum

**What's the current status of what you're working on?**	 
Early stages. Currently working on basic PoC.

**What progress has been made in the last two weeks?**  
Decided on tools and created multi-target build system.
Started RPC communication with node-ethereum.
Initial wireframes and logos generated by designer (Jef Cavens)

**What are the next steps?**  
Finish basic identity management and wallet view.
Package node-ethereum with native builds.
Build Dapp runtime environment.

###Launch
**Launch Date?**  
Ask me after Zug!

**Which Apps will be available?**  
No idea, I just got hired and I don't know anything yet!

**Which Exchanges will be integrated?**   
I'll ask George!

##Leadership

###Foundation

**What's the current status of what you're working on?** 	
Currently we are focusing our attention on the Genesis launch, making sure that all the aspects involved in the release are taken care off. We are also experimenting with an open collaboration system designed to create the favorable conditions for developers to unleash their creativity and build amazing things with ethereum. 

**What progress has been made in the last two weeks?**	
Intense brainstorming and discussions regarding the launch strategy and how to best nurture the growth of the ethereum community and ecosystem as a whole. 

**What are the next steps?**  
Coordinate with comms, dev and the community in order to concretize the ethereum Genesis launch strategy. Regarding post-Genesis plans, there will be a number of exciting announcements coming in the following weeks ^_^

##Communications 

###Meetups

**What's the current status of what you're working on?**	
Comms has been focused on ethereum.org and bounty sites for the last month and a half. I was tasked with design and front end development. Have moved back to meetups full time now. Working on Ethereum 101, Genesis Block Party, regrouping and strategizing for launch.  

**What progress has been made in the last two weeks?**	
Have moved away from design and development. Developed idea for the Genesis Block Party and have begun to work with meetup organizers to have a day of meetups around launch. Group skypes have occurred every thursday, ramping up to daily meetings. 

**What are the next steps?**  
Concretize the Block Party, Finish the slides for 101 series. Get swag together for launch parties. 

###Upcoming Events

**What's the current status of what you're working on?**	
Working with meetup organizers on Genesis Block Parties

**What progress has been made in the last two weeks?**	
Working with Sarah Masare from Silicon Valley, Cameron and Peter Van Garderen from Vancouver on formalizing the Genesis Block Party. 

**What are the next steps?**  
Confirm release date, get swag printed, have meetups sign on for block party

###Comms

**What's the current status of what you're working on?**	
Currently the comms team is working on the new Ethereum website (which is our main focus until complete in the next week or so). We're also continuing maintain the various social media outlets Ethereum utilises to update and interface with the community to reach out to various companies, journalists etc to spread the word of Ethereum. Ken Kappler continues to write various tutorials (which will continue to be released as we reach stability) and content for the future Ethereum Academy website. Unfortunately Stephan Tual has been taken ill and will be unavailable for a week or so :( (get well soon!) 

**What progress has been made in the last two weeks?**  
Having completed the bounty website just before, Ian and Konstantine are nearing completion of the Ethereum.org website with content now being provided by myself, Gavin and Vitalik. 
In the last two weeks we have also been working on reaching out to various exchanges and possible parters for Ethereum as we move forward to the genesis release.	

**What are the next steps?**  
Release the website! Continue reaching out to various entites that could potentially align themselves with Ethereum. We also have raw video footage that will be processed once Ian is free for doing the website design.

###Ether Academy Website/Ethdev Website

**What's the current status of what you're working on?**  
Jr Bedard (of ether.fund) has been contracted to build a new Code Academy type site for teaching developers to build smart contracts.	

**What progress has been made in the last two weeks?**	
Development is continuing nicely, the framework of handling user accounts is almost in place.

**What are the next steps?**  
Design decisions, content creation.

###In the Media

**What's the current status of what you're working on?**  
Putting together various responses for journalists (spectator, coin telegraph etc) that have been in contact. Working with companies using ethereum for joint press releases/coverage.	

**What progress has been made in the last two weeks?**  
We had a great article in Vice/Motherboard... (motherboard.vice.com/read/smart-contracts-sound-boring-but-theyre-more-disruptive-than-bitcoin

**What are the next steps?**  
ojn