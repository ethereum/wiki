Every client implementation of the Ethereum protocol – along with every version of it – has a *human readable* string identifier. Although this ID is exchanged in the [P2P protocol](https://github.com/ethereum/wiki/wiki/%C3%90%CE%9EVp2p-Wire-Protocol#p2p), it itself isn't used by the protocol for anything, rather it enables easily localizing issues with a particular client, or a particular version of a client.

Still, there tools popping up that analyze and create various statistics about the network topology itself, which have a hard time conforming to the identifier/versioning schema employed by each implementation. To avoid requiring each such tool to implement a full ID parsing for every type of node in the network, this document outlines a **suggested** schema, part of which would be common among all implementations, and some would be specific to one or the other. This would allow simple tools to properly parse the common information out of each client's ID string, whereas more complex tools might also further inspect the client specific metadata.

*Please note, this document should be perceived as a guideline at most, and definitely not a golden standard to be enforced by any implementation or tool. The goal is to aid the ecosystem relying on it, but it is not something set in stone.*

### Suggested identifier schema

All identifier strings should be a slash separated list of metadata fields: `metadata-1/metadata-2/.../metadata-n`, where the first few fields are fixed among all clients, whereas the latter ones (both in meaning and in count) are up to each implementation to use as seen fit.

The fixed fields are:
 1. Display name of the client, without any version numbering
   * E.g. `Geth`, `++eth`, `Ethereum(J)`
 2. Semantic version, prefixed with `v`, *optionally* suffixed by `-<extra info>`
   * E.g. `v1.3.3`, `v0.9.41-ed7a8a35`, `v1.4.0-unstable`
 3. Name of the operating system the client is running on
   * E.g. `Linux`, `Windows`, `Darwin`, `Android`

As there might be differences in implementation as to how one language or another retrieves the name of the operating system – among others – we recommend tools using the IDs to convert the strings internally to lowercase before making aggregations.

A few examples conforming to the above schema spec:

```
 Geth/v1.3.3/darwin
 ++eth/v0.9.41-ed7a8a35/Windows
 Ethereum(J)/v1.0.0/Linux
```

### Optional extensions

Beside the above defined fixed metadata fields, an identifier string may contain arbitrarily many implementation specific metadata entries (the order and index of which should desirably be maintained by the developers of said client implementations). To help developers working on analysis tools have a central repository of the metadata semantics, the rest of this document describes the extra fields used by each implementation. Feel free to add your own implementation to the below list, but please maintain alphabetical ordering and please be concise.

 * **Geth**: `<go runtime>` / `<jit vm>` / `<custom name>`
   * `Geth/v1.3.3-c541b38/linux/go1.5.5`
   * `Geth/v1.4.0-unstable/android/go1.6beta2/jit/gopher-power`