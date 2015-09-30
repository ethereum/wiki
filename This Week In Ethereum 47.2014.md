---
name: This Week in Ethereum 47
category: 
---

###Current Status 

We are in the last days of preparing the POC-7 release. It is scheduled to ship on 21th of November. Latest stable release is Po6, testnet is reachable here, chain explorer here. Release of PoC8 is expected to be on christmas. Alpha releases are expected for mid January 2015. Release of the genesis is scheduled for March 2015.

### Protocol
* Version update to eth:34
* Blogs/Blooms/Recipes
    There are often going to be situations where contracts need to record events that will be indexible/accessible/provable by light clients and these events are often fire-and-forget.
    So filling a storage index for them is very wasteful.
    We solve this by adding a an opcode LOG, whose purpose it is to create an event that gets put into the receipt trie of the block but is not stored in the state. The transactions trie is split up into two tries.
    (1) transactions only
    (2) medstate, gas, and logs (the recipes)

* Exception severity uniformity
   See: https://ethereum.etherpad.mozilla.org/14

## Clients
### AlethZero
* Commandline option -v now also reports protocol version
* Cleaned up make files
* ...

### Mist
* Added LOG opcode
* Refactored XYZ

###pyethereum
* Added LOG opcode
* Refactored logging to give log structured (machine readable) data  

##Projects

* Continuous Integration: The bootstrapping node was extended to also host a "eth" protocol node. 
* Serpent 2.0: was updated to feature function definitions and proper data structures
* Solidity: First compiled contracts are working and on the PoC7 testnet
* Security Audit
   Results of a preliminary GO code audit were provided by Sergio. Jutta is working on contracting external auditors.
* Stress Testing: Sven is working on the infrastructure to run and analyze various scenarios in a synthetic test network.
* Berlin Office: Almost done, move in is scheduled for Monday.

##People: 
* Yann joined DEV Berlin and will work on the IDE
* Jutta joined and will work on security audits

##In the Media
Worldwide coverage of Vitalik winning the 2014 Software Innovation Award at the World Technology Awards. 

##Upcoming Events

### Meetups
* Berlin 18.11.14
* NYC, Paris, Tokio

###DEVCon-000
The first Ethereum developer conference is happening during the next week in our new office in Berlin. 
