The best known tutorial/explanation of the ethereum patricia tree is here:
https://easythereentropy.wordpress.com/2014/06/04/understanding-the-ethereum-trie/

Note that it is outdated in one place; specifically, replace `state = trie.Trie('triedb', trie.BLANK_ROOT)` with `state = trie.Trie(db.EphemDB(), trie.BLANK_ROOT)` (and make sure to run `import rlp; from ethereum import db, trie` beforehand).