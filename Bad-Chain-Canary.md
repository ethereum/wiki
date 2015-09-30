---
name: Bad Chain Canary
category: 
---

There will be a canary contract to notify that a given chain is bad. It's very easy to use; check storage location 0 of contract at the given address (see below). If non-zero, client should not mine (at least without a non-default option being given to ignore the canary and mine on a known-bad chain).

Specifically there are three modes it can be in:

- `0` All fine. Carry on.
- `1` Bad chain. Client should not mine on it. Client upgrade not yet available.
- `2` Update required. Just as for `1`; additionally, an update to your client is available and would be prudent.

Clients implementing this protocol should display a message to the user to make any non-zero status clear. For a status of 2, the user should be notified than an immediate upgrade is required, regardless of whether mining is enabled.

#### Addresses

- For Olympic: `0x6879392ee114f8a4e133f0ff3dc4bc1717fe9344`
- For Frontier: TBC
