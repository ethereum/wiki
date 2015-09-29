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

### Web of trust

Basically, implement http://www.advogato.org/trust-metric.html (a trust score scheme based on max-flow) on Ethereum. Create a dapp by which anyone can register an identity, and register trust scores for other identities. Then, create a decentralized cloud computing service by which anyone can query "provide a proof, consisting of a list of augmenting paths, showing the Advogato trust score from A to B" and anyone can reply in exchange for a micropayment (or perhaps create a server that does it for free, and convince a charity to subsidize it); if user A wants to know "what is B's trust score", they can make this query, receive the result, verify the proof, and then display it.

### Financial Derivatives Market

Essentially, a polished, working dapp that allows users to make options on any ethereum asset, and derivatives (eg. CFDs) on any ethereum price feed. This should support "multisig price feeds": choosing multiple data feed contract addresses that support a particular ticker symbol, and taking the median of them, so as to remove reliance on any single party.

Supported actions should ideally include:

* Entering into CFDs with other parties on any price index, settling in any standards-compatible currency or asset (though perhaps start with ETH)
* Options between any two assets (though perhaps start with asset<->ETH)
* Plain old regular asset exchange
* An order book for all of the above

### RANDAO

Work on [this](https://forum.ethereum.org/discussion/2031/randao-a-dao-working-as-rng). Set it up as a decentralized service which any lottery or other randomness-based game can use; also, build a "full node software" package/plugin which facilitates participating in the RANDAO by providing random numbers.

### Interface with national ID

Create a system, relying on trusting no one other than the original issuer, by which users with electronic identities (eg. Estonian digital ID, other electronic passports, crypto KYC schemes, etc) can prove to the ethereum blockchain that they have that particular ID. Note that this can be plugged into the WoT by, eg, creating a contract which trusts everyone who has an Estonian digital ID with score 1.

### Zero knowledge proofs

Create multiple compatible implementations of a ZK-SNARK protocol.

### Ultrahard KDFs for brainwallets

A decentralized paid cloud computing service for brainwallet computations, combined with a client-side solution, in order to implement my proposal for ultra-secure brainwallets using blind-outsourceable ultrahard KDFs: https://blog.ethereum.org/2014/10/23/information-theoretic-account-secure-brainwallets/