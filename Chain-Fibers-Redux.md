---
name: Chain Fibers Redux
category: 
---

# Blockchain Scalability: Chain-Fibers Redux

## History

I came up with the first seed of this idea while chatting to Janislav Malahov in Berlin in Spring 2014. Unfortunately, the original article I wrote was lost along with my laptop when it was stolen in Vienna. After chatting over the principles with Vitalik more recently, we made a number of alterations and formalisations, mainly to the validation and the sub-state cutting mechanisms. What follows is a fairly complete picture of one particular possible plan for block chain scalability in a later version of Ethereum.


## Overview

The basic idea of Chain-Fibers is unchanged from a year ago; split the state-space up into strata and have separate transaction collators specialising in one or a number of state sub-spaces. Transactions requiring interactions from many a subspace would be accordingly more expensive (since collators would have to maintain presence on multiple chains) and take longer to execute (since there is a lesser chance that any given block would contain a superset of the transaction's subspaces). Validity of a transaction is verifiable in isolation through the provision of comprehensive Merkle proofs to its inputs alongside it in the block in which it is included.

The subtleties lie in precisely what governs the division of subspaces (my original proposal included the automated splitting, merging and rotation of subspace-divisions in order to best deliver internal coherency), how security is maintained within comparatively worthless subspaces and how this can play well with Proof-of-Stake (the original was based upon a master PoW chain, feeding off an idea put forward by Max Kaye in early 2014 to disassociate block chain archival from transition semantics).

Basic idea is to have a number of chains (e.g. N), each detailing the state-transitions for only a strata of the entire system state (i.e. a state subspace). Following from programming terminology, these might be termed "fibers". Accounts thus belong to a subspace and as such a single fiber; the fiber to which they belong can be determined simply from the first log2(N) bits of the address. N can increase or decrease, and is a value maintained within the housekeeping information on the "Master Chain".

The Master Chain in maintained by a set of bonded Validators V, with the number of validators proportional to N. A random selection of validators validate each block produced, and validators ultimately vote to form consensus over the Master Chain. Each block of the Master Chain maintains a reference to the header of each fiber.

Transaction collators produce blocks (accepting fees from transactors), and pay Validators some of the fees collected to include the hash of their block in the main chain. Blocks are produced across a particular "home set" of fibers; this is basically just the set of fibers of which they maintain the State Trie. Their blocks may involve transactions over one or many of these fibers, though none outside their "home set".

"Fishermen" is a term given to freelance checkers. Since block validation and availability are both important, and since it is possible that sets of validators may be contractually bribed, it is important to have a mechanism to involve additional rational individuals in acting as "whistle-blowers" to avoid bogging the other validators needlessly checking all blocks. The fishermen basically pay to attempt to convince a quorum of validators that a previously validated block is invalid (or unavailable, which we assume is equivalent). If a fisherman demonstrates a validator (or, more likely, set of validators) acted in a dishonourable fashion, then they get to claim all of their bonds. To avoid DoSing the validators with spurious challenges, a fee is payable.


## Schematic

Sorry for the not-quite ASCII-art. I'm not quite as 1337 at Inkscape as Vitalik.

```
Transactors        ==TX+FEE==>  Collators                     ==BLOCK+FEE==>  Validators
make transaction                 validate transaction,                         random selection chosen to audit
                                produce Comprehensive Merkle                    TX/PSR/CMP contents & availability,
                                  Proof and Post State Root,                  all placed in PoS-consensus master block
                                collate into X-fiber Block                   

                                Fishermen                 ==CHALLENGE+FEE==>  Validators
                                search for invalid or                         a selection adjudicate challenge
                                  unavailable X-fiber blocks                 
```


## Transactors

Transactors are pretty much exactly the same as in Ethereum 1.0 - they are the users of the system.

### Transactors: make transaction

Transactors make a transaction much like they do in the existing Ethereum system. One or two minor differences - addresses can be used as a distance metric; those sharing the same number of initial bits are considered "closer", which means a greater certainty into the future that they will continue to be contained in the same state subspace. Contracts are naturally created in the same state subspace as the creator.

Transactions, like Collators, operate over a number of fibers; perhaps one perhaps all, probably somewhere in between. Submission to collators may be directed through fiber sub-network overlays.

Submission and payment to the collators happens much as existing transaction submission to miners happens in Ethereum 1.0.


## Collators

Collators maintain presence on at least two peer sub-network overlays; the Validators overlay, and one or more fiber overlays. The fiber overlays may provide directed transaction propogation. Collators "collate" on a set of fibers. They maintain a full fiber-chain for each fiber they collate over, and can accept all transactions that involve any combination of their fiber set. The greater this combination, then the greater their "transaction net", but the greater their overall disk/memory footprint.

### Collators: validate transaction

On receipt of a transaction, they go through the usual Ethereum 1.0 rites of checking payment is enough, initial balances &c. Once basic validation is done, they attempt to execute it, throwing it out if it touches any fiber that is not part of collator's fiber set.

### Collators: produce Comprehensive Merkle Proof and Post State Root

Collators provide each post-state-root (as is found in the transaction receipt of Ethereum 1.0) and append to the block Merkle proofs and associated hints (e.g. contract code) for all inputs (balance, nonce, state, code) from all subspaces that are required for the evaluation of each transaction from a previously known post-state-root.

This allows an auditor to, without anything other than the previous post-state-root for each fiber, determine the validity of the block.

### Collators: collate into X-fiber Block

A Cross Fiber Block is created from the total information collated. This includes transactions, transaction receipts (post-state-roots), Comprehensive Merkle-Proofs and associated hash-hints. This block does not include any consensus-specific information such as timestamping, uncles &c.


## Validators

Validators (who might be better named auditors) are bonded particpants, chosen regularly from the highest bidders, who take a small fee for the ultimate maintenence of the network. Their job, as a whole, is to form a judiciary and ultimate authority over the validity and transaction contents of the chain. We generally assume that they are mostly benevolent and cannot all be bribed. Being bonded, validators may also be called to audit and stake their bond on an opinion over validity or information-availability.

### Validators: all placed in PoS-consensus master block

They maintain signing control over the Master Chain. The Master Chain (MC) encodes all PoS/consensus stuff like timestamping and includes its own little state root for recording validator's bond balances, ongoing challenges, fiber block header-hashes and any other housekeeping information.

Each master block (MB), a set of collated X-Fiber Blocks (XBs) are taken; these must be non-overlapping, so that each fiber belongs to only a single XB.

### Validators: random selection chosen to audit TX/PSR/CMP contents & availability

For each MB we have a number of XSBs referenced from the MB's Trie. Each fiber is assigned a randomly selected set of validators, and the validators must review whatever XB contains their assigned fiber. Validation includes attaining the XB, finding the previous PSRs for each of the fibers (placed in the MB) and checking that the proofs in its CMP, cover all required inputs to the transactions collated within and that the PSR is indeed the final state root when all are executed.

The block is considered valid iff all assigned validators sign it. Signing it is considered an assertion that the block contents are both valid and available for a probabilistically long "challenge period" in which a Fisherman may challenge. Any challenge to the block's validity which is ultimately upheld by a full consensus of a randomly selected set of validators (ultimately ending with a majority vote, should it be doggedly contested) will mean the instant loss of the bond.


## Fishermen

Fishermen (who might be called bounty hunters) are the freelance error-checkers of the system. The watch the validators in the hope that they can find wrong-doing. To help guarantee presence, payouts are designed to be huge. The costs of challenging are small but not insignificant.

### Fishermen: search for invalid or unavailable X-fiber blocks

They check the X-fiber blocks looking for validity errors and/or inavailability of data. When they find an invalid block or unavailable data, they launch a challenge (for a small fee, paid to validators) in the hope that a sufficiently large portion of validators will concur. If they succeed and validators ultimately uphold the challenge, then they receive the bonds of all validators who had previously asserted validity/availability of the information.

### Fishermen's Challenge:

1. Fisherman finds an invalid/unavailable block not yet outside its "challenge period" (10-30 blocks); pays a fee, submits a challenge transaction into the master chain;
2. A randomly selected set of validators (e.g. of order e.g. sqrt(N)) ++ any validators that self-select (through doubling their bond), check the block that was challenged; each votes Y or N to the block's validity;
   - If N, the validator receives a small payment Pn.
   - If Y, the validator stakes their bond, though receives a larger payment Py (perhaps Py = 2Pn).
3. The outcome of the challenge (probably accumulated into the following block) is:
   - If more than 66% of validators vote Y (valid), then the challenge ends. The Fisherman loses their fee, but may reinitiate a challenge.
   - If at least one validator votes Y (valid), then the challenge continues with a second, larger set of randomly selected validators. All bonds are staked.
   - If all validators vote N (invalid), then the block is recorded as invalid and the Fishermen receives the bond of all validators that have asserted the blocks validity. This is a very large payoff.
   - NOTE: If the set includes all validators, then it's a simple majority-carries rule.


## Other differences

- All addresses are contained in a lookup table unique to each state subspace; this means they can be referenced through a small number of bits and avoid large amounts of wasted entropy in the RLP for proofs &c.

## Notes

- Once a block is out of the challenge period, it is considered unassailable. If it does turn out to be bad, then it must be fixed in the same way as a protocol upgrade. As such it is likely that validators and other large stakeholder would act as Fishermen to protect their investment.
