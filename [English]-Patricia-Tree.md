The best known tutorial/explanation of the ethereum patricia tree is here:
https://easythereentropy.wordpress.com/2014/06/04/understanding-the-ethereum-trie/

Note that it is outdated in a few places; specifically, replace:

* `state = trie.Trie('triedb', trie.BLANK_ROOT)` with `state = trie.Trie(db.EphemDB(), trie.BLANK_ROOT)`
* `state = trie.Trie('triedb', '15da97c42b7ed2e1c0c8dab6a6d7e3d9dc0a75580bbc4f1f29c33996d1415dcc'.decode('hex'))` with `state = trie.Trie(prev_state.db, '15da97c42b7ed2e1c0c8dab6a6d7e3d9dc0a75580bbc4f1f29c33996d1415dcc'.decode('hex'))` where `prev_state` is the state object created and used in the first section.

(and make sure to run `import rlp; from ethereum import db, trie` beforehand).