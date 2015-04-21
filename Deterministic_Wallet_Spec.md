Address/key generation

The following is a proposed deterministic wallet specification.

Let `S` be the secret. We define `key(i) = sha3(S + int_to_big_endian(i))`, where `i` is an incrementing index starting at 0

Assuming `S = "123dog"`, we thus have:

    key(0) = sha3("123dog")

Note that the big endian representation of 0 is the empty string.

    key(1) = sha3("123dog\x01")
    key(2) = sha3("123dog\x02")
    key(255) = sha3("123dog\xff")
    key(256) = sha3("123dog\x01\x00")
    key(257) = sha3("123dog\x01\x01")
    key(4294967295) = sha3("123dog\xff\xff\xff\xff")
    key(4294967296) = sha3("123dog\x01\x00\x00\x00\x00")
    key(4294967297) = sha3("123dog\x01\x00\x00\x00\x01")