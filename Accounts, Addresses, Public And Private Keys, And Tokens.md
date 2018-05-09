---
name: Accounts, Addresses, Public And Private Keys, And Tokens

---
# Ethereum Key Formats
## Private Key
The format of your private key is ```3a1076bf45ab87712ad64ccb3b10217737f7faacbf2872e88fdd9a537d8fe266```

## Account or Address
The format of your account (which is generated from your public key) is ```0xC2D7CF95645D33006175B78989035C7c9061d3F9```.

Note that there is a lowercase version ```0xc2d7cf95645d33006175b78989035c7c9061d3f9``` and a partially uppercase version <code>0xC2D7CF95645D33006175B78989035C7c9061d3F9</code>.

The partially uppercase version has a checksum to verify the address. See [EIP55 - Yet another cool checksum address encoding](https://github.com/ethereum/EIPs/issues/55 


## UTC JSON Keystore File

The password encrypted private key is stored in a JSON file with the following format (newlines and indents added for clarity, example on OS/X):
```
 $ more ~/Library/Ethereum/keystore/UTC--2017-03-18T05-48-53.504714737Z--c2d7cf95645d33006175b78989035c7c9061d3f9 
 {"address":"c2d7cf95645d33006175b78989035c7c9061d3f9",
   "crypto":{
     "cipher":"aes-128-ctr",
     "ciphertext":"0f6d343b2a34fe571639235fc16250823c6fe3bc30525d98c41dfdf21a97aedb",
     "cipherparams":{
       "iv":"cabce7fb34e4881870a2419b93f6c796"
     },
     "kdf":"scrypt",
     "kdfparams"{
       "dklen":32,
       "n":262144,
       "p":1,
       "r":8,
       "salt":"1af9c4a44cf45fe6fb03dcc126fa56cb0f9e81463683dd6493fb4dc76edddd51"
     },
     "mac":"5cf4012fffd1fbe41b122386122350c3825a709619224961a16e908c2a366aa6"
   },
   "id":"eddd71dd-7ad6-4cd3-bc1a-11022f7db76c",
   "version":3
 }

```
# How To Create New Accounts (or Addresses)

## How To Create A New Account In Go Ethereum (<code>geth</code>)
You can generate a new Ethereum account by executing <code>geth account new</code> if you already have the <code>geth</code> Ethereum node software installed:
 ```
 $ geth account new
 Your new account is locked with a password. Please give a password. Do not forget this password.
 Passphrase: xxxxxxxx
 Repeat passphrase: xxxxxxxx
 Address: {4e6cf0ed2d8bbf1fbbc9f2a100602ceba4bf1319}
```

A UTC--{year}-{month}--{account} encrypted account file is created (formatted):
```
 $ more ~/Library/Ethereum/keystore/UTC--2017-03-03T13-24-07.826187674Z--4e6cf0ed2d8bbf1fbbc9f2a100602ceba4bf1319 
 {"address":"4e6cf0ed2d8bbf1fbbc9f2a100602ceba4bf1319",
  "crypto":{
    "cipher":"aes-128-ctr",
    "ciphertext":"7a0629096348faa0ae8f71a09c613279d80323a400699658305a43f34279d56d",
    "cipherparams":{
      "iv":"003d6c6e3d77255d70d72088be034d03"
    },
    "kdf":"scrypt",
    "kdfparams":{
      "dklen":32,
      "n":262144,
      "p":1,
      "r":8,
      "salt":"8325cad5dcc8e70163d3cf42b43ad97bc3383db53418b084f6650573f7cabe1e"
    },
    "mac":"a627309776c6bc20b9476869c589c858f6bdf64a58881bff01476829555cf8b7"
  },
  "id":"ba2e1b9a-f140-4940-a1c1-f9dddedc0e1b",
  "version":3
 }
```

Here your account or address is 0x4e6cf0ed2d8bbf1fbbc9f2a100602ceba4bf1319. The contents of the file also includes an encrypted version of your private key.

You can also generate a new account from within your running <code>geth</code> console:
```
  > personal.newAccount("insert a passphrase for this new account here")
```
```geth``` will respond with the address of your newly created account. Again, you must retain the passphrase used to create the account.


## How To Create A New Account In Ethereum Wallet / Mist

In Ethereum Wallet / Mist, select the menu item ''Accounts'' -> ''New Account'' to see the following screen. Enter a password AND save this password in a secure location. Click on '''OK''', repeat the same password and confirm:

![](https://theethereum.wiki/w/images/0/0b/EthereumWalletCreateAccount.png)

You will see a new account appear in your Ethereum Wallet / Mist screen:

![](https://theethereum.wiki/w/images/b/b2/EthereumWalletCreateAccountNewAccount.png)

Select the menu ''Accounts'' -> ''Backup'' -> ''Accounts'' to display the directory containing your newly create account JSON file with a name beginning with '''UTC-'''. Back this file (and any other existing files in the same directory) in a secure location.

See _Network Ports, Files And Directories_ for the location of the directories and files on your computer.


## How To Create A New Account In MyEtherWallet

**IMPORTANT**:
..* Save your JSON file and password AND/OR your private key to be able to transfer funds from your new account
..* Check that you are using the correct URL as there are similarly named phishing sites

Navigate to https://www.myetherwallet.com and you will see the following screen:

![](https://theethereum.wiki/w/images/e/e6/MEWGenerateWallet.png)

Enter a password in the following screen. You will have to remember this password, so save it in a secure location. Click on **Generate Wallet**:

![](https://theethereum.wiki/w/images/e/e6/MEWGenerateWallet.png)


Your new wallet has been generated. Click on **Download** and download the file onto your computer. Back this file up in a secure location. Click on **I understand. Continue**:

![](https://theethereum.wiki/w/images/9/97/MEWGenerateWalletDownloadJSON.png)


Following is content of a sample downloaded JSON file with the name **UTC--2017-03-26T11-31-51.429z--dd4eccd742d17887f50c27aebb14d99bfd7571b6**
```
 {"version":3,
  "id":"25d4a9e3-3a1e-4b77-8995-17627b00031a",
  "address":"dd4eccd742d17887f50c27aebb14d99bfd7571b6",
  "Crypto":{
    "ciphertext":"b819f1769169beaf7e6dcdb578dad519eccb86cf34a139c3707b450caa1383ba",
    "cipherparams":{
      "iv":"e85f34bb37ccb102287ade3660b1a32f"
    },
    "cipher":"aes-128-ctr",
    "kdf":"scrypt",
    "kdfparams":{
      "dklen":32,
      "salt":"67f458a5fa32647f294ff113464dfefdced9e0dd129c061bfa655ec53759a849",
      "n":1024,
      "r":8,
      "p":1
     },
     "mac":"35054e01efd959cbafe412411540a311e26ed70bad60b8eb26a8d2f9d76304fb"
   }
 }
```
In the following screen, you will see the sample private key **85e3d0b2bb3011d00a139e5cdc4ae13144962752d6af7916bf2bd271a240094e**. Save this private key in a secure location. 

You can either use the JSON file with the password **AND/OR** this private key to unlock your account. You can print a paper wallet from the following screen:

![](https://theethereum.wiki/w/images/4/4f/MEWGenerateWalletPKPrint.png)

In the following screen, you upload the JSON file you just created, to test the new new account:

![](https://theethereum.wiki/w/images/c/c6/MEWGenerateWalletUnlockWallet.png)

Enter your password and click **Unlock**:

![](https://theethereum.wiki/w/images/a/a6/MEWGenerateWalletSelectJSON.png)

You will see your account (sample **0xdd4eccd742d17887f50c27aebb14d99bfd7571b6**) in the following screen. You can provide this address publicly to receive funds into your newly created account.

![](https://theethereum.wiki/w/images/4/45/MEWGenerateWalletViewWallet.png)

# How To Import Private Keys

## How To Import A Private Key Into Go Ethereum (```geth```)
To import a private key using ```geth```:
```
 # Create a text file containing your private key
 $ more privatekey
 3a1076bf45ab87712ad64ccb3b10217737f7faacbf2872e88fdd9a537d8fe266
 # Execute geth to import your private key
 $ geth account import privatekey 
 Your new account is locked with a password. Please give a password. Do not forget this password.
 Passphrase: 
 Repeat passphrase: 
 Address: {c2d7cf95645d33006175b78989035c7c9061d3f9}
 # Remember to delete the text file containing your private key
 $ rm privateky
```

## How To Import A Private Key Into Ethereum Wallet / Mist
You cannot import a private key directly into Ethereum Wallet / Mist. Instead, you will have to find the ```geth``` executable that is downloaded by Ethereum Wallet / Mist and use the procedure above.

See **Network Ports, Files And Directories** for the location of the ```geth``` executable.


## How To Import A Private Key Into MyEtherWallet
* Navigate to https://www.myetherwallet.com/#send-transaction . **Check that you are using the correct URL as there are similarly named phishing sites**. Click on **Private Key**, paste your private key into the text box and click **Unlock**:
![](https://theethereum.wiki/w/images/a/a8/MEWSendEtherTokensPrivateKey.png)
* The following screen shows an account with a zero balance.
![](https://theethereum.wiki/w/images/9/93/MEWSendEtherTokens.png)
* If you have a non-zero ether balance, you can send ethers to another account.
* If you have a non-zero token balance AND you have a non-zero ether balance, you can send tokens to another account.


# How To Import JSON Files

## How To Import A JSON File Into Ethereum Wallet / Mist

In Ethereum Wallet / Mist, select the menu *Accounts* -> *Backup* -> *Accounts*. Ethereum Wallet / Mist will open up your file manager showing your **keystore** directory.

Copy your JSON file into this directory and your new account will be displayed in the Ethereum Wallet / Mist Wallets tab shortly.


# Frequently Asked Questions

## What is the difference between a private key, public key and account (or address)?

See [How are ethereum addresses generated?](http://ethereum.stackexchange.com/questions/3542/how-are-ethereum-addresses-generated/3619#3619) and [Create full Ethereum wallet, keypair and address](https://kobl.one/blog/create-full-ethereum-keypair-and-address/).


## Are account (or address) generated uniquely?

See [Account uniqueness guaranteed?](https://ethereum.stackexchange.com/questions/4299/account-uniqueness-guaranteed)


## Can the private key be brute forced?

See [Public key vulnerable to brute force in the future?](https://www.reddit.com/r/ethereum/comments/605f5c/public_key_vulnerable_to_brute_force_in_the_future/df3mton/)


## Are tokens stored with the same account (or address) as for normal ethers?

Yes. You store your ERC20 tokens in the same account you use for storing ethers. See **How_Does_A_Token_Contract_Work?** for further information.

You can store ERC20 tokens in an account with 0 ethers, but when you want to transfer the tokens from your account, you will need some ethers to pay for the gas required to transfer your tokens.


## I have lost my password for my JSON file, and I don't have my private key. What can I do?

From [How can I recover or reset a lost wallet password?](https://ethereum.stackexchange.com/questions/97/how-can-i-recover-or-reset-a-lost-wallet-password), you can try using the **pyethrecover** tool from https://github.com/burjorjee/pyethrecover to brute force your password. You will need to provide some password hints for the application to attempt finding your password.


## How can I extract my private key from my JSON file?

The easiest way is to use the View Wallet Info tab in https://www.myetherwallet.com, select the **Keystore File (UTC / JSON)** option, 
select your JSON file, enter your password and click **Unlock**. Your private key will be displayed in the next page.
