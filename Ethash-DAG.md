Ethash is the PoW system. It requires a great huge dataset known as the DAG. This takes a good long while to generate which is a pain. As such we tend to memorise it. Clients wishing to store the DAG in a cache should conform to this spec in order to share the cache with other clients:

#### Location

The DAG should be stored in a 1GB dump (for the initial epoch, anyway), in a file:

- Mac/Linux: `$(HOME)/.ethash/full-R<REVISION>-<SEEDHASH>`
- Windows: `$(HOME)/Appdata/Ethash/full-R<REVISION>-<SEEDHASH>`

Where:
 
- `<REVISION>` is a decimal integer, given as the C-constant `REVISION` in `libethash/ethash.h`;
- `<SEEDHASH>` is 16 lowercase hex digits specifying the first 8 bytes of the epoch's seed hash.

There may be many such DAGs stored in this directory; it is up to the client and/or user to remove out of date ones.
