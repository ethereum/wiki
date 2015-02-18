
* [web3](#web3)
  * [sha3](#web3sha3) *(s1)*
  * [toAscii](#web3toascii) *(hexString)*
  * [fromAscii](#web3fromascii) *(string, [padding])*
  * [toDecimal](#web3todecimal) *(hexString)*
  * [fromDecimal](#web3fromdecimal) *(number)*
  * [setProvider](#web3setprovider) *(providor)*
  * [reset](#web3reset)
  * [eth](#web3eth)
    * [coinbase](#web3ethcoinbase) -> number (BigNumber?)
    * [listening](#web3ethlistening) -> boolean
    * [mining](#web3ethmining) -> boolean
    * [gasPrice](#web3ethgasprice) -> number
    * [accounts](#web3ethaccounts) -> array
    * [register](#web3ethregister) *(hexString)*
    * [unregister](#web3ethunregister) *(hexString)*
    * [peerCount](#web3ethpeercount) -> number
    * [defaultBlock](#web3ethdefaultblock) -> ?
    * [blockNumber](#web3ethnumber) -> number // PREVIOUS: number()
    * [getBalance](#web3ethbalanceat) *(address)* -> number (BigNumber?) // PREVIOUS: balanceAt()
    * [getState](#web3ethstateat) *(address, storage)* // PREVIOUS: stateAt()
    * [getStorage](#web3ethstorageat) *(address)* // PREVIOUS: storageAt()
    * [getTransactionCount](#web3ethcountat) *(address)* // PREVIOUS: countAt()
    * [getCode](#web3ethcodeat) *(address)* // PREVIOUS: countAt()
    * [send or still transact?](#web3ethtransact) *(object)*  // PREVIOUS: transact() 
    * [call](#web3ethcall) *(object)*
    * [getBlock](#web3ethblock) *(hash/number)*  // PREVIOUS: block() 
    * [getTransaction](#web3ethtransaction) *(_object, _number)*  // PREVIOUS: transaction() 
    * [uncle](#web3ethuncle) *(hash/number)*  // PREVIOUS: uncle()
    * [getCompilers](#web3ethcompilers) *()*  // PREVIOUS: compilers()
    * [compiler.lll](#web3ethlll) *(string)*  // PREVIOUS: lll()
    * [compiler.solidity](#web3ethsolidity) *(object)*  // PREVIOUS: lll()
    * [compiler.serpent](#web3ethserpent) *(object)*  // PREVIOUS: lll()
    * Replace with filter.previous(): [filter](#web3ethlogs) *(object/string)* // PREVIOUS: logs()
    * [filter](#web3ethwatch) *(object/string)*
      * [arrived](#) *(callback)* ??
      * [watch](#) *(callback)* // PREVIOUS: changed()
      * [stopWatching](#) *(callback)* // PREVIOUS: uninstall()
      * [previous](#) *(callback)*  // PREVIOUS: logs()
    * [contract](#web3ethcontract) *(address, abi)*
    * [flush](#web3ethflush)
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
    * [watch](#web3shhwatch) *(_object/_string)* // TODO: SAME as above
      * [arrived](#) *(_callback)*
      * [changed](#) *(_callback)*
      * [messages](#) *(_callback)*
      * [uninstall](#) *(_callback)*