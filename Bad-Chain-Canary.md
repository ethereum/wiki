There were canary contracts to notify that a given chain was bad. They were very easy to use; check storage location 0 of contract at the given address (see below). If non-zero, client would not mine (at least without a non-default option being given to ignore the canary and mine on a known-bad chain).

Specifically there are three modes they could be in:

- `0` All fine. Carry on.
- `1` Bad chain. Client should not mine on it. Client upgrade not yet available.
- `2` Update required. Just as for `1`; additionally, an update to your client is available and would be prudent.

Clients that implemented this protocol displayed a message to the user to make any non-zero status clear. For a status of 2, the user would be notified than an immediate upgrade was required, regardless of whether mining was enabled.

#### Addresses

- For Olympic: `0x6879392ee114f8a4e133f0ff3dc4bc1717fe9344`
- For Frontier: Multiple people
- For Homestead and later, clients don't support this contract anymore. The last clients removed this in mid-2016.