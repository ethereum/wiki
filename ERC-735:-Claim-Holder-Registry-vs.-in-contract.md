This is part of the discussion for https://github.com/ethereum/EIPs/issues/735 ([Related comment](https://github.com/ethereum/EIPs/issues/735#issuecomment-337284218))
This is a collaborative list of PROs and CONs to gather advantages for both sides. 

### Central claim registry

#### PROs

- standardised, e.g. functionality known which prevents cheating
- central reference  point
- claim addition and removal can have complex processes, all standardised

#### CONs

- will mainly be useful for pure ethereum accounts or smart contracts. There is no advantage over in-contract claims when adding signatures to them
- Will be hard to change, or to improve over time, as the code once deployed is fixes, or needs a complicated upgrade mechanism

### In-Contract claims

#### PROs

- Claims are at the spot where people interact with the identity itself (this is what the third party receives as info anyway when interacting with this identity/person)
- Behaviour how claims are added can be customised (e.g. an identity would approve added claims, an ICO smart contract may just accept any without approval to show validity)
- Claims are verified through signatures, not the adding logic (as it can not be controlled)
- Claims can be removed/made invalid, if the issuer removes the key he signed with from its identity.
- The claim scheme can be more freely arranged
- as the focus is on signatures, those can be new types in the future, and interaction is not necessarily (Ethereum) blockchain specific.

#### CONs

- Claim addition can't be trusted (validation of the signature count is important)
- verifying claim signatures on chain can be costly.
- claims retrieval can be not follow standers and causes issues (leads to invalid/incompatible standard)
- Adds a lot of complexity/is more complicated to implement


Please feel free to add.
Be aware that one claim or key can easily reference a claim registry, so these standards could work perfectly along side each other.