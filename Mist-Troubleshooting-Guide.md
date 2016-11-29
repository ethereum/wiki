Known problems:
- ["Your computers time it out of sync!" error](#your-computers-time-it-out-of-sync-error)
- [Unable to find peers](#unable-to-find-peers)
- [Mist is synchronized but is stuck during the last part](#mist-is-synchronized-but-is-stuck-during-the-last-part)
- [My transaction is not confirmed](#my-transaction-is-not-confirmed)
- [Account can't be unlocked](#account-cant-be-unlocked)
- [Unable to import pre-sale wallet](#unable-to-import-presale-wallet)
- [I send ether to the wallet contract but it doesn't show up](#i-send-ether-to-the-wallet-contract-but-it-doesnt-show-up)

## Please read first!

Wallet or [Mist](https://github.com/ethereum/mist)? The current version of Mist only runs the [wallet dapp](https://github.com/ethereum/meteor-dapp-wallet), this is due to missing features in Mist itself and so that we are able to release earlier. So the wallet is Mist, in wallet mode.

Mist is a [electron](https://github.com/atom/electron) application, means its a desktop hybrid application with a web interface. This allows faster development and changes of the Mist interface and helps with the browser part of Mist. Therefor some problems can come from [electron](https://github.com/atom/electron) itself.

Please note that Mist is still in beta and problems can be expected. This guide will help you troubleshooting some of the most common problems. Some steps require the use of the command line or the developer console that Mist offers. If you need assistance you can ask for help on https://gitter.im/ethereum/mist.

First step is to make sure you have the latest version of Mist. Mist is under active development and new versions contain bugfixes. You can download the latest version here: https://github.com/ethereum/mist/releases.

If its not a common problem you can look at log files to see if there are errors. It is important to known that the wallet consists of two applications. Mist, which runs the wallet and an ethereum node which connects to the network. Communication between the two is done over ipc. A quick scan can be done to determine if the problem is within the Wallet or within the node.

The last part of the node log file can be viewed from Mist through the top menu bar -> develop -> Show log file. If it contains an error try to search for it on https://github.com/ethereum/mist/issues?utf8=%E2%9C%93&q=is%3Aissue to see if its already been report and if someone offered a solution.

If there are no errors the next step is to see if there are problems within Mist. Open the console via the top menu -> develop -> Toggle developer tools -> Wallet UI and look for errors (ignore the "Failed to load resource: net::ERR_FILE_NOT_FOUND" error) and look on the github issue list for simular problems.

If there are none type `web3.eth.blockNumber` on the console and see if you get the latest block number. If you cannot find the problem create an issue on github and describe as good as possible what you have done and which error you got, preferable with a screenshot and log files included.

## Start the node manually

Sometimes its useful to start the node manually to see what its doing:

Stop Mist if its still running and open a command prompt on Windows or a terminal on OSX or Linux. You can start the node manually and see its output on the command line. Navigate to `%APPDATA%\Mist\binaries\geth` on Windows and look for the geth.exe. On Linux go to `.config/Mist/binaries/geth` and on Mac to `~/Library/Application Support/Mist/binaries/geth`. Navigate to that directory (`cd my/path`, on windows use cmd.exe for that) and type the following command:

Windows: `geth.exe --fast --cache 512`
OSX/Linux: `./geth --fast --cache 512`

You can optionally increase the log level by adding `--verbosity 5`.

# Known problems

## "Your computers time it out of sync!" error
If the time of your computers is deviated the wallet isn't able to connect with the network. The wallet is able to detect if time synchronisation is turned off. Please enable time synchronisation:
- Windows: http://windows.microsoft.com/en-gb/windows/set-clock#1TC=windows-7
- OSX: https://support.apple.com/kb/PH21911?locale=en_US
- Linux:  `apt-get install ntp`

## Unable to find peers
There are several reasons why the Mist is not able to connect to the network.
- Time synchronization problems, please update to the latest version of the wallet.
- A firewall is preventing access, check the logs of your firewall.
- A Router doesn't allow NAT or the necessary UDP port 30303, or block other connections made by the ethereum node.

In some cases users have had succes by [starting geth manually](#start-the-node-manually) and add the `--nat=none` flag. 

## Mist is synchronized but is stuck during the last part

Each time the Wallet is started it needs to sync the chain with the network. The first time it needs to download and verify the entire chain which can take a long time. Therefore a smarter and faster solution is used the first time the Wallet is started. It will not download and verify the entire chain but will only download specific parts up to a specific block (latest block - 1024 blocks). After that it will download and process the state of the contracts which can take quite some time. So please wait an let it finish downloading the state, otherwise it would start from scratch again. You can find more details here: https://github.com/ethereum/go-ethereum/pull/1889.

## My transaction is not confirmed

There are multiple causes:
- You lowered the transaction fee. Miners have the option to ignore transaction with a too low fee. It can take a long time before your transaction is included in the block chain. Currently Mist doesn't provide a solution to resend the transaction with a higher transaction fee.
- Mist is not fully synchronized with the network. Make sure you have peers (see the top bar) and the block number in Mist matches the one that is shown on http://etherscan.io. Also check if the block number increases over time. If not, the node is not able to connect to the network. See the section ["Unable to find peers"](#unable-to-find-peers).
- If you have peers and your block number matches or differs only a couple of blocks to the one reported on http://etherscan.io click on the unconfirmed transaction. A popup will open which shows the transaction details and the transaction hash. The transaction hash is actually a link to etherscan. Click on this link. If etherscan shows the transaction details this means the transaction is processed and part of the blockchain, just wait before the wallet receives the block it is included. Otherwise the transaction has not been processed (yet). It is a bit harder to determine where your transaction actually is. It might have never been send onto the network.
- In some cases Mist can loose the connection the underlying node and therefore won't show the transaction s confirmed, please restart Mist and see if it fixes it.

## Account can't be unlocked

Please have a look at [these recommendations](https://github.com/ethereum/mist/issues/669).
In some cases Mist looses the connection the underlying node and therefore can't unlock the account. To make sure your password is correct, you can [start geth manually](#start-the-node-manually) using `geth --unlock "0x123456543234565432345.."` and type the password, should it unlock successfully, then you know its Mist which lost the connection while unlocking.

## Unable to import presale wallet

Due to a bug importing a presale wallet can fail. It is possible to import the presale wallet manually following these instructions: https://github.com/ethereum/go-ethereum/wiki/Managing-your-accounts#importing-your-presale-wallet

## I send ether to the wallet contract but it doesn't show up

If you send ether to a contract wallet, it consumes more than the minimum of 21 000 gas for a normal value transfer, as the contract will also fire logs when receiving the transaction. Make sure you send at least 50 000 gas along with your transaction. 

The transaction will otherwise be reverted and the ether stay at its origin address.