---
name: Registrar ABI
category: 
---

### Registrar

Read-only interface that all registrars must implement

#### Javascript
```
var Registrar = web3.eth.contract([{"constant":true,"inputs":[{"name":"_owner","type":"address"}],"name":"name","outputs":[{"name":"o_name","type":"bytes32"}],"type":"function"},{"constant":true,"inputs":[{"name":"_name","type":"bytes32"}],"name":"owner","outputs":[{"name":"o_owner","type":"address"}],"type":"function"},{"constant":true,"inputs":[{"name":"_name","type":"bytes32"}],"name":"content","outputs":[{"name":"o_content","type":"bytes32"}],"type":"function"},{"constant":true,"inputs":[{"name":"_name","type":"bytes32"}],"name":"addr","outputs":[{"name":"o_address","type":"address"}],"type":"function"},{"constant":true,"inputs":[{"name":"_name","type":"bytes32"}],"name":"subRegistrar","outputs":[{"name":"o_subRegistrar","type":"address"}],"type":"function"},{"anonymous":false,"inputs":[{"indexed":true,"name":"name","type":"bytes32"}],"name":"Changed","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"name","type":"bytes32"},{"indexed":true,"name":"addr","type":"address"}],"name":"PrimaryChanged","type":"event"}]);
```

#### Solidity
```
contract Registrar{function name(address _owner)constant returns(bytes32 o_name){}function owner(bytes32 _name)constant returns(address o_owner){}function content(bytes32 _name)constant returns(bytes32 o_content){}function addr(bytes32 _name)constant returns(address o_address){}function subRegistrar(bytes32 _name)constant returns(address o_subRegistrar){}}
```

### GlobalRegistrar

`GlobalRegistrar` is the main name registration object. The global registrar object on the PoC-9 testnet is at address `0xc6d9d2cd449a754c494264e1809c50e34d64562b`.

#### Javascript
```
var GlobalRegistrar = web3.eth.contract([{"constant":true,"inputs":[{"name":"_owner","type":"address"}],"name":"name","outputs":[{"name":"o_name","type":"bytes32"}],"type":"function"},{"constant":true,"inputs":[{"name":"_name","type":"bytes32"}],"name":"owner","outputs":[{"name":"","type":"address"}],"type":"function"},{"constant":true,"inputs":[{"name":"_name","type":"bytes32"}],"name":"content","outputs":[{"name":"","type":"bytes32"}],"type":"function"},{"constant":true,"inputs":[{"name":"_name","type":"bytes32"}],"name":"addr","outputs":[{"name":"","type":"address"}],"type":"function"},{"constant":false,"inputs":[{"name":"_name","type":"bytes32"}],"name":"reserve","outputs":[],"type":"function"},{"constant":true,"inputs":[{"name":"_name","type":"bytes32"}],"name":"subRegistrar","outputs":[{"name":"o_subRegistrar","type":"address"}],"type":"function"},{"constant":false,"inputs":[{"name":"_name","type":"bytes32"},{"name":"_newOwner","type":"address"}],"name":"transfer","outputs":[],"type":"function"},{"constant":false,"inputs":[{"name":"_name","type":"bytes32"},{"name":"_registrar","type":"address"}],"name":"setSubRegistrar","outputs":[],"type":"function"},{"constant":false,"inputs":[],"name":"Registrar","outputs":[],"type":"function"},{"constant":false,"inputs":[{"name":"_name","type":"bytes32"},{"name":"_a","type":"address"},{"name":"_primary","type":"bool"}],"name":"setAddress","outputs":[],"type":"function"},{"constant":false,"inputs":[{"name":"_name","type":"bytes32"},{"name":"_content","type":"bytes32"}],"name":"setContent","outputs":[],"type":"function"},{"constant":false,"inputs":[{"name":"_name","type":"bytes32"}],"name":"disown","outputs":[],"type":"function"},{"constant":true,"inputs":[{"name":"_name","type":"bytes32"}],"name":"register","outputs":[{"name":"","type":"address"}],"type":"function"},{"anonymous":false,"inputs":[{"indexed":true,"name":"name","type":"bytes32"}],"name":"Changed","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"name","type":"bytes32"},{"indexed":true,"name":"addr","type":"address"}],"name":"PrimaryChanged","type":"event"}]);
```

#### Solidity

```
contract GlobalRegistrar{function name(address _owner)constant returns(bytes32 o_name){}function owner(bytes32 _name)constant returns(address ){}function content(bytes32 _name)constant returns(bytes32 ){}function addr(bytes32 _name)constant returns(address ){}function reserve(bytes32 _name){}function subRegistrar(bytes32 _name)constant returns(address o_subRegistrar){}function transfer(bytes32 _name,address _newOwner){}function setSubRegistrar(bytes32 _name,address _registrar){}function Registrar(){}function setAddress(bytes32 _name,address _a,bool _primary){}function setContent(bytes32 _name,bytes32 _content){}function disown(bytes32 _name){}function register(bytes32 _name)constant returns(address ){}}
```

### OwnedRegistrar

OwnedRegistrar fill the same interface as Registrar but allows a single owner complete authority over its contents.

#### Javascript

```
var OwnedRegistrar = web3.eth.contract([{"constant":true,"inputs":[{"name":"_owner","type":"address"}],"name":"name","outputs":[{"name":"o_name","type":"bytes32"}],"type":"function"},{"constant":true,"inputs":[{"name":"_name","type":"bytes32"}],"name":"owner","outputs":[{"name":"o_owner","type":"address"}],"type":"function"},{"constant":true,"inputs":[{"name":"_name","type":"bytes32"}],"name":"getAddress","outputs":[{"name":"o_owner","type":"address"}],"type":"function"},{"constant":true,"inputs":[{"name":"_name","type":"bytes32"}],"name":"content","outputs":[{"name":"","type":"bytes32"}],"type":"function"},{"constant":true,"inputs":[{"name":"_name","type":"bytes32"}],"name":"addr","outputs":[{"name":"","type":"address"}],"type":"function"},{"constant":true,"inputs":[{"name":"_name","type":"bytes32"}],"name":"subRegistrar","outputs":[{"name":"","type":"address"}],"type":"function"},{"constant":true,"inputs":[{"name":"_owner","type":"address"}],"name":"getName","outputs":[{"name":"o_name","type":"bytes32"}],"type":"function"},{"constant":false,"inputs":[{"name":"_name","type":"bytes32"},{"name":"_registrar","type":"address"}],"name":"setSubRegistrar","outputs":[],"type":"function"},{"constant":true,"inputs":[{"name":"_name","type":"bytes32"}],"name":"record","outputs":[{"name":"o_primary","type":"address"},{"name":"o_subRegistrar","type":"address"},{"name":"o_content","type":"bytes32"}],"type":"function"},{"constant":false,"inputs":[{"name":"_name","type":"bytes32"},{"name":"_a","type":"address"},{"name":"_primary","type":"bool"}],"name":"setAddress","outputs":[],"type":"function"},{"constant":false,"inputs":[{"name":"_name","type":"bytes32"},{"name":"_content","type":"bytes32"}],"name":"setContent","outputs":[],"type":"function"},{"constant":false,"inputs":[{"name":"_name","type":"bytes32"}],"name":"disown","outputs":[],"type":"function"},{"anonymous":false,"inputs":[{"indexed":true,"name":"name","type":"bytes32"}],"name":"Changed","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"name","type":"bytes32"},{"indexed":true,"name":"addr","type":"address"}],"name":"PrimaryChanged","type":"event"}]);
```

#### Solidity
```
contract OwnedRegistrar{function name(address _owner)constant returns(bytes32 o_name){}function owner(bytes32 _name)constant returns(address o_owner){}function getAddress(bytes32 _name)constant returns(address o_owner){}function content(bytes32 _name)constant returns(bytes32 ){}function addr(bytes32 _name)constant returns(address ){}function subRegistrar(bytes32 _name)constant returns(address ){}function getName(address _owner)constant returns(bytes32 o_name){}function setSubRegistrar(bytes32 _name,address _registrar){}function record(bytes32 _name)constant returns(address o_primary,address o_subRegistrar,bytes32 o_content){}function setAddress(bytes32 _name,address _a,bool _primary){}function setContent(bytes32 _name,bytes32 _content){}function disown(bytes32 _name){}}
```