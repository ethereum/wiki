
The changes should be backwards compatible for a while, and we throw a warning stating thats it deprecated. Simple map. The return values of the funcitons don't change for now. (community wish)

All returned numbers should be typed statically either to JS integers or BigNumbers, depending on their practical maximum value. This should be annotated whenever a number is returned.

* [web3](#web3)
  * [version](#) (community wish)
     * [network](#) -> string e.g. '0.0.1' (the node version)
     * [api](#) -> string e.g. '0.0.1' (the ethereum.js version)
     * [client](#) -> string e.g. 'AlethZero/1.0.0' (the client ID)
  * [port](#) -> number e.g. 8080 (community wish)
  * [sha3](#web3sha3) *(s1)*
  * [toAscii](#web3toascii) *(hexString)*, returns textString
  * [fromAscii](#web3fromascii) *(textString, [padding])*, returns hexString
  * [toDecimal](#web3todecimal) *(hexString)*, returns decimalString
  * [fromDecimal](#web3fromdecimal) *(decimalString)*, returns hexString
  * [setProvider](#web3setprovider) *(provider)*
  * [reset](#web3reset)
  * [eth](#web3eth)
    * [coinbase](#web3ethcoinbase) -> hexString
    * [listening](#web3ethlistening) -> boolean
    * [mining](#web3ethmining) -> boolean
    * [gasPrice](#web3ethgasprice) -> BigNumber
    * [accounts](#web3ethaccounts) -> array of hexStrings
    * [register(hexString)](#web3ethregister)
    * [unRegister(hexString)](#web3ethunregister) // PREVIOUS: unregister()
    * [peerCount](#web3ethpeercount) -> Integer
    * [defaultBlock](#web3ethdefaultblock) -> Integer
    * [blockNumber](#web3ethnumber) -> Integer // PREVIOUS: number()
    * [getBalance(address)](#web3ethbalanceat) -> BigNumber // PREVIOUS: balanceAt()
    * [getState(address, storage)](#web3ethstateat) -> hexString // PREVIOUS: stateAt()
    * [getStorage(address)](#web3ethstorageat) -> hexString // PREVIOUS: storageAt()
    * [getTransactionCount(address)](#web3ethcountat) -> Integer // PREVIOUS: countAt()
    * [getCode(address)](#web3ethcodeat) -> hexString// PREVIOUS: countAt()
    * [sendTransaction(object)](#web3ethtransact)  // PREVIOUS: transact() 
    * [call(object)](#web3ethcall) -> hexString
    * [getBlock(hash/number)](#web3ethblock) -> headerObject // PREVIOUS: block() 
    * [getTransaction(object, number)](#web3ethtransaction) -> transactionObject  // PREVIOUS: transaction() 
    * [getUncle(hash/number)](#web3ethuncle) -> headerObject // PREVIOUS: uncle()
    * [getCompilers()](#web3ethcompilers) -> array of strings // PREVIOUS: compilers()
    * [compile.lll(string)](#web3ethlll) -> hexString // PREVIOUS: lll()
    * [compile.solidity(string)](#web3ethsolidity) -> hexString // PREVIOUS: solidity()
    * [compile.serpent(string)](#web3ethserpent) -> hexString // PREVIOUS: serpent()
    * Replaced with filter(...).get() *(object/string)* // PREVIOUS: logs()
    * [filter(string)](#web3ethwatch) -> filterObject
    * [filter(object)](#web3ethlogs) -> filterObject // object is a log filter 
    * [filter(object (, options) )](#web3ethlogs) -> filterObject // object is a contract object
    * [filter(object (, indexed_event_arguments (, options)) )](#web3ethlogs) -> filterObject // object is an event object belonging to a specific contract object
    * [filter(array (, options) )](#web3ethlogs) -> filterObject // array is an array of events object belonging to specific contract objects
      * `options` is a object containing one or more of `earliest`, `latest`, `offset` and `max`, as before.
      * `indexed_event_arguments` is an object with keys of one or more indexed arguments to the event(s) and values of either one (directly) or more (in an array) e.g. {'a': 1, 'b': [myFirstAddress, mySecondAddress]}.
      * filterObject contains:
        - [watch(callback)](#) // PREVIOUS: changed() arrived()
        - [stopWatching(callback)](#) // PREVIOUS: uninstall()
        - [get()](#)  // returns []; existingItems? lastItems? PREVIOUS: logs()
    * [contract(abi)](#web3ethcontract) -> contractPrototypeObject // PREVIOUS: contractFromAbi
      * contains much, including a function taking an address to give you the contract instance.
    * DEPRECATED [contractInstance(abi, address)](#web3ethcontract) // PREVIOUS: contract, parameters exchanged.
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