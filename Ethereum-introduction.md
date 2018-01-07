![Ethereum Homestead gold ingots](https://sustergy.files.wordpress.com/2017/05/ethereum-homestead-background-17.jpg?w=1000)

Note that due to the lightning-fast pace of development in the Ethereum space with core development and dapps continually being launched, certain parts of this article may be outdated. You can help by keeping it up to date!

<!-- Created by [gh-md-toc](https://github.com/ekalinin/github-markdown-toc)-->

Table of Contents
=================

   * [Table of Contents](#table-of-contents)
   * [About Ethereum](#about-ethereum)
   * [Uses](#uses)
      * [List of dapps](#list-of-dapps)
      * [Market analysis](#market-analysis)
   * [Issues](#issues)
      * [Scalability](#scalability)
      * [Proof of work / proof of stake / other proving methods](#proof-of-work--proof-of-stake--other-proving-methods)
      * [Public permissionless blockchains vs. private permissioned blockchains](#public-permissionless-blockchains-vs-private-permissioned-blockchains)
      * [No technological artefacts can be a panacea](#no-technological-artefacts-can-be-a-panacea)
   * [How do you buy and sell Ether, the currency of Ethereum?](#how-do-you-buy-and-sell-ether-the-currency-of-ethereum)
      * [Table of exchanges](#table-of-exchanges)
      * [More details about what I've tried (not very necessary to know)](#more-details-about-what-ive-tried-not-very-necessary-to-know)
   * [Development](#development)
   * [Concluding remarks](#concluding-remarks)
   * [Further reading](#further-reading)

# About Ethereum
<a href="https://www.ethereum.org/" target="_blank" rel="nofollow noopener noreferrer">Ethereum</a> is a [decentralized](https://medium.com/@VitalikButerin/the-meaning-of-decentralization-a0c92b76a274) blockchain platform for "building unstoppable applications", while Ether is the cryptocurrency used on this platform. Ethereum has been described in several ways, such as (the first and third resources are more general introductions, while the second is a technical introduction, although all are outdated. 
Another introduction is available [here](https://bitsonblocks.net/2016/10/02/a-gentle-introduction-to-ethereum/), but again, it is outdated. Despite being outdated, Ethereum has maintained backwards compatibility thus far up till January 1 2018, so the info is still relevant.):
<ul>
	<li><a href="https://github.com/ethereum/wiki/wiki/White-Paper" target="_blank" rel="noopener">"A Next-Generation Smart Contract and Decentralized Application Platform"—Ethereum White Paper</a></li>
	<li><a href="https://github.com/Ethereum-community/yellowpaper/blob/master/Paper.pdf" target="_blank" rel="noopener">"A secure decentralised generalised transaction ledger" and a generalised "transactional singleton machine with shared-state". Also described as crypto-law—Ethereum Yellow Paper</a></li>
	<li><a href="http://ethdocs.org/en/latest/introduction/what-is-ethereum.html" target="_blank" rel="noopener">an open source "programmable blockchain"—Ethdocs</a></li>
</ul>
Let's briefly breakdown what those terms mean.

**Decentralized** technology uses [peer-to-peer computer networks](https://en.wikipedia.org/wiki/Peer-to-peer) (there's a picture below), and are not subject to the whims of a central authority such as a government or server administrator (like Google or Facebook) which can help to achieve better decision making for public good. **Blockchain** means that the currency is built and secured by adding and verifying blocks of transactions to blocks made previously, thus forming a "chain". Blocks added to the chain become harder and harder to crack over time, as they are verified by more nodes in the blockchain peer-to-peer network. Blockchain technology has been referred to as the **Web 3.0**. The world wide web (retroactively the Web 1.0) consisted of websites publishing content and users passively reading/viewing it. The Web 2.0 used user interaction, such as forums (with upvoting and commenting), reaction buttons (e.g. the Facebook reactions: likes 👍, love ❤️ , laughter 😆, wow 😲, sad 😢, angry 😠), sharing (republishing), however these interactions have no direct economic effect on the host website; users do not share in the value generated from the website. The Web 3.0 is starting to be defined as the movement away from centralisation of computation power in servers which provide services to clients (known as the <a href="https://en.wikipedia.org/wiki/Client%E2%80%93server_model" target="_blank" rel="noopener">client-server network model</a>) to peer-to-peer networks and blockchains, and <a href="https://github.com/DemocracyEarth/paper/blob/master/README.mediawiki" target="_blank" rel="noopener">from centralisation of authority and sovereignty from nation-states and corporations to the networked individual</a>.

![server-based-network](https://sustergy.files.wordpress.com/2017/05/200px-server-based-network-svg.png)
![p2p-network](https://sustergy.files.wordpress.com/2017/05/200px-p2p-network-svg.png)

**Cryptocurrency** refers to a a digital currency that secures transactions with cryptographic code, which is solved through hardware computational power (known as mining or proof of work) or other less energy-intensive ways such as proof-of-stake. (There are more details on that below.)

Zero knowledge proofs like <a href="https://crypto.stackexchange.com/questions/19884/what-are-snarks" target="_blank" rel="noopener">ZK SNARKs</a> can also be used to make cryptocurrency transactions more private 🕵️ or secret 🤐 (which is different to being secure 🔒), thus negating the need to run applications on a permissioned private network like the [Ethereum Enterprise Alliance](https://entethalliance.org/). Ethereum uses [precompiled contracts for addition and scalar multiplication on the elliptic curve alt_bn128](https://github.com/ethereum/EIPs/pull/213), for [pairing checks](https://github.com/ethereum/EIPs/pull/212), which permit [zk-SNARKs](https://blog.ethereum.org/2017/10/12/byzantium-hf-announcement/), also see [here](https://medium.com/@VitalikButerin/zk-snarks-under-the-hood-b33151a013f6), [as implemented](https://github.com/ethereum/EIPs#finalized-eips-standards-that-have-been-adopted) in the [Byzantium hard fork](https://blog.ethereum.org/2017/10/12/byzantium-hf-announcement/). There is also the Zerocoin protocol which is demonstrated by <a href="https://zcoin.io/" target="_blank" rel="nofollow noopener noreferrer">Zcoin</a> (which plans to integrate Ethereum).

# Uses
The platform part of Ethereum makes it much more useful than just a cryptocurrency. With it, you can create any decentralized application (known as a dapp, which works over a peer-to- peer network rather than a centralized client-server network 💻🕸️), so the functionality is only limited by what programs could potentially do and not do, and by consequence, what programmers develop, 👨‍💻 but it can theoretically be used for any economic or governance activity. 

## List of dapps

For a list of dapps, visit [here](https://github.com/Ethereum-community/Ethereum-introduction/wiki/Decentralised-apps-(dapps)). 


***


However, there are several issues that will need to be resolved to help Ethereum be used to its full potential, which are described below.

## Market analysis
As of the 1st of December 2017, [the market capitalisation of Ethereum is $32.6 billion USD](https://cryptolization.com/ethereum) (refer to the link for the latest figure), and [it has been in circulation possibly since 30 July 2015](https://github.com/jamesray1/homestead-guide/blob/32d2fa4ccfa3d45f8493a673a08247450d55fea0/source/introduction/the-homestead-release.rst#milestones-of-the-ethereum-development-roadmap), with the [first transaction using Ethereum on 8 August 2015](https://www.etherchain.org/account/0x5abfec25f74cd88437631a7731906932776356f9). Compare this with the next largest and the current largest cryptocurrency, [Bitcoin, with a market cap of $41.0 b. US](https://cryptolization.com/ethereum), where [it has been in circulation since January 2009](http://www.newyorker.com/reporting/2011/10/10/111010fa_fact_davis). Technically, Ethereum has had a much faster growth rate, while more importantly for long term investment (I do not encourage speculation as that only causes volatility as has been seen) the fundamentals are much better than Bitcoin. While it is true that Bitcoin has more of a market and currency, e.g. in terms of more entities that will accept it as a form of payment, the creator of this wiki expects that time will change that (indeed the <a href="https://seekingalpha.com/article/4077679-ethereum-blasts-20-billion-market-cap-half-bitcoin" target="_blank" rel="noopener">market cap of Ethereum recently surpassed half that of Bitcoin, around May 2017</a>). Also, [the number of transactions of Ethereum surpassed that of several cryptocurrencies combined on 22 Nov 2017](https://www.reddit.com/r/ethereum/comments/7est9k/ethereum_is_now_processing_more_transactions_a/). However, note [this retort](https://www.reddit.com/r/ethereum/comments/7est9k/ethereum_is_now_processing_more_transactions_a/dq7a31u/).

# Issues
There also several issues with Ethereum, such as not being scalable enough, not being full decentralized, energy consumption with mining, if quantum computing advances it would be insecure (but this is being fixed). With its [large storage database](https://www.reddit.com/r/ethtrader/comments/7axn5g/ethereum_blockchain_sizewe_have_a_problem/) (I have to provide a [Reddit link](https://www.reddit.com/r/ethtrader/comments/7axn5g/ethereum_blockchain_sizewe_have_a_problem/) as a source as the [original link](https://etherscan.io/chart/chaindatasizefull) doesn't have the graph any more, while [Wayback doesn't render it either](https://web.archive.org/web/20171211015955/https://etherscan.io/chart/chaindatasizefull).), mining and architecture requiring to run a full node to mine or validate transactions, it is not decentralized enough. More (outdated but still applicable) info on that is e.g. [here](https://ethereum.stackexchange.com/questions/143/what-are-the-ethereum-disk-space-needs#826), as well as [here](https://github.com/ethereum/go-ethereum#full-node-on-the-main-ethereum-network).

## Scalability

Ethereum will need to scale to process far more transactions per second (to become a "<a href="https://www.youtube.com/watch?v=j23HnORQXvs" target="_blank" rel="noopener">world computer</a>") than Visa, Mastercard and American Express combined (which process on the order of [tens of thousands of transactions per second](https://usa.visa.com/run-your-business/small-business-tools/retail.html) [in the link, CTRL+F 24,000]), while Ethereum 1.0, the current version as of December 30 2017, processed [a record of 1103523 transactions on Friday, December 22, 2017, or 12.77 transactions per second](https://web.archive.org/web/20171230005127/https://etherscan.io/chart/tx).

Note that [Ripple claims that it's Consensus Ledger can process a thousand transactions per second](https://ripple.com/dev-blog/ripple-consensus-ledger-can-sustain-1000-transactions-per-second/), while it could process more with payment channels. "Although payment channels achieve practically infinite scalability by decoupling payment from settlement, they do so without incurring the risk typically associated with delayed settlement." Further note that Ripple achieves this by trading off on decentralization, through a [distributed network of validators or distributed servers](https://ripple.com/build/xrp-ledger-consensus-process/), while it has been described as a [federation protocol](https://wiki.ripple.com/Federation_protocol).

There are even more scalable blockchains that use a delegated proof of stake (DPOS) consensus protocol, such as Bitshares and Steem. [Bitshares can apparently process 100,000 TPS](https://bitshares.org/technology/industrial-performance-and-scalability/).

More generally, in order to have faster payments or higher transaction throughput, you need to reduce the number of validators (miners are a kind of validator that perform energy intensive computational work, finding a random nonce or sequence number in a large set of numbers) in the consensus protocol, or reduce the other (i.e. for faster payments you can reduce transaction throughput or reduce validators, while for higher transaction throughput you can reduce validators or have payments take longer to finalize). This is [a trade-off triangle](https://twitter.com/VladZamfir/status/932319930363494400). You could potentially have one blockchain with [heterogeneous sharding](https://twitter.com/VladZamfir/status/932320997021171712), with different shards with a different degree of balance between these properties. Ethereum is working on [sharding](https://github.com/ethereum/sharding/blob/develop/docs/doc.md), which includes using [stateless clients](https://github.com/ethereum/sharding/blob/develop/docs/doc.md#stateless-clients) (while more on that is [here](https://ethresear.ch/t/the-stateless-client-concept/172/14)).

If you increase scalability in an instant via some blockchain or shard, while keeping latency constant (or reducing it) you need to reduce decentralization, which reduces the number of points of attack needed to compromise the whole network, i.e. reducing decentralization reduces security.

## Proof of work / proof of stake / other proving methods
The mining process to crack cryptographic code (specifically to discover the nonce, a very large number, for each block by trial and error) requires a lot of computation power. Nevertheless, I'm guessing that the computation power should be less when you consider the <strong>energy consumption</strong> of incumbent financial systems. (Think of extracting and processing resources to make coins and notes, minting and printing, energy consumption of banks and tiers of related energy consumption in the life cycle of fiat money.) Still, developers of some cryptocurrencies such as Ethereum are transitioning to (as is the case for Ethereum), or already using, a different way of maintaining and creating blocks, known as proof of stake. For more information, you can see this Proof of Stake Wikipedia article <a href="https://en.wikipedia.org/wiki/Proof-of-stake" target="_blank" rel="nofollow noopener noreferrer">here</a> (although note the header warning about the article potentially not being verifiable or neutral due to relying heavily on sources too closely associated to the subject). The tricky part is in getting proof methods to work better than proof of work, as outlined <a href="https://en.wikipedia.org/wiki/Proof-of-stake" target="_blank" rel="noopener">here</a> in the criticism section of the PoS Wiki.

## No technological artefacts can be a panacea
For the continual improvement of humanity, there needs to be balance in life between things that benefit us materially and things that benefit us on higher levels, particularly spiritually. There is a risk that technology can make some people better off, and others worse off. So there needs to be consideration for how technology can be implemented to maximise [utility](https://en.wikipedia.org/wiki/Utilitarianism).  One consideration of that is [here](https://medium.com/@RhysLindmark/co-evolving-the-phase-shift-to-cryptocapitalism-by-founding-the-ethereum-commons-co-op-f4771e5f0c83).

## There's a risk of attacks from quantum computing, if it becomes performant enough



# How do you buy and sell Ether, the currency of Ethereum?

Summary: compare deals with buying and sell through different exchanges such as P2P ones with an arbitrator like [**LocalEthereum**](https://localethereum.com), or with centralized exchanges (which vary with your local jurisdiction, e.g. in Australia there is [BTCmarkets](https://btcmarkets.net/fees) and in the US plus worldwide there is [Coinbase](https://www.coinbase.com), which also allows you to spend cryptocurrencies e.g. with a debit card. For more see the table below, [here](https://github.com/jamesray1/Ethereum-introduction/wiki/Ethereum-introduction#table-of-exchanges)).

The simplest way may be to use [**LocalEthereum**](https://localethereum.com), where you don't need to go through KYC processes. The creator of this wiki has found that "I can **sell Ether for a better deal** than I can find with an exchange, without any trading or withdrawal fees, apart from those associated with different payment methods". I like that it has lower fees compared to exchanges. I also read about how the localethereum platform works, and it seems pretty secure. The maker fee (which is what the party of the trade that puts up the offer) is 0.25% while the taker fee is 0.75%. [BTCmarkets](https://btcmarkets.net/fees) has higher fees compared to the maker fee, and higher than the taker fee if the amount is below $3000. Additionally, there have been hacks with exchanges like Mt. Gox and Bithumb. That's harder to achieve with localethereum since they can't get access to your funds, they can only settle disputes (if they arise) by sending the funds in the escrow to the buyer or the seller. However, I have been able to find a **better deal**, at least at certain times, with buying Ether if I buy through [**BTCmarkets**](https://btcmarkets.net/fees), although that would be slower via BPAY with bank transfer than paying via cash deposit via an ATM or bank."

More information about Ether is [here](http://ethdocs.org/en/latest/ether.html).

Disclaimer from the creator of this wiki: I have put most of my funds in Ether, the currency of Ethereum. Does that shock you? 😲 Yep, it's risky, but I've done due diligence 🔍 with fundamental analysis, and a little bit of sentiment and technical analysis, and I think that the market cap of Ether will continue to grow 🌱🌳 and increase, albeit with some volatility 📈. I consider it a digital currency that is in a pioneering, rapid growth stage of development (fuelled by a lot of genuine uptake of the currency as well as speculation about its future value, if not its current value), not just an asset. I hope my post will outline why investing in Ether is a good idea (and not just investing a small percentage of your cash, unless you are tied up with a mortgage 🏠 or have other monetary or non-monetary ties or circumstances that limit your investable capital). However, if you don't want to risk the downside volatility, e.g. if you don't have any risk capital, then you may want to consider buying a stable coin like [Dai Coin](https://makerdao.com/) instead.

Based on my research below, <strong>if you're in Australia</strong> 🇦🇺 (otherwise skip to the next paragraph), I recommend creating an account with BTCmarkets.net, verifying your ID, and using BPAY or PoliPayments to deposit AUD. Note that the rest of the following info in this paragraph is outdated since MEBank used to not support BPAY publicly, but now they do. If your bank doesn't support PoliPayments and you want to use that instead of BPAY, although I can't think of any good reason why you'd want to use PoliPayments over BPAY, then you can set up a bank account with BOQ (or <a href="https://www.polipayments.com/Banks" target="_blank" rel="noopener">any other bank that supports POLi Payments</a>, <a href="https://www.marketforces.org.au/banks/compare" target="_blank" rel="noopener">doesn't invest in fossil fuels</a> and <a href="https://www.choice.com.au/money/banking/savings-options/articles/top-fee-free-bank-accounts" target="_blank" rel="noopener">has no fees for transaction and savings accounts</a> [note that Bendigo doesn't have an Ultimate Everyday account any more]. You can probably ignore the interest rate since it is so marginal compared to other bank's rates and the gains that you are likely to make by holding funds with Ether instead. However, when I did research for interest rates I found that ME Bank savings rates were second only to UBank, which invests in fossil fuels indirectly as it is owned by NAB.

## Table of exchanges

You can use [localethereum](https://localethereum.com) anywhere, but in my experience you may not get as good a deal as buying it on a local exchange.

<strong>If you're not in Australia 🇦🇺</strong>, then here's a comparison of exchange rates 💱 for fiat to crypto- and crypto- to crypto- currencies (note that there is still a focus on Australia, but you can click on the links to get more information of the fees):
<table border="1" width="643" cellspacing="0" cellpadding="4"><colgroup> <col width="314" /> <col width="311" /> </colgroup>
<tbody>
<tr valign="TOP">
<td width="314"> Exchange link (may go to a fees page)</td>
<td width="311">Fiat to crypto exchange fee buy/sell spread</td>
</tr>
<tr valign="TOP">
<td width="314" height="10"><a href="https://btcmarkets.net/fees" target="_blank" rel="noopener">BTCmarkets</a></td>
<td width="311">AUD is the only fiat currency. 0.85% trading fee. Funds available to deposit, withdraw, buy and sell are: AUD, ETH, ETC, BTC and LTC. To deposit AUD, you need to verify your account and then use POLi Payments (available banks are <a href="https://www.polipayments.com/Banks" target="_blank" rel="noopener">here</a>. My bank is ME Bank, which doesn't support POLi Payments. I applied with BOQ because they do support POLi Payments, they <a href="https://www.marketforces.org.au/banks/compare" target="_blank" rel="noopener">don't invest in fossil fuels</a> [like ME Bank] and they have <a href="http://www.boq.com.au/day2day.htm" target="_blank" rel="noopener">transaction and savings accounts with no fees</a> [like ME Bank].)</td>
</tr>
<tr valign="TOP">
<td width="314" height="10"><a href="http://gdax.com/fees/" target="_blank" rel="noopener">GDAX</a></td>
<td width="311">0.25/.3% maker fee low volume, 0% taker fee BTC for USD/EUR/GBP, ETH/LTC for USD/BTC/EUR</td>
</tr>
<tr valign="TOP">
<td width="314" height="10"><a href="https://www.coinjar.com.au/">Coinjar</a>, has a debit card with accounts for Ether, BTC, XRP and LTC.</td>
<td width="311">1.00% AUD/BTC (and only AUD/BTC)</td>
</tr>
<tr valign="TOP">
<td width="314" height="10"><a href="https://support.coinbase.com/customer/en/portal/articles/2109597">Coinbase</a></td>
<td width="311">In Aus: 3.99% credit/debit card only (no other transaction method accepted). Other fiat and crypto currencies are available.</td>
</tr>
<tr valign="TOP">
<td width="314"><a href="https://cex.io/" target="_blank" rel="noopener">CEX</a></td>
<td width="311">7% BTC/ETH for USD/EUR/GBP/RUB</td>
</tr>
<tr valign="TOP">
<td width="314"><a href="https://ice3x.com/" target="_blank" rel="noopener">ice3x</a></td>
<td width="311">1% ZAR/BTC</td>
</tr>
<tr valign="TOP">
<td width="314"><a href="https://www.luno.com/trade/XBTZAR" target="_blank" rel="noopener">Luno</a></td>
<td width="311">ZAR/BTC fee not easy to find</td>
</tr>
<tr valign="TOP">
<td width="314"><a href="https://www.okcoin.cn/" target="_blank" rel="noopener">okcoin</a></td>
<td width="311">CNY/USD for BTC/LTC, fee not easy to find</td>
</tr>
<tr valign="TOP">
<td width="314"><a href="https://www.maicoin.com/">Maicoin</a></td>
<td width="311">TN/BTC Fee not shown, claimed none</td>
</tr>
</tbody>
</table>
<table border="1" width="100%" cellspacing="0" cellpadding="4"><colgroup> <col width="128*" /> <col width="128*" /> </colgroup>
<tbody>
<tr valign="TOP">
<td width="50%"></td>
<td width="50%">Crypto to crypto exchange fee / buy/sell spread</td>
</tr>
<tr valign="TOP">
<td width="50%" height="21"><a href="https://poloniex.com/">Poloniex</a></td>
<td width="50%">0.15/.25% BTC/ETH “maker-taker” (presumably the same as buy-sell)</td>
</tr>
<tr valign="TOP">
<td width="50%"><a href="http://gdax.com/fees/">gdax.com/fees/</a></td>
<td width="50%">0.25/.3% maker fee low volume, 0% taker fee ETH/LTC for BTC</td>
</tr>
<tr valign="TOP">
<td width="50%"><a href="https://www.exodus.io/">Exodus</a></td>
<td width="50%">~0.4720–.528% BTC/ETH variable. Desktop app. Sometimes currencies are not available for exchange!</td>
</tr>
<tr valign="TOP">
<td width="50%"><a href="https://nvo.io/">NVO</a></td>
<td width="50%">Decentralised. ICO 05/27/17 02:00 UTC to 06/27/17 02:00 UTC</td>
</tr>
</tbody>
</table>

Exchanges are listed in [this article](https://medium.com/@Ethereum_AI/ethereum-introduction-what-exactly-is-it-why-care-how-to-invest-9a627ab04408), which I've copied and pasted (repetition is OK):

**America, US dollars**

https://www.coinbase.com

https://gemini.com/

**Australian Dollar**

https://btcmarkets.net/

**Canadian Dollar**

https://www.quadrigacx.com

**Chinese Yuan**

https://www.huobi.com

https://www.okcoin.com

https://yunbi.com

**European Euros**

https://www.kraken.com

**India**

https://duckduckgo.com/?q=Ethereum+exchange+india&t=brave&ia=web

**Mexican Peso**

https://bitso.com

**South Korean Won**

https://coinone.co.kr/

https://www.bithumb.com/

## More details about what I've tried (not very necessary to know)

I bought BTC in a different way. Step 2 worked for me. I will try step 1 next time I buy more BTC.

I created an account on btcmarkets.net, verified my email address, tried to login, but couldn't. I tried to disable two factor authentication, but it asked for my mobile number, which I didn't enter when I created my account. I tried to reset my password and log in again, but that didn't work. I entered my email address correctly. I sent a message to support to ask for help. I figured out that my password was too long, and have notified btcmarkets.net. After creating a password of 23 characters, I was able to log in. I created another password of 56 characters using Chinese characters, emojis and ASCII characters, and was able to log in. (Incidentally, it is a hassle to do this, so a decentralised, private password generator would be wonderful.)

The following steps are what I have tried so far, in detail.
<ol>
	<li>Convert AUD to Bitcoin (BTC) via an exchange like <a href="https://www.coinjar.com/" target="_blank" rel="nofollow noopener noreferrer">Coinjar</a>. I left a review of Coinjar <a href="https://au.trustpilot.com/reviews/59391a3d20b79f0b9c73a629" target="_blank" rel="noopener">here</a>. Fee: 1%. While this step is tailored for Australia, it can be adapted to any country that has a bitcoin exchange.</li>
	<li>I created a <a href="https://poloniex.com/" target="_blank" rel="noopener">Poloniex</a> account, sent funds from got the Bitcoin deposit address, then sent my BitGo funds to that address. Once the transaction completed, I then used Poloniex Exchange to convert to Eth, with a maker-taker fee of: <a class="standard" href="https://poloniex.com/feeTier"><span class="makerFee">0.15</span>/<span class="takerFee">0.25</span>%</a>.</li>
</ol>
Before step 2 above, I tried the following:
<ol>
	<li>Create an Ethereum Wallet 👛 like <a href="https://www.myetherwallet.com/" target="_blank" rel="nofollow noopener noreferrer">MyEtherWallet</a>. Unfortunately <a href="https://github.com/ethereum/mist" target="_blank" rel="noopener">Mist</a>, a program being developed by Ethereum that has a browser and a wallet is <a href="https://github.com/ethereum/mist/releases" target="_blank" rel="noopener">still a pre-release</a>, so it may not be very suitable for end users just yet.</li>
	<li>Convert BTC to Ether (ETH) via a cryptocurrency exchange like <a href="https://shapeshift.io/" target="_blank" rel="nofollow noopener noreferrer">Shapeshift</a>. Use a precise transaction, make sure the amount is within the min. and max. deposit. Copy and paste the bitcoin address in Coinjar by signing in (if you aren't already) and going to Accounts > Everyday Bitcoin > View address. Put this address as the refundable address. Copy and paste the address from <a href="https://www.myetherwallet.com/" target="_blank" rel="nofollow noopener noreferrer">MyEtherWallet</a>. Put this as the destination address. Tick the box to save the destination address for future payments. However, this didn't work.</li>
</ol>
I also sent an email to Coinjar to suggest that they develop functionality for purchasing Ether directly using AUD. You can do the same with the exchange of your choice.

I have been able to deposit funds into Coinjar (needing to be less than the transaction limits) but have not been able to get Eth funds as of yet. I have also tried to create an account on BitGo, send coins from Coinjar to BitGo, and use Shapeshift with that account and still with MyEtherWallet, however that didn't work.

I will not discuss trading (which I mean buying with the intention to sell most or all of the purchase at any future time particularly in the short term in order to realize a profit) as I am not very interested in trying to guess the short-term direction of markets (although I admit that trading helps to provide liquidity).

# Development
Are you interested in learning to develop smart contracts with Ethereum, and maybe develop a really useful dapp and become a millionaire?

Check out the [Ethereum website](https://www.ethereum.org/)! Then, you can [read the Solidity docs](https://solidity.readthedocs.io/en/develop/).

If you want to help contribute to core development, there is also:
* the [Yellow Paper](https://github.com/ethereum/yellowpaper/pull/376) (make sure that you read the [EIPs](https://github.com/ethereum/EIPs) too since as of Dec 8 it is not up-to-date with the last commit on August 8, while the Constantinople EIPs were implemented in October). Instead I recommend ;
* Learn Python first, e.g. with [Learn Python the Hard Way](https://www.learnpythonthehardway.org/) (I learnt using this, it's pretty good), [Codecademy](https://www.codecademy.com/learn/learn-python), [Pydocs](https://docs.python.org/3/), [Coursera](https://www.coursera.org/courses?languages=en&query=learn+python), etc. Knowing Python is useful for [pyethereum](https://github.com/ethereum/pyethereum), which is being used as an Ethereum client, to implement Serenity and sharding, as well as [vyper](https://github.com/ethereum/Vyper), an experimental, secure smart contract programming language;
* [LLL](https://media.consensys.net/an-introduction-to-lll-for-ethereum-smart-contract-development-e26e38ea6c23) (also see [here](https://github.com/ethereum/solidity/tree/develop/liblll) and [here](https://github.com/ethereum/solidity/tree/develop/lllc));
* [JULIA](https://solidity.readthedocs.io/en/develop/julia.html), an intermediate language for different Ethereum virtual machines;
* clients such as [Geth](https://github.com/ethereum/go-ethereum), [Parity](https://github.com/paritytech/parity) which is under [Parity Tech](https://github.com/paritytech) a separate organization to the Ethereum Foundation, [C++ Ethereum](https://github.com/ethereum/cpp-ethereum), [Pyethereum](https://github.com/ethereum/pyethereum); 
* [Serenity](https://github.com/ethereum/pyethereum/tree/serenity);
* [sharding](https://github.com/ethereum/sharding/blob/develop/docs/doc.md);
* [research](https://github.com/ethereum/research) such as stateless clients, sharding, scalability improvements, Casper and more;
* [EWasM](https://github.com/ewasm);
* if you're interested in testing, see the documentation [here](https://ethereum-tests.readthedocs.io/en/latest/), as well as [the Github tests repo](https://github.com/ethereum/tests), [a Gist here (it is outdated)](https://gist.github.com/Souptacular/fd197b1fac7c6d2660b0bef27a33ed40#lll-and-evm-stack-resources),  and [Gitter here](https://gitter.im/ethereum/tests) ; and 
* [many other repositories](https://github.com/ethereum).

# Concluding remarks

Ether certainly seems like a good investment, and a good alternative to using fiat currencies, as well as an enabler for otherwise uneconomical business, due to lower transaction costs. It's more decentralized nature than central banks has advantages for trade from a local to global scale. With governance applications and systems on top Ethereum, it is even possible to do away with the hindering borders surmounted by nation-states. By doing away with these borders, society can be more open, inclusive and equitable.

However, all technology can only help mankind and the world to a certain extent. What is more important is for each and every person to become increasingly blissful. Each person must go within and enter a stillness of body and mind, which is when that bliss starts to manifest, and practice balanced living. Practicing certain techniques such as those given by Self-Realization Fellowship, such as daily Kriya yoga meditation, developing unconditional love that starts in the heart, keeping the mind at the point between the eyebrows, and moral living, helps each person manifest that bliss within, and from there, express that bliss outwardly at all times.

# Further reading

* [Another introduction](https://github.com/jamesray1/Ethereum-introduction/wiki/Ethereum-introduction)
* [MyEtherWallet knowledge base (good for issues with wallets)](https://myetherwallet.github.io/knowledge-base/)
* [An introduction (Frontier first release, outdated)](https://ethereum.gitbooks.io/frontier-guide/content/ethereum.html)
* [Here's another introduction, made in November 2017](https://medium.com/@Ethereum_AI/ethereum-introduction-what-exactly-is-it-why-care-how-to-invest-9a627ab04408)
* [Ethereum community on Gitter](https://gitter.im/ethereum)
* [Ethereum research forum](https://ethresear.ch/)
* [Correct by construction Casper prototype](https://ethresear.ch/t/the-correct-by-construction-casper-paper-prototype-published-at-devcon-tear-it-apart/196)
* [Casper the Friendly Finality Gadget](https://ethresear.ch/t/latest-casper-basics-tear-it-apart/151/57)
* [The stateless client concept](https://ethresear.ch/t/the-stateless-client-concept/172)
* [Ethereum 2 and alternative PoS implementations](https://ethresear.ch/t/ethereum-2-and-alternative-pos-implementations/190/7)
* [Ethereum wiki](https://en.wikipedia.org/wiki/Ethereum)
* [Ethereum and the hodlers that love them](https://www.reddit.com/r/ethtrader/comments/6jyn9y/ethereum_the_hodlors_that_love_them/)
</ul>

This article was originally created here in May 2017, and has been regularly updated since then: https://sustergy.wordpress.com/2017/05/18/why-buy-ether-and-how/. Feel free to send a donation to the initial author at jamesray.eth, or make edits to it yourself, or fork it!
