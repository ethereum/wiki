There are three official Ethereum implementations. Being official, they have been developed in-house, primarily by each of the three DEV directors. All three have passed our "gold standard" Yellow Paper consensus tests, which test much of the consensus algorithm. All have been subject to a rigorous external audit of the security and attack resilience.

|   |C++ ("AlethZero", "eth")|Go ("geth")|Python|
|---|---|---|---|
|Historical Consensus Flaws|0|4|0|
|Internal GPU mining|YES|NO|NO|
|External mining ("ethminer")|YES|YES|YES|
|Viable on RPi|YES|NO|NO|
|Chain import benchmark for 1,000,000 blocks of Olympic|4:41|?|?|

With more than 5 clients out there, it can be confusing to work out which one to download and use. That's why we made this page for you!

### eth: Daemon and console client
- Based on C++ implementation (0 Frontier consensus bugs)
- + JSONRPC host
- + Private chains
- + Transaction confirmation security with whitelists
- + Database-rescue
- X Text mode only

### AlethZero: Power-user desktop client
- Based on C++ implementation (0 Frontier consensus bugs)
- + JSONRPC host
- + Private chains
- + Analytic block explorer
- + Plugins
- + Javascript console
- + Transaction confirmation security with whitelists
- + Provides extended wallet with TruGas optimised gas usage system
- + Many more useful features
- X Complex

### AlethOne: Desktop client for mining
- Based on C++ implementation (0 Frontier consensus bugs)
- + No-nonsense interface 
- + Supports solo, pool and farm mining
- + Provides basic wallet with TruGas optimised gas usage system
- X No advanced wallet

### pyethereum: Scripting-friendly Python client
- Based on Go implementation (0 Frontier consensus bugs)
- Command-line (text) interface
- X Text mode only

### geth: Command-line (text) interface.
- Based on Go implementation (4 Frontier consensus bugs)
- + JSONRPC host
- + Private chains
- X Text mode only
