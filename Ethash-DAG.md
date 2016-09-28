Ethash is the PoW system. It requires a ~1GB dataset known as the DAG (see [Dagger Hashimoto](https://github.com/ethereum/wiki/blob/master/Dagger-Hashimoto.md)). This typically takes hours to generate so we tend to memorise it. Clients wishing to store the DAG in a cache should conform to this spec in order to share the cache with other clients:

#### Location

The DAG should be stored in a 1GB dump (for the initial epoch, anyway), in a file:

- Mac/Linux: `$(HOME)/.ethash/full-R<REVISION>-<SEEDHASH>`
- Windows: `$(HOME)/Appdata/Local/Ethash/full-R<REVISION>-<SEEDHASH>`

Where:
 
- `<REVISION>` is a decimal integer, given as the C-constant `REVISION` in `libethash/ethash.h`;
- `<SEEDHASH>` is 16 lowercase hex digits specifying the first 8 bytes of the epoch's seed hash.

There may be many such DAGs stored in this directory; it is up to the client and/or user to remove out of date ones.

#### Format

Each file should begin with an 8-byte magic number, `0xfee1deadbaddcafe`, written in little-endian format (i.e., bytes `fe ca dd ba ad de e1 fe`).

The Ethash algorithm expects the DAG as a two-dimensional array of uint32s (4-byte unsigned ints), with dimension (n &times; 16) where n is a large number. (n starts at 16777186 and grows from there.) Following the magic number, the rows of the DAG should be written sequentially into the file, with no delimiter between rows and each unint32 encoded in little-endian format.