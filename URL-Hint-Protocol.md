---
name: URL Hint Protocol
category: 
---

There exists a root `Registry` contract at address 0x42 (this is the auctioning thing). There exists a contract interface, `register`, which `Registry` implements.

Entries in `register`, when looked up (indexed by a string32 name) have several value fields, including `content` and `register`.

`content` is the content hash which gets loaded when you enter this name into Mist/AZ.

`register` is the address of a subregistry contract (some contract implementing `register`) which gets queried recursively.

### Example

- `eth://gavofyork` results in the main Registry (at 0x42) being queried for the entry `gavofyork`. The content field of this entry (a SHA3 hash of a zip file OR manifest) is used to display content (see Content section for more info).

## URL Composition

All URLs canonically begin `eth://` and are a number of components, each separated by a `/`. The field `register` is used to do a recursive lookup.

So the components of `gavofyork/tools/site/contact` would comprise four components, in order: `gavofyork`, `tools`, `site`, `contact`. 

Any periods `.` before the left-most slash `/` delineate components which are specified in reverse order. 

So non-canonical forms of the above address are:

- `tools.gavofyork/site/contact`
- `site.tools.gavofyork/contact`
- `contact.site.tools.gavofyork`

NOTE: any periods after the leftmost `/` are treated no differently than other alphanumeric characters.

### Example

- `eth://gavofyork/site` results in the main Registry (at 0x42) being queried for the entry `gavofyork`. The register field of this entry (an address of a `register`-implementing contract) is then queried for the entry `site`. The `content` field of this entry is used to display content.

# Content

Normally Swarm would be used to determine the content from the content hash.

For 1.0, this will not be possible, so we will fall back to using HTTP distribution. To enable this we will include one or many URLHint contracts, which provide hints of URLs that allow downloading of particular content hashes. Find the contract in dapp-bin.

The content downloaded should be treated in many ways (and hashed) to discover what the content is. Possible ways include base 64 encoding, hex encoding and raw, and any content-cropping needed (e.g. a HTML page should have everything up to body tags removed).

It will be up to the dapp/content uploader to keep URLHint entries updated.

The address of the URLHint contract will be specified on an ad-hoc basis and users will be able to enter additional ones into their browser.

