**Note** We are still in the process of finalising the software infrastructure for mining, so this information is provided on a best-effort basis. Expect to finalisation to happen imminently.

## The Algorithm

Our algorithm, Dashimoto (Dagger-Hashimoto), is based around the provision of a large, transient, randomly generated dataset which forms a DAG (the Dagger-part), and attempting to solve a particular constraint on it, partly determined through a block's header-hash.

It is designed to hash a fast verifiability time within a slow CPU-only environment, yet provide vast speed-ups for mining when provided with a large amount of memory with high-bandwidth. The large memory requirements mean that large-scale miners get comparatively little super-linear benefit. The high bandwidth requirement means that a speed-up from piling on many super-fast processing units sharing the same memory gives little benefit over a single unit.

### Formal Requirements

TODO: Content from formal requirements doc.

### Design Decisions Taken

TODO: Content from design decisions doc.

## Infrastructure Overview

Mining will be accomplished in one of two ways: either on CPU (and possibly the GPU, to be confirmed) with the Mist client or on the GPU though a combination of the Ethereum daemon and [sgminer](https://github.com/sgminer-dev/sgminer).

An sgminer module for Dashimoto is expected to be released by the 1st of March.

### JSON-RPC

Communication between the external mining application and the Ethereum daemon for work provision and submission happens through the JSON-RPC API. Two RPC functions are provided; `eth_getWork` and `eth_submitWork`.

These are formally documented on the [JSON-RPC API](https://github.com/ethereum/wiki/wiki/JSON-RPC) wiki article.


