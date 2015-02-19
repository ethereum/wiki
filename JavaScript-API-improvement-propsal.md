
The changes should be backwards compatible for a while, and we throw a warning stating thats it deprecated. Simple map. The return values of the funcitons don't change for now. (community wish)

* [web3](#web3)
  * [version](#) (community wish)
     * [network](#) -> string e.g. '0.0.1' (the node version)
     * [client](#) -> string e.g. '0.0.1' (the ethereum.js version)
  * [port](#) -> number e.g. 8080 (community wish)
  * [sha3](#web3sha3) *(s1)*
  * [toAscii](#web3toascii) *(hexString)*
  * [fromAscii](#web3fromascii) *(string, [padding])*
  * [toDecimal](#web3todecimal) *(hexString)*
  * [fromDecimal](#web3fromdecimal) *(number)*
  * [setProvider](#web3setprovider) *(providor)*
  * [reset](#web3reset)
  * [eth](#web3eth)
    * [coinbase](#web3ethcoinbase) -> hexString
    * [listening](#web3ethlistening) -> boolean
    * [mining](#web3ethmining) -> boolean
    * [gasPrice](#web3ethgasprice) -> number
    * [accounts](#web3ethaccounts) -> array
    * [register(hexString)](#web3ethregister)
    * [unRegister(hexString)](#web3ethunregister) // PREVIOUS: unregister()
    * [peerCount](#web3ethpeercount) -> number
    * [defaultBlock](#web3ethdefaultblock) -> ?
    * [blockNumber](#web3ethnumber) -> number // PREVIOUS: number()
    * [getBalance(address)](#web3ethbalanceat) -> number (BigNumber?) // PREVIOUS: balanceAt()
    * [getState(address, storage)](#web3ethstateat) // PREVIOUS: stateAt()
    * [getStorage(address)](#web3ethstorageat) // PREVIOUS: storageAt()
    * [getTransactionCount(address)](#web3ethcountat) // PREVIOUS: countAt()
    * [getCode(address)](#web3ethcodeat) // PREVIOUS: countAt()
    * [sendTransaction(object)](#web3ethtransact)  // PREVIOUS: transact() 
    * [call(object)](#web3ethcall)
    * [getBlock(hash/number)](#web3ethblock)  // PREVIOUS: block() 
    * [getTransaction(object, number)](#web3ethtransaction)  // PREVIOUS: transaction() 
    * [uncle(hash/number)](#web3ethuncle)  // PREVIOUS: uncle()
    * [getCompilers()](#web3ethcompilers)  // PREVIOUS: compilers()
    * [compile.lll(string)](#web3ethlll)  // PREVIOUS: lll()
    * [compile.solidity(object)](#web3ethsolidity)  // PREVIOUS: solidity()
    * [compile.serpent(object)](#web3ethserpent)  // PREVIOUS: serpent()
    * [Replaced with filter.previous()](#web3ethlogs) *(object/string)* // PREVIOUS: logs()
    * [filter(object/string)](#web3ethwatch)
      * [arrived(callback)](#) ??
      * [watch(callback)](#) // PREVIOUS: changed()
      * [stopWatching(callback)](#) // PREVIOUS: uninstall()
      * [pastItems(callback)](#)  // existingItems? lastItems? PREVIOUS: logs()
    * [contract(address, abi)](#web3ethcontract)
    * [flush()](#web3ethflush)
  * [db](#web3db)
    * [put](#web3dbput) *(_name, _key, _value)*
    * [putString](#web3dbputstring) *(__name, _key, _value)*
    * [get](#web3dbget) *(_name, _key)*
    * [getString](#web3dbgetstring) *(_name, _key)*
  * [shh](#web3shh)
    * [post](#web3shhpost) *(_object)*
    * [newIdentity](#web3shhnewidentity) *()*
    * [hasIdentity](#web3shhhaveidentity) *(_string)*  // PREVIOUS: haveIdentity()
    * [newGroup](#web3shhnewgroup) *(_id, _who)*
    * [addToGroup](#web3shhaddtogroup) *(_group, _who)*
    * [filter(object/string)](#web3shhwatch)
      * [arrived(callback)](#) ??
      * [watch(callback)](#) // PREVIOUS: changed()
      * [stopWatching(callback)](#) // PREVIOUS: uninstall()
      * [pastItems(callback)](#)  // existingItems? lastItems? PREVIOUS: logs()