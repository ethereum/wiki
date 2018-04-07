This page describes some ideas that I (Vitalik) have that I would like to see implemented, but do not have time for myself. If anyone wants advice on how to implement them feel free to ping me at any time, or just start yourself.

### Data Feed

The core idea behind a data feed is simple: a data feed is a contract with an administrator that maintains a key-value store, where the keys represent what data is stored (eg. "BTC/USD") and the values represent the value that the administrator believes the variable currently is in the real world (eg. 235). A simple data feed contract can be found [in the standardized contract APIs repository](https://github.com/ethereum/dapp-bin/tree/master/standardized_contract_apis/fee_charging_datafeed.sol); it has a `set` function which allows its administrator to set the value for a particular key, and a `get` function which allows anyone to get the value for a particular key; that particular contract also allows the administrator to set a fee in ether.

Regarding fee setting, note that you should not set the fee too high as otherwise it's possible at medium cost to create a "caching passthrough contract": essentially, the smart contract equivalent of piracy, and there's no way to prevent that without compromising the flexibility that makes Ethereum Ethereum; a fee of around 1-10 finney should be fair and work well if you do wish to charge.

The way that you would implement the server side code is simple. In pseudocode, you can implement it as a separate daemon that accesses an api:

    keys = [("BTC/USD", "http://my_exchange.com/api/BTCUSD?password=12345"),
            ("CHF/USD", "http://my_exchange.com/api/CHFUSD?password=12345"),
            ("temperature:Seattle", "http://my_weather_network.com/api/temperature/Seattle?password=12346"),
              ... ]
    for key, url in keys:
        try:
            value = json.loads(open(url))["value"]
            ethereum.send_transaction(ethereum.get_nonce(ethereum.my_address), ethereum.get_gasprice(),
                                     200000, MY_DATAFEED_CONTRACT, 0, MY_DATAFEED_ABI.encode('set', [key, value])
        except:
            pass

(Note: the above is pseudocode; not all of the above methods are available in pyethereum quite yet).

If you control:

* An exchange or other website that has access to price feed or index data
* A weather network
* A site that lists results of real-life events
* A service that KYCs people and assigns them unique identity tokens
* Any other data repository

Then the Ethereum ecosystem will benefit from you publishing a data feed contract on the ethereum blockchain.

A closely related idea is an **HTTP getter passthrough contract**. Essentially, the contract would have one method, `get(string)`, that would take a URL as an argument, and simply log the URL alongside the address that asked for the get. A server daemon would make a curl request to the URL, and call the `passthroughCallback(string)` method of the sender address with the output. Note that multiple daemons running in this form could be called in multisig fashion if desired.

### Web of trust

Reputation and credit scores are potentially a very powerful economic lubricant in modern society, allowing us to easily tell reliable from unreliable parties, and are even more important in a decentralized world where centralized curation is unavailable and many classes of regulation become more difficult to enforce. The question of "can this user be trusted?" is approximated with a simple and easy-to-understand number.

Currently, such systems are actively being looked into and adopted by private companies particularly in the "sharing economy":

<center>
![](http://www.kellydessaint.com/wp-content/uploads/2014/08/lyft_ratings3.jpg)
</center>

And even governments:

<center>
[![](https://www.privateinternetaccess.com/blog/wp-content/uploads/2015/10/SesameCredit.jpg)](https://www.privateinternetaccess.com/blog/2015/10/in-china-your-credit-score-is-now-affected-by-your-political-opinions-and-your-friends-political-opinions/)
</center>

However, if we want to create a globally accessible system, centralized credit scores are arguably insufficient: not everyone in the world will be willing to trust a particular entity to provide accurate and fair credit scores, particularly in international cases where there are large political differences. Hence, while centralized credit scores may work in particular closed environments, in an international trade context it would be more ideal to have a global framework that allows specialized scoring agencies to exist, but which allows recipients to choose which ones they "listen" to, and even combine their scores.

A highly effective solution to this problem, which has existed in academia and the public for over a decade, uses the "web of trust" route: essentially, instead of trying to achieve a single universal score for everyone, provide a way for each person to score anyone else from their own point of view. This is accomplished through the trust transitivity heuristic: if A trusts B, and B trusts C, then to some degree that's cause for A to trust C. In some ways, centralized credit scores are a special case of this: everyone trusts the central agency (in theory), the central agency assigns people trust ratings, and so that's how much everyone is induced to trust everyone else. However, the decentralized approach is more general, and ideally allows us to take into account _all_ "chains of trust" between two people, and not just the ones that flow through a particular centralized gateway.

![](http://vitalik.ca/files/wot_centralized_and_decentralized.png?1)

One example of an algorithm that does this well is the [Advogato trust metric](http://www.advogato.org/trust-metric.html), a trust score scheme based on [max-flow](https://en.wikipedia.org/wiki/Maximum_flow_problem).

A useful project would be to create an implementation of such a system on Ethereum, and create a dapp by which anyone can register an identity, and register trust scores for other identities. Then, create a decentralized cloud computing service by which anyone can query "provide a proof, consisting of a list of trust paths, showing the Advogato trust score from A to B" and anyone can reply in exchange for a micropayment (or perhaps create a server that does it for free, and convince a charity to subsidize it); if user A wants to know "what is B's trust score", they can make this query, receive the result, verify the proof, and then display it.

This system could then be used by many other dapps on ethereum, including financial contract using price feeds and arbitration.

### Financial Derivatives Market

Essentially, a polished, working dapp that allows users to make options on any ethereum asset, and derivatives (eg. CFDs) on any ethereum price feed. This should support "multisig price feeds": choosing multiple data feed contract addresses that support a particular ticker symbol, and taking the median of them, so as to remove reliance on any single party.

Supported actions should ideally include:

* Entering into CFDs with other parties on any price index, settling in any standards-compatible currency or asset (though perhaps start with ETH)
* Options between any two assets (though perhaps start with asset<->ETH)
* Plain old regular asset exchange
* An order book for all of the above

### RANDAO

Essentially, [this](https://forum.ethereum.org/discussion/2031/randao-a-dao-working-as-rng). Set it up as a decentralized service which any lottery or other randomness-based game can use; also, build a "full node software" package/plugin which facilitates participating in the RANDAO by providing random numbers.

### Interface with national ID

Create a system, relying on trusting no one other than the original issuer, by which users with electronic identities (eg. Estonian digital ID, other electronic passports, crypto KYC schemes, etc) can prove to the ethereum blockchain that they have that particular ID. Note that this can be plugged into the WoT by, eg, creating a contract which trusts everyone who has an Estonian digital ID with score 1.

### Zero knowledge proofs

Create multiple compatible implementations of a ZK-SNARK protocol.

### Ultrahard KDFs for brainwallets

A decentralized paid cloud computing service for brainwallet computations, combined with a client-side solution, in order to implement my proposal for ultra-secure brainwallets using blind-outsourceable ultrahard KDFs: https://blog.ethereum.org/2014/10/23/information-theoretic-account-secure-brainwallets/

### Estonia ID integration

Create a system which integrates the Estonian digital ID system into Ethereum; essentially, create a registry where someone can link their address to a particular Estonian digital ID by signing a transaction with their ID. The registry should cryptographically verify that the signature is valid and that it matches a particular ID, and should then store a mapping, eg. `address -> (first name, last name, number)`

Some developer resources that can help with this include:

* http://eid.eesti.ee/index.php/Authenticating_in_web_applications
* https://e-estonia.com/e-residents/for-developers/
* https://sk.ee/en/services/testcard/
* http://id.ee/index.php?id=30469

### Security Deposit-backed Conditional Hashcash

The key piece of technology that later spawned the advent of blockchains starting with Bitcoin, proof of work, was originally devised for quite a different application: email spam prevention. In order to send an email that would be viewed by recipients' email interfaces, users would need to complete a certain amount of computational work on their computers that could quickly be verified. This would impose a small cost in electricity and CPU power to sending an email (say, $0.01) that would in theory be no problem for ordinary messages, but would be so expensive as to make spam no longer worth it. In practice, this idea never succeeded, in large part because people did not want to pay for their email and because it would only lead to spammers paying more in order to send more well-crafted emails to fewer parties, which would be more likely to receive attention because users think that they already passed the proof of work filter.

Here, I propose a proof of stake twist on the algorithm that would solve this problem. The key insight is that instead of making the sender _always_ pay the cost, we make the cost a security deposit, and the recipient has the right, only if they wish, to destroy the security deposit at no benefit to themselves (in an actual implementation, "destroy" will hopefully mean something like "donate to protocol developers"). Ordinary "useful" messages would get through unscathed, and spam would be punished in proportion to the percentage of people that find it scammy. The conditional nature of the protocol can theoretically allow the deposits to be increased much higher than the cost of proof of work; deposits of $1 or even higher are quite reasonable.

There are two possible extensions to this idea:

1. Use techniques similar to [anti-pre-revelation games](https://blog.ethereum.org/2015/08/28/on-anti-pre-revelation-games/#comment-2230211912) (or possibly other techniques) to allow users to destroy other users' security deposits (either probabilistically or fully) without the victim knowing who did it. This allows the mechanism to be used more effectively in intra-organizational and social situations, so that you can punish people for being annoying without extra-protocol repercussions. Aside from information-theoretic strategies, there may be routes to doing this using Chaumian blinding schemes, cryptographic accumulators and/or zk-SNARK proofs; the goal to target would be in a scenario with one sender and N recipients, with the sender having a deposit of $NX, giving each recipient the right to destroy $X exactly once without revealing which one of the N they are; the main challenge is the sender gleaning this information by simulating the process of each single recipient destroying their deposit and seeing which one(s) fail, and so private information on the recipients' part and perhaps even an interactive protocol is required.
2. Have different levels of deposits, where higher levels of deposits can be used to signal more importance/urgency in the interface; at the highest levels, one could imagine a deposit of $500 leading to one's phone ringing maximally loudly whereas a deposit of $0.1 would only lead to an email appearing next time one opens one's mailbox.