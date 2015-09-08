With five clients out there, it can be confusing to work out which one to download and use. That's why we made this page for you!

There are three families of official Ethereum implementations. All three have passed our "gold standard" Yellow Paper consensus tests, which test much of the consensus algorithm. All have been subject to a rigorous external audit of the security and attack resilience. That is where the similarities end, though. This page describes the differences between each of the clients.

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
