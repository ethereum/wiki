With five clients out there, it can be confusing to work out which one to download and use. That's why we made this page for you!

There are three families of official Ethereum implementations. All three have passed the suite of Yellow Paper consensus tests, which test much of the consensus algorithm. All have been subject to a rigorous external audit of the security and resilience to attack. That is where the similarities end, though. This page describes the differences between each of the families of clients and the clients themselves.

## In Short

- If you are looking for a no-nonsense miner with simple wallet functionality for sending on your mined Ether, simply use AlethOne (TODO: Link to binaries).
- If you are developing or debugging a contract, or need a more comprehensive wallet, blockchain explorer and development tool, use AlethZero (TODO: Link to binaries).
- If you are looking for a CLI daemon to mine or interact with on a headless box, use either eth (TODO: Link) or geth (TODO: Link).
- If you can read Python and want a Python-language Ethereum implementation for experimenting, scripting and integrating, use pyethtool.
- If you like C++ and want a C++-language Ethereum implementation for experimenting and integrating, use libethereum and libwebthree.
- If you like Go and want a Go-language Ethereum implementation for experimenting and integrating, use go-ethereum.
- If you just want a wallet and DApp browser, get AlethFive, a prototype of which will hopefully be released later this week.

## Details
|   |C++ ("AlethOne", "AlethZero", "eth")|Go ("geth")|Python|
|---|---|---|---|
|Historical Consensus Flaws|0|4|1|
|Built-in GPU mining|YES|SOON|NO|
|Viable on RPi|YES|NO|NO|
|Chain import benchmark for 1,000,000 blocks of Olympic|4:41|?|?|
|Total heap security|YES|NO|NO|

### AlethOne: Desktop client for mining
- *0 Frontier consensus bugs* (based on C++ implementation)
- + No-nonsense interface 
- + Supports solo, pool and farm mining
- + Provides basic wallet with TruGas optimised gas usage system
- X No advanced wallet

### AlethZero: Power-user desktop client
- *0 Frontier consensus bugs* (based on C++ implementation)
- + JSONRPC host
- + Private chains
- + Analytic block explorer
- + Plugins
- + Javascript console
- + Transaction confirmation security with whitelists
- + Provides extended wallet with TruGas optimised gas usage system
- + Many more useful features
- X Complex

### eth: Daemon and console client
- *0 Frontier consensus bugs* (based on C++ implementation)
- + JSONRPC host
- + Private chains
- + Transaction confirmation security with whitelists
- + Database-rescue
- X Text mode only

### pyethtool: Scripting-friendly Python client
- *1 Frontier consensus bug* (based on Python implementation)
- X Text mode only

### geth: Command-line (text) interface.
- *4 Frontier consensus bugs* (based on Go implementation)
- + JSONRPC host
- + Private chains
- X Text mode only
