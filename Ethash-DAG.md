Ethash is the PoW system. It requires a great huge dataset known as the DAG. This takes a good long while to generate which is a pain. As such we tend to memoise it. Clients wishing to store the DAG in a cache should conform to this spec in order to share the cache with other clients:

#### Location

The DAG should be stored in a 1GB dump (for the initial epoch, anyway), in a file:

- Mac/Linux: `$(HOME)/.ethash/full`
- Windows: `$(HOME)/Appdata/Ethash/full`

There should also be a second file indicating the Ethash version and DAG epoch; when wrong or missing, both files should be removed and regenerated.

The latter file is stored:

- Mac/Linux: `$(HOME)/.ethash/full.info`
- Windows: `$(HOME)/Appdata/Ethash/full.info`

It is the RLP encoding of a list of two items:

- **revision**, an integer, given as the C-constant `REVISION` in `libethash/ethash.h`;
- **seedhash**, an 32-byte hash, altered during each epoch-change.
