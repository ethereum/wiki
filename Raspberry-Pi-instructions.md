---
name: Raspberry Pi Instructions
category: 
---

## Get the "everything-is-ready" image for your Raspberry Pi 2
Download from: http://gav.ethdev.com/ArchLinuxEthereum12082015.img.zip
(it's 2.4 GB in size, if you want to save bandwidth, go and build it yourself, see instructions below)

Unzip it and follow those guidelines to copy the image to your sd card: https://www.raspberrypi.org/documentation/installation/installing-images/

Plugin the sd card, LAN connection and the power supply and lets start!

In order to connect to your pi follow those instructions: https://www.raspberrypi.org/documentation/remote-access/ssh

Currently there are two users, `root` and `pi`.
The passwords are `root` and `raspberry`, respectivly. The first thing you should do is change that. Otherwise everyone can see your IP in netstats and just log into your Raspberry Pi.
Log in and type `passwd <user>` to update the password for each user.



Currently the go client (geth) and the cpp client (eth) are preinstalled. You can find instruction on how to use them here: https://github.com/ethereum/go-ethereum/wiki/Command-Line-Options and here: https://github.com/ethereum/cpp-ethereum/wiki/Using-Ethereum-CLI-Client. But executing eth/geth with `--help` will give you more up-to-date information on how to use them.
Also eth-netstats is installed. You can find instructions in how to use it here: https://github.com/ethereum/wiki/wiki/Network-Status.
It is used to display your client on the centralized network server (http://stats.ethdev.com/).
In order to give your client a different name, choose your favorite command line text editor and change the `INSTANCE_NAME` parameter in `~/eth-net-intelligence-api/app.json` to whatever you like.

For convenience there is `start<NameOfTheClient>ClientWithNetstat.sh` script (execute it with bash) which starts the client and registers it on eth-netstats.
(Note: Downloading the chain takes several hours up to a day, during that time the Raspberry Pi is so busy that netstats doesn't work properly, it may even crash. You may not see your client on netstats until the download is complete. If you don't see you client after one complete day, try a reboot and execute the script again.)

For updating the clients you can just do `bash update<NameOfTheClient>Client.sh` (this may take a while and may not work, since it is pulling current develop. Dependencies have changed).

## Resize your system to use large sd cards

The image is for a 8GB sd card. It allocates 2 GB as swap (needed for compiling and mining :-) ). If your sd card is larger than 8GB, then follow those instructions:

As root execute
```
fdisk /dev/mmcblk0
```

Delete the third partition:
```
d
3
```

Create a new primary partition and use default sizes prompted. This will then create a partition that fills the disk.
```
n
p
3
enter
enter
```
Save and exit fdisk:
```
w
```

Now reboot (`reboot`). Once rebooted: 
```
resize2fs /dev/mmcblk0p3
```

That's it, now you are using all the disk space available.
This has been tested on the Raspberry Pi 2 only.

## Just get the binaries

Alternativly, you can use the linux system of your choice and and get the arm binaries here:
* https://build.ethdev.com/builds/ARM%20Go%20develop%20branch/geth-ARM-latest.tar.bz2 (go)
* \<link will follow> (cpp-ethereum)

## Build it yourself
If you want to build all that yourself, you can do so following those instructions:

We start with a ArchLinux system with 2 GB swap. Instructions on how to get there can be found here: 
http://archlinuxarm.org/forum/viewtopic.php?f=60&t=8366

### go-ethereum
[Installation instructions for ARM](https://github.com/ethereum/go-ethereum/wiki/Installation-Instructions-for-ARM)

### cpp-ethereum
First we install all necessary libaries as described here https://github.com/ethereum/cpp-ethereum/wiki/Building-on-ArchLinux (but without the qt libs):

These are the required packages from the official repositories:
```
sudo pacman -Sy
sudo pacman -S base-devel cmake scons clang llvm boost leveldb crypto++ jsoncpp
```

These are packages that you can get from the [AUR(Arch User Repositories)](https://aur.archlinux.org/). I would suggest using [yaourt](https://wiki.archlinux.org/index.php/yaourt) or any other of the [AUR helpers](https://wiki.archlinux.org/index.php/AUR_helpers).

```
yaourt -S argtable libjson-rpc-cpp
```

Yaourt can be installed by doing this:

```
curl -O https://aur.archlinux.org/packages/pa/package-query/package-query.tar.gz
tar -xvzf package-query.tar.gz
cd package-query
makepkg -si

cd ..
curl -O https://aur.archlinux.org/packages/ya/yaourt/yaourt.tar.gz
tar -xvzf yaourt.tar.gz
cd yaourt
makepkg -si
```
#### Building the client

The instructions for building the client from here and on are identical with Ubuntu so the reader should refer to the [relevant page](https://github.com/ethereum/cpp-ethereum/wiki/Building-on-Ubuntu#choose-your-source). but use `cmake .. -DBUNDLE=minimal -DETHASHCL=0 -DEVMJIT=0` instead.

Resources:

http://archlinuxarm.org/forum/viewtopic.php?f=60&t=8366
http://archlinuxarm.org/forum/viewtopic.php?f=31&t=3119
https://github.com/ethereum/cpp-ethereum/wiki/Building-on-ArchLinux