Make new vault:

1. `cd ~/coldstorage`
2. `coindb create coldstorage.vault`

Make new private key(chain):

1. `cd ~/coldstorage`
2. `coindb newkeychain coldstorage.vault telaviv4`
3. `coindb exportkeychain coldstorage.vault telaviv4 false`
4. Send `telaviv4.pub` to all others

Make a new cold storage multisig:

1. Ask everyone to follow "Make new private key(chain)" above
2. Put everyone's .pub files onto a USB key
3. `sudo mount /dev/sd[b-z]1 /mnt`
4. `coindb importkeychain coldstorage.vault /mnt/telaviv4.pub` <- Repeat for all pubkeys
5. `coindb newaccount coldstorage.vault coldaccount6 K telaviv4 helsinki8 shenzhen14`, substituting the city names with your own pubkeys and `K` with the number of sigs needed (eg. 2 in this case if you want a 2-of-3)
6. Repeat these steps on your online laptop. On your online laptop, load CoinVault, open your vault, and have everyone do Accounts -> Request Payment -> New Invoice and make sure everyone gets the same address

Unlock:

1. `cd ~`
2. `sudo ./unlock.sh`
3. Follow unlock.sh command line instructions. At the end, make sure you do not get any error (if you do, you probably entered one or more key parts wrong)

Lock:

1. `cd ~`
2. `sudo ./lock.sh`
3. Distribute key parts in ~/output to their owners

Sign transaction:

1. Unlock (see above)
2. Insert USB, `sudo mount /dev/sd[b-z]1 /mnt`
3. `cd ~/coldstorage`
4. `coindb insertrawtx coldstorage.vault /mnt/unsigned.tx`, substituting `/mnt/unsigned.tx` with the filename of the transaction
5. `coindb listtxs coldstorage.vault unsigned`
6. `coindb signtx coldstorage.vault K telaviv4 123`, substituting in your own city/number for telaviv4 and the highest index in the `listtxs` output for K
7. `coindb rawtx coldstorage.vault K true`, once again substututing K for that index
8. `mv <hex>.tx /mnt/signed.tx`
9. `sync`
10. Remove USB