Mining will be accomplished in one of two ways: either on CPU with the Mist client or on GPU though a combination of the Ethereum daemon and an external GPU-capable mining application.

### JSON-RPC

Communication between the external mining application and the Ethereum daemon for work provision and submission happens through the JSON-RPC API. Two RPC functions are provided; `eth_getWork` and `eth_submitWork`.

