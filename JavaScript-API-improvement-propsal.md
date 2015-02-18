
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
    * [defaultBlock](#web3ethdefaultblock) function
    * [number](#web3ethnumber)
    * [balanceAt](#web3ethbalanceat) *(_address)*
    * [stateAt](#web3ethstateat) *(_address, _storage)*
    * [storageAt](#web3ethstorageat) *(_address)*
    * [countAt](#web3ethcountat) *(_address)*
    * [codeAt](#web3ethcodeat) *(_address)*
    * [transact](#web3ethtransact) *(_object)*
    * [call](#web3ethcall) *(_object)*
    * [block](#web3ethblock) *(_hash/_number)*
    * [transaction](#web3ethtransaction) *(_object, _number)*
    * [uncle](#web3ethuncle) *(_hash/number)*
    * [compilers](#web3ethcompilers) *()*
    * [lll](#web3ethlll) *(_string)*
    * [solidity](#web3ethsolidity) *(_object)*
    * [serpent](#web3ethserpent) *(_object)*
    * [logs](#web3ethlogs) *(_object/_string)*
    * [watch](#web3ethwatch) *(_object/_string)*
      * [arrived](#) *(_callback)*
      * [changed](#) *(_callback)*
      * [logs](#) *(_callback)*
      * [uninstall](#) *(_callback)*
    * [contract](#web3ethcontract) *(_address, _abi)*
    * [flush](#web3ethflush)
  * [db](#web3db)
    * [put](#web3dbput) *(_name, _key, _value)*
    * [putString](#web3dbputstring) *(__name, _key, _value)*
    * [get](#web3dbget) *(_name, _key)*
    * [getString](#web3dbgetstring) *(_name, _key)*
  * [shh](#web3shh)
    * [post](#web3shhpost) *(_object)*
    * [newIdentity](#web3shhnewidentity) *()*
    * [haveIdentity](#web3shhhaveidentity) *(_string)*
    * [newGroup](#web3shhnewgroup) *(_id, _who)*
    * [addToGroup](#web3shhaddtogroup) *(_group, _who)*
    * [watch](#web3shhwatch) *(_object/_string)*
      * [arrived](#) *(_callback)*
      * [changed](#) *(_callback)*
      * [messages](#) *(_callback)*
      * [uninstall](#) *(_callback)*