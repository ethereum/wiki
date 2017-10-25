---
name: Clearinghouse
category: 
---

### Introduction

In some type of financial instruments like Futures (but not Forwards), parties are guaranteed against credit losses resulting from the counterparty default by a "clearinghouse". Essentially, a clearinghouse provides this guarantee via a procedure in which the gains and losses that accrue on daily basis throughout the life of a contract are converted into actual cash (daily settlement of gains or losses).

### Daily Settlement

As explained a daily settlement is the actual conversion into cash of daily gains or losses. This procedure is called **daily settlement** or **marking to market** and it is best illustrated via an actual example.

Let's assume two parties enter a Future contract to buy some underlying with a settlement price of 100 USD in a month time (not to be confused with the daily settlement procedure). In order to enter this contract the clearinghouse will set an **initial margin requirement**both the future traders (in this case the party that is going to buy the underlying or the **holder of the long position** and the party that is selling the underlying or the **holder of the short position**) will have to deposit upfront (notice in the context of clearinghouse operations **margin** does not have the same meaning of leverage trading on borrowed money that is used elsewhere). Our initial margin requirement will be 5 USD per contract and will be deposited into a clearinghouse **margin account** associated with the specific contract.

During the life of the contract as the margin account balances change, the parties involved in the contract must maintains their corresponding balances above a level called the **maintenance margin requirement** again decided by the clearinghouse and usually lower than the initial margin.

At the end of each day, the clearinghouse will provide a **settlement price** (usually an average price of any trades that happened during the day) and execute the **mark-to-market** process (daily settlement). 

In our specific example, let's assume the maintenance margin has been set at 3 USD per contract and buying party is entering 10 contract (in other words he will have to deposit an initial margin of 50 USD. We shall also assume money cannot be withdrawn from the clearinghouse (i.e. excess funds). The initial price will be 100 USD (the price of the future contract).

A complete example, is provided in the table below. Notice the two panels report the daily balances of the two parties (long and short).


#### Panel A. Holder of Long Position of 10 Contracts

|Day (1)|Beginning Balance (2)|Funds Deposited (3)|Settlement Price (4)|Futures Price Change (5)|Gain/Loss (6)|Ending Balance (7)|
|:----------:|-------------:|------:|---:|---:|---:|---:|
|0|0|50|100.0| - | - |50|
|1|50|0|99.20| -0.80| -8|42|
|2|42|0|96.00| -3.20| -32|10|
|3|10|40|101.00|5.00|50|100|
|4|100|0|103.50|2.50|25|125|
|5|125|0|103.00| -0.50| -5|120|
|6|120|0|104.00|1.00|10|130|


#### Panel B. Holder of Short Position of 10 Contracts
|Day (1)|Beginning Balance (2)|Funds Deposited (3)|Settlement Price (4)|Futures Price Change (5)|Gain/Loss (6)|Ending Balance (7)
|:----------:|-------------:|------:|---:|---:|---:|---:|
|0|0|50|100.0| -| -|50
|1|50|0|99.20| -0.80|8|58
|2|58|0|96.00| -3.20|32|90
|3|90|0|101.00|5.00| -50|40
|4|40|0|103.50|2.50| -25| 15
|5|15|35|103.00| -0.50| 5|55
|6|55|0|104.00|1.00| -10|45|}

The day the contract is entered will be referred as ''Day 0'', both parties will deposit an initial margin of 50 USD and we shall assume that on ''Day 1'' the future price moves down to 99.20 USD as indicated in Column 4 of Panel A. In Column 5 we can see the Future price change -0.80 (99.20 - 100) and this amount is multipled by the number of contract, 10, to obtain the number in Column 6: -0.80 x 10 = - 8 USD. The ending balance is shown in Column 7 and it is the beginning balance plus/minus any gain/loss. The ending balance on Day 1 for the Holder of the Long Position is 42 USD and it is above the maintenance margin of 30 USD so no extra deposit is required.

### Ethereum clearinghouse implementation and logic

* does it apply to all contracts?
* which flag should be set to enable clearinghouse?
* how are the money/ether debited/credited? Push or pull system?
* Normally clearinghouses use an average daily price (not close price). How to implement? THIS IS IMPORTANT TO AVOID PRICE MANIPULATION ethereum would be even more vulnerable than classic clearinghouse.
* what happens in case of default (failed margin call)? Reputation system?
* many contracts will require an external price feed (i.e. Gold price from CME or LIBOR interest rates). How do we feed them?
* can money being withdrawn from the clearinghouse (i.e. excess funds/gains)? Would be simpler if this was not the case.
* what if the parties really want to exchange the underlying? i.e. no cash/ether settlement? (NOT SPECIFIC TO CLEARINGHOUSE)
* losses/gains are magnified by the leverage inherently provided by the clearinghouse. ACCEPTABLE FOR ETHEREUM? NOTICE ALTHOUGH A FULL SUM DO NOT NEED TO BE DEPOSITED UPFRONT.

### Code reference sample
