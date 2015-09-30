---
name: Layers
category: 
---

## Networking daemon

Purpose: the networking daemon must be able to connect to other Ethereum nodes, and accept and share data, so as to serve as the backbone for the Ethereum network. The daemon itself should be as neutral as possible; ideally, it should be useful as part of any currency or cryptographic protocol without modification. It should include anti-DDoS features; perhaps maintain a running score preventing any single IP from making more than one request per second.

Interface:

1. every time the daemon receives a new message (ie. H(M) is not equal to H(Mprev) for any Mprev received previously), it should send a POST request to http://localhost:<outputport> containing the data, where outputport can be set in the ~/.ethereum/ethereum.conf file (default 1242 if not set)
2. the daemon should listen on port <inputport>, where inputport can be set in the ~/.ethereum/ethereum.conf file (default 1243 if not set), and if it receives a message in a post request it should push the data out to all connected nodes in the network.
Dependencies: internet, bootstrapping nodes

## Ethereum Core

### Communication layer

1. Process_network_message(string) -> msg or transaction or block
2. Create_network_message

### IO Layer (ie. usually database)

1. Insert object(value)   (key = sha256(value))
2. Get object (key) -> value

### Data layer

1. get_transaction
2. get_block
3. get_address_balance / nonce
4. get_contract_memory_at_index
5. get_contract_root
6. get_object (Merkle trie nodes, etc)
7. block class
8. transaction class

### Manager

1. get_latest_block
2. add_block (including validation and total difficulty calculation)
3. apply_transactions_to_block (for miners)

### Application Layer

1. Wallet
2. Full
3. SPV
4. Graphical block explorer
5. Miner

## Classes

### Block class

Purpose: the block class should be able to parse blocks, serialize blocks and handle certain updating operations to blocks.

Interface:

1. deserialize: string -> void (constructor)
2. get_balance: number address -> number
3. get_contract_state: number address, number index -> number
4. update_balance: number address, number newbalance -> void
5. update_contract_state: number address, number index, number newvalue -> void
6. get_contract_size: number address -> number
7. serialize: void -> string

### Transaction class

Purpose: the transaction class should be able to parse transactions, sign transactions and serialize transactions. Note that the serialize method should fail for transactions without a signature; transactions that are created inside a block and not deserialized should never actually be stored or serialized in any form.

Interface:

1. deserialize: string -> void (constructor)
2. sign: privkey -> void
3. serialize: void -> string
4. create: number address, number value, number fee, array[number] data -> void (constructor)
5. hash: void -> number
6. to, from, value, fee, data (accessible and settable member variables)
