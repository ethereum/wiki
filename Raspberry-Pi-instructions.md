Download the image from : http://gav.ethdev.com/ArchLinux2GbSwapCppEthGeth.img

Follow those guidelines to copy the image to your sd card: https://www.raspberrypi.org/documentation/installation/installing-images/

Plugin the sd card, LAN connection and the power supply and lets start!

In order to connect to your pi follow those instructions: https://www.raspberrypi.org/documentation/remote-access/ssh

Currently there are two users, `root` and `pi`.
The passwords are `root` and `raspberry`, respectivly. The first thing you should do is change that. Otherwise everyone can see your IP in netstats and just log into your Raspberry Pi.
Log in and type `passwd <user>` to update the password for each user.

The image is for a 8GB sd card. It allocates 2 GB as swap (needed for compiling and mining :-) ). If your sd card is larger than 8GB, then follow those instructions, otherwise you can start your client as described below:

As root execute
* `fdisk /dev/mmcblk0`

Delete the third partition:
* `d`
* `3`

Create a new primary partition and use default sizes prompted. This will then create a partition that fills the disk
* `n`
* `p`
* `3`
* `enter`
* `enter`

Save and exit fdisk:
* `w`

Now reboot (`reboot`). Once rebooted: 
* `resize2fs /dev/mmcblk0p3`

That's it, now you are using all the disk space available.

Currently the go client (geth) and the cpp client (eth) are preinstalled. You can find instruction on how to use them here: https://github.com/ethereum/go-ethereum/wiki/Command-Line-Options and here: https://github.com/ethereum/cpp-ethereum/wiki/Using-Ethereum-CLI-Client. But executing eth/geth with `--help` will give you more up-to-date information on how to use them.
Also eth-netstats is installed. You can find instructions in how to use it here: https://github.com/ethereum/wiki/wiki/Network-Status.
It is used to display your client on the centralized network server (https://eth-netstats.herokuapp.com/)

For convenience there is start\<NameOfTheClient>ClientWithNetstat.sh script (execute it with bash) which starts the client and registers it on eth-netstats.
For updating the cpp client you can just do `bash updateCppClient.sh` (this may take a while). The go client can be updated by getting the latest develop version with wget \<link will follow>.

This has been tested on the Raspberry Pi 2 only.

If you want to build all that yourself, you can do so by installing ArchLinux on you Raspberry (add 1-2 GB of swap) and follow those instructions https://github.com/ethereum/cpp-ethereum/wiki/Building-on-ArchLinux (without the qt libs) for the cpp-client. You should use the cmake option `-DBUNDLE=minimal` when building the client.
For the Go client you can just get the newest executable with wget \<link will follow>.

Resources:

http://archlinuxarm.org/forum/viewtopic.php?f=60&t=8366
http://archlinuxarm.org/forum/viewtopic.php?f=31&t=3119
https://github.com/ethereum/cpp-ethereum/wiki/Building-on-ArchLinux



