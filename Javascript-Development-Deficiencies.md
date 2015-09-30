---
name: Javascript Development Deficiencies
category: 
---

The medium-term vision for the Ethereum javascript development tools is simple: to make developing a dapp as easy as developing a web page. However, we are not quite there yet; this page provides a partial list of the problems that dapp developers have to deal with that web developers do not.

### Transaction Asynchrony and Uncertainty

When sending an ajax request to a server, that request (i) processes immediately, (ii) is either answered by the server or fails immediately, and (iii) has an effect that we know is final. When sending a transaction, we lose many of these benefits:

1. When you send a transaction, it takes ~0-30 seconds for the transaction to get included in a block.
2. When a transaction does get included in a block, it could get un-included later due to blockchain reorgs, and could then get double-spent.
3. The transaction gas a gas limit and a gas price, and either of these could lead to failures: in the first case an OOG exception, and in the second case a failure to get the transaction to get included for even longer (possibly forever). It is not possible to tell for sure when this has happened.

We also lack good ways of dealing with the output of non-constant transactions; for example, a common workflow is creating a transaction that registers a new record in a contract and then getting back the ID of that record or a success or failure confirmation, and there currently is no convenient coding pattern for getting that output back.

### Failure and retrying

If a transaction fails for some reason (eg. gas price too low, out of gas), it may be prudent to automatically retry with a higher gas price or gas limit.