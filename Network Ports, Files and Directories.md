---
name: Network Ports, Files and Directories
---

# Network Ports, Files and Directories

This page contains the default location of the files and directories commonly used by the Ethereum software:
* Go Ethereum (```geth```)
* Parity
* Ethereum Wallet

## OS/X

### Network Listening Port
The default port Ethereum clients use to listen for incoming connections is **30303**. In the listing below, the first two connections are the outgoing connections from my local Ethereum client to Ethereum clients over the Internet. The next two ports are the TCP and UDP 30303 ports the local Ethereum client is listening on:
 
 ```
 $ netstat -an | grep 30303
 tcp4       0      0  172.20.10.4.54871      47.90.2.48.30303       ESTABLISHED
 tcp4       0      0  172.20.10.4.54854      198.27.65.15.30303     ESTABLISHED
 tcp46      0      0  *.30303                *.*                    LISTEN     
 udp46      0      0  *.30303                *.*
```

### RPC Port
Some application communicate with the Ethereum client through the RPC (Remote Procedure Call) port **8545**, In the listing below, the first two connections are communications with Remix (Browser Solidity) from my web browser. The third line is the local Ethereum client listening for RPC connections from locally running applications.
```
 $ netstat -an | grep 8545
 tcp4       0      0  127.0.0.1.8545         127.0.0.1.55313        ESTABLISHED
 tcp4       0      0  127.0.0.1.55313        127.0.0.1.8545         ESTABLISHED
 tcp4       0      0  127.0.0.1.8545         *.*                    LISTEN
```
### IPC File Directory
The IPC (Interprocess Communication) file is created when ```geth``` is running, and is located in the directory shown below. Ethereum Wallet / Mist communicates with ```geth``` through this pipe:
 ```
 $ ls -al $HOME/Library/Ethereum/geth.ipc 
 srw-------  1 bok  staff  0 17 Mar 11:44 /Users/bok/Library/Ethereum/geth.ipc
```

### ```geth``` Executable

#### ```geth``` Executable - Homebrew Installation
If installed using Homebrew, the location of your ```geth``` executable is:
```
 /usr/local/bin/geth
```

#### ```geth``` Executable - Manual Download
If installed using a downloaded package, the location of your ```geth``` executable is in the subdirectory where you unpacked the downloaded package.

#### ```geth``` Executable - Automatic Download By Ethereum Wallet / Mist
If installed automatically by Ethereum Wallet / Mist, the location of your ```geth``` executable is:
```
 $HOME/Library/Application Support/Ethereum Wallet/binaries/Geth/unpacked
```

The contents of my Ethereum Wallet / Mist ```geth``` directory follows:
```
 $ ls -al "$HOME/Library/Application Support/Ethereum Wallet/binaries/Geth/unpacked"
 total 59464
 drwxr-xr-x  7 user  staff       238 17 Feb 20:02 .
 drwxr-xr-x  4 user  staff       136 25 Nov 11:26 ..
 -rwxr-xr-x  1 user  staff  30444260 17 Feb 20:02 geth
 drwxr-xr-x  4 user  staff       136  1 Jan  1970 geth-darwin-amd64-1.5.3-978737f5
 drwxr-xr-x  4 user  staff       136 22 Dec 02:36 geth-darwin-amd64-1.5.5-ff07d548
 drwxr-xr-x  4 user  staff       136  1 Jan  1970 geth-darwin-amd64-1.5.8-f58fb322
 drwxr-xr-x  4 user  staff       136  1 Jan  1970 geth-darwin-amd64-1.5.9-a07539fb
```

#### ```geth``` Keystore Directory
The location of your your keystore directory follows. Please make sure that you have a backup of the files in this directory:
```
$HOME/Library/Ethereum/keystore
```

The keystore directory contains your encrypted private keys with the account address at the end of the filename:
```
 $ ls $HOME/Library/Ethereum/keystore
 UTC--2017-03-18T05-48-53.504714737Z--c2d7cf95645d33006175b78989035c7c9061d3f9
```
#### ```geth``` Blockchain Data Directory
The location of the directory containing your blockchain data is:
```
 $HOME/Library/Ethereum/geth/chaindata
```

The amount of space required for the blockchain data (fast synced) is: 
```
 $ du -hs $HOME/Library/Ethereum/geth/chaindata
 21G	/Users/user/Library/Ethereum/geth/chaindata
```

### Ethereum Wallet Directories
When you start Ethereum Wallet and it starts ```geth``` as it cannot detect an already running instance of ```geth```, the log files for ```geth``` can be found in:
 ```
 /Users/bok/Library/Application Support/Ethereum Wallet/node.log*
```

Following is the contents of the most recent ```geth``` log file:
```
 $ more "/Users/bok/Library/Application Support/Ethereum Wallet/node.log"
 I0315 20:59:22.049217 ethdb/database.go:83] Allotted 1024MB cache and 1024 file handles to /Users/bok/Library/Ethereum/geth/chaindata
 I0315 20:59:22.207807 ethdb/database.go:176] closed db:/Users/bok/Library/Ethereum/geth/chaindata
 I0315 20:59:22.208268 node/node.go:176] instance: Geth/v1.5.9-stable-a07539fb/darwin/go1.7.4
 I0315 20:59:22.208343 ethdb/database.go:83] Allotted 1024MB cache and 1024 file handles to /Users/bok/Library/Ethereum/geth/chaindata
 I0315 20:59:22.547166 eth/backend.go:187] Protocol Versions: [63 62], Network Id: 1
 I0315 20:59:22.547926 eth/backend.go:215] Chain config: {ChainID: 1 Homestead: 1150000 DAO: 1920000 DAOSupport: true EIP150: 2463000 EIP155: 2675000 EIP158: 2675000}
 I0315 20:59:22.561490 core/blockchain.go:219] Last header: #3353439 [6012bfd9…] TD=166996240872107570564
 I0315 20:59:22.561532 core/blockchain.go:220] Last block: #3353439 [6012bfd9…] TD=166996240872107570564
 I0315 20:59:22.561546 core/blockchain.go:221] Fast block: #3353439 [6012bfd9…] TD=166996240872107570564
 I0315 20:59:22.562435 eth/handler.go:119] blockchain not empty, fast sync disabled
 I0315 20:59:22.564363 p2p/server.go:340] Starting Server
 I0315 20:59:24.677522 p2p/discover/udp.go:227] Listening, enode://eaccc2ebaf60fd5401371f15950b5fd37f60b8ecf686566238c6bd4d925ef3644f92c0e90d2d2b92ac5decf5a25bbe6b1eda23b0c50529cba7c8a8eed90f2783@[::]:30303
 I0315 20:59:24.677791 p2p/server.go:608] Listening on [::]:30303
 I0315 20:59:24.680574 node/node.go:341] IPC endpoint opened: /Users/bok/Library/Ethereum/geth.ipc
 I0315 20:59:34.827406 eth/downloader/downloader.go:326] Block synchronisation started
```


### Parity Directories

#### Parity executable
```
 $ ls -al "/Applications/Parity Ethereum.app/Contents/MacOS/parity"
 -rwxr-xr-x  1 root  admin  32598160 12 Mar 05:17 /Applications/Parity Ethereum.app/Contents/MacOS/parity
```

#### Parity Keystore Directory
The location of your your keystore directory follows. Please make sure that you have a backup of the files in this directory:
 ```
 $HOME/Library/Application Support/io.parity.ethereum/keys/ethereum
```

The keystore directory contains your encrypted private keys with the account address at the end of the filename:

```
 $ ls -al "$HOME/Library/Application Support/io.parity.ethereum/keys/ethereum"
 ...
 -rw-------   1 user staff   551 14 Mar 13:48 UTC--2017-03-03T13-24-07.826187674Z--c2d7cf95645d33006175b78989035c7c9061d3f9
 -rw-r--r--   1 user staff  2162 14 Mar 23:32 address_book.json
 -rw-r--r--   1 user staff   139 15 Mar 01:25 dapps_history.json
```

#### Parity Blockchain Data Directory
The location of the directory containing your blockchain data is:
```
 $HOME/Library/Application Support/io.parity.ethereum/chains/ethereum
```

The amount of space required for the blockchain data (warp synced) is:
```
 $ du -hs "$HOME/Library/Application Support/io.parity.ethereum/chains/ethereum"
 13G	/Users/usr/Library/Application Support/io.parity.ethereum/chains/ethereum
```

## Linux
Linux and OS/X both arose from Unix and exhibit many similarities.

### Network Listening Port
As with OS/X, the default port Ethereum clients use to listen for incoming connections is **30303** The first two entries indicate the ```geth```' process on this machine is **LISTENING** for UDP/TCP connections on all interfaces, port 30303.
The ten others are **ESTABLISHED** connections to other peers' ports 30303.
```
 $ lsof -i|grep 30303
 geth       6475 <yourUser>   30u  IPv6  3399403      0t0  UDP *:30303
 geth       6475 <yourUser>   38u  IPv6  3401768      0t0  TCP *:30303 (LISTEN)
 geth       6475 <yourUser>   18u  IPv4 10126848      0t0  TCP <yourMachine>.local:40570->eu1.ethpool.org:30303 (ESTABLISHED)
 geth       6475 <yourUser>   64u  IPv4 10292235      0t0  TCP <yourMachine>.local:40646->c-69-243-156-140.hsd1.il.comcast.net:30303 (ESTABLISHED)
 geth       6475 <yourUser>   93u  IPv4  6602449      0t0  TCP <yourMachine>.local:36292->121.144.192.63:30303 (ESTABLISHED)
 geth       6475 <yourUser>  154u  IPv4 10011023      0t0  TCP <yourMachine>.local:41570->host-188.65.212.49.knopp.ru:30303 (ESTABLISHED)
 geth       6475 <yourUser>  158u  IPv4 10559115      0t0  TCP <yourMachine>.local:45358->cpc77315-basf12-2-0-cust908.12-3.cable.virginm.net:30303 (ESTABLISHED)
 geth       6475 <yourUser>  160u  IPv4  7925298      0t0  TCP <yourMachine>.local:59546->li1597-200.members.linode.com:30303 (ESTABLISHED)
 geth       6475 <yourUser>  273u  IPv4 10045560      0t0  TCP <yourMachine>.local:39358->82.145.59.46:30303 (ESTABLISHED)
 geth       6475 <yourUser>  480u  IPv4  9849641      0t0  TCP <yourMachine>.local:42860->server.kuthost.com:30303 (ESTABLISHED)
 geth       6475 <yourUser>  552u  IPv4 10608519      0t0  TCP <yourMachine>.local:40652->103.199.16.37:30303 (ESTABLISHED)
 geth       6475 <yourUser>  577u  IPv4  8642782      0t0  TCP <yourMachine>.local:51348->2.95.198.232:30303 (ESTABLISHED)

```
### ```geth``` Files Directory
```geth``` creates a directory in your home directory named **.ethereum** to use for IPC I/O, to store the blockchain, your command history and keystore files.
 
 
 ```
 $ ls -al /home/<yourUser>/.ethereum/
 total 20
 drwxr-xr-x  4 <yourUser> <yourUser> 4096 Mar 16 01:33 .
 drwxr-xr-x 24 <yourUser> <yourUser> 4096 Jan 21 02:24 ..
 drwxr-xr-x  5 <yourUser> <yourUser> 4096 Mar 11 23:09 geth
 srw-------  1 <yourUser> <yourUser>    0 Mar 16 01:33 geth.ipc
 -rw-------  1 <yourUser> <yourUser> 3856 Mar 16 01:33 history
 drwx------  2 <yourUser> <yourUser> 4096 Feb  4 20:26 keystore
```

### ```parity``` Files Directory

```parity``` creates a directory in your home directory named **.ethereum** to use for IPC I/O, to store blockchains etc.
```
 $ ls ~/.local/share/io.parity.ethereum/ -al
 total 40
 drwxrwxr-x  9 <yourUser> <yourUser> 4096 Feb  5 10:24 .
 drwxr-xr-x 41 <yourUser> <yourUser> 4096 Mar 21 19:36 ..
 drwxrwxr-x  2 <yourUser> <yourUser> 4096 Dec 19 23:52 906a34e69aec8c0d
 drwxrwxr-x  4 <yourUser> <yourUser> 4096 Jan 12 16:32 chains
 drwxrwxr-x  2 <yourUser> <yourUser> 4096 Sep 25 10:10 dapps
 drwxrwxr-x  2 <yourUser> <yourUser> 4096 Oct 18 00:38 ipc
 srwxrwxr-x  1 <yourUser> <yourUser>    0 Feb  5 10:24 jsonrpc.ipc
 drwxrwxr-x  4 <yourUser> <yourUser> 4096 Jan 12 16:32 keys
 drwxrwxr-x  2 <yourUser> <yourUser> 4096 Sep 25 10:10 network
 drwxrwxr-x  2 <yourUser> <yourUser> 4096 Oct 27 21:23 signer
 -rw-rw-r--  1 <yourUser> <yourUser>    5 Dec  6 23:24 ver.lock
```
