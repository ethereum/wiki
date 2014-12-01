In order to free ethereum implementation developers from the hassle of writing their own JavaScript implementation for browser-to-node communication there's now [ethereum.js](https://github.com/ethereum/ethereum.js).

Ethereum.js provides a full communication bridge between your node (backend) and browser (frontend) given that you **tell** it how _speak_. Communication is done through a `Provider`.

Ethereum.js exports one single container; `web3` which contains the `eth` object as well as a few helper methods (see [[JavaScript-API]] for more details).

### JSON RPC

The following RPC messages should be accepted by the RPC-backend:

* `eth_coinbase`
* `eth_setCoinbase`
* `eth_listening`
* `eth_setListening`
* `eth_mining`
* `eth_setMining`
* `eth_gasPrice`
* `eth_accounts`
  * request:
  ```bash
  curl -X POST --data '{"jsonrpc":"2.0","method":"eth_accounts","params":null,"id":1}' http://localhost:8080
  ```
  * response:
  ```json
  {"id":1,"jsonrpc":"2.0","result":["0x407d73d8a49eeb85d32cf465507dd71d507100c1"]}
  ```
* `eth_peerCount`
* `eth_defaultBlock`
* `eth_setDefaultBlock`
* `eth_number`
* `eth_balanceAt( address : String )`
  * request:
  ```bash
  curl -X POST --data '{"jsonrpc":"2.0","method":"eth_balanceAt","params":["0x407d73d8a49eeb85d32cf465507dd71d507100c1"],"id":1}' http://localhost:8080
  ```
  * response:
  ```json
  {"id":1,"jsonrpc":"2.0","result":"0x0234c8a3397aab580000"}
  ```
* `eth_stateAt`
* `eth_storageAt`
* `eth_countAt`
* `eth_codeAt`
* `eth_transact`
* `eth_call`
* `eth_blockByHash( hash : String )`
  * request:
  ```bash
  curl -X POST --data '{"jsonrpc":"2.0","method":"eth_blockByHash","params":["ee670ec64341771606e55d6b4ca35a1a6b75ee3d5145a99d05921026d1527331"],"id":1}' http://localhost:8080
  ```
  * response:
  ```json
  {"id":1,"jsonrpc":"2.0","result":{"difficulty":"0x0327c5","extraData":"0x0000000000000000000000000000000000000000000000000000000000000000","gasLimit":300018,"hash":"ee670ec64341771606e55d6b4ca35a1a6b75ee3d5145a99d05921026d1527331","minGasPrice":"0x09184e72a000","miner":"0x82022c34d173cf3b83c6e4553d627276fff6b66d","nonce":"0xc2adfa12f40d142eb585b0b7892cd0841cf30dd18cb88940a5323df767f0db2f","number":1231,"parentHash":"0xaa322b7bd7418b4328f0c7b350359a86d6b9b698ef584937447743b082eed099","sha3Uncles":"0x1dcc4de8dec75d7aab85b567b6ccd41ad312451b948a7413f0a142fd40d49347","stateRoot":"0x86df58f157e5ff5b77a6b1cddb43a2616acbaa7907087e0effb339e14c23a5db","timestamp":1416509555,"transactionsRoot":"0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421"}}
  ```

* `eth_blockByNumber( number : Integer )`
  * request:
  ```bash
  curl -X POST --data '{"jsonrpc":"2.0","method":"eth_blockByNumber","params":[1231],"id":1}' http://localhost:8080
  ```
  * response:
  ```json
  {"id":1,"jsonrpc":"2.0","result":{"difficulty":"0x0327c5","extraData":"0x0000000000000000000000000000000000000000000000000000000000000000","gasLimit":300018,"hash":"ee670ec64341771606e55d6b4ca35a1a6b75ee3d5145a99d05921026d1527331","minGasPrice":"0x09184e72a000","miner":"0x82022c34d173cf3b83c6e4553d627276fff6b66d","nonce":"0xc2adfa12f40d142eb585b0b7892cd0841cf30dd18cb88940a5323df767f0db2f","number":1231,"parentHash":"0xaa322b7bd7418b4328f0c7b350359a86d6b9b698ef584937447743b082eed099","sha3Uncles":"0x1dcc4de8dec75d7aab85b567b6ccd41ad312451b948a7413f0a142fd40d49347","stateRoot":"0x86df58f157e5ff5b77a6b1cddb43a2616acbaa7907087e0effb339e14c23a5db","timestamp":1416509555,"transactionsRoot":"0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421"}}
  ```
* `eth_transactionByHash( hash : String, nth : Integer )`
```bash
# TODO
```
* `eth_transactionByNumber( number : Integer, nth : Integer )`
```bash
# TODO
```
* `eth_uncleByHash( hash : String, nth : Integer )`
```bash
# TODO
```
* `eth_uncleByNumber( number : Integer, nth : Integer )`
```bash
# TODO
```
* `eth_compilers`
* `eth_lll`
* `eth_solidity`
* `eth_serpent`
* `eth_newFilter`
* `eth_newFilterString`
* `eth_uninstallFilter`
* `eth_changed`
* `eth_filterLogs`
* `eth_logs`
* `db_put`
* `db_get`
* `db_putString`
* `db_getString`
* `shh_post`
* `shh_newIdeninty`
* `shh_haveIdentity`
* `shh_newGroup`
* `shh_addToGroup`
* `shh_newFilter`
* `shh_uninstallFilter`
* `shh_changed`
* `transact( parameters : Object )`
###### old
* `newFilter( parameters_or_type : Variadic )` **Note**: Registers a new filter. Expects a return call with an unique ID specifying the new filter. In any subsequent queries that reference this filter, this same ID is used to identify the filter on the remote backend. It's up to the implementors to implement this look-up method as they see fit on the remote backend (see [[events](#events)]).
* `messages( id : Integer )`
* `uninstallFilter( id : Integer )`
* **optional** `changed( id : Integer )` Specificly for unidirectional implementations (e.g., TCP JSON RPC) which need a polling mechanism for filtered messages. `id` is the ID of the filter.

When a RPC call is done through the provided provider a JavaScript object will be send along with it. The arguments provided to the function will match the array in the `args` field.

```javascript
{
    id: <Integer>,
    method: <String>,
    params: [<Variadic1>, <Variadic2>, ...],
}
```

Example:

```javascript
getBlock( 10 )
// Creates the following JSON RPC
{
    id: 0,
    method: "getBlock",
    params: [10],
}

```

The id represents the internal id thats's being used to identify the current RPC call. The `id` plays a crucial role in creating response messages. When a response message to a call is made we use this same **id** in the return data. This is done so that ethereum.js knows exactly which return-callback to trigger (matching of ids with callbacks is all obfuscated in ethereum.js).

When a reponse is given on a particular call you'll have to supply it with a JavaScript object and serialise it to proper JSON:

```
{
    id: <Integer>,
    results: <Variadic>,
}
```

The `id` must match the `id` that you received from the initial call, the `data` is user supplied data and contains arbritrary data.

### Events

For the Messaging API specified in the JavaScript API we use a very simple event mechanism. As stated above, when the `newFilter` RPC is done it's expected that you return a unique identifier for the filter. This identifier is used in all subsequent calls or as identifier in finding the JavaScript equivalent `Filter` object.

When you want to trigger a filter's watches which has been created through `watch( ... )` you post a new message without `id`. Instead you pack it with an `event` field which contains the `messages` event as string. The `results` field is an array of _exactly_ two elements. The first being the filtered messages and the second being the filter id which was created through `newFilter` (`watch` creates an RPC with `newFilter`). Ethereum.js sorts out the rest.

```javascript
{
    event: "messages",
    results: [[Message1, Message2, ...], filter_id]
}
```

### Providers

A provider can be set to the `web3` stack using the `setProvider` method. A `Provider` is an interface that handles the communication between `ethereum.js` and the ethereum node. It's absolutely crucial that a provider exposes two methods:

* `send( payload )` implements the sending mechanism. Payload is in unserialised format. How the data is send (and serialised) is up to the `Provider`'s implementation.
* `onmessage` the `onmessage` is an attribute and will be set to a default handler. If the `Provider` requires a different receiving mechanism your `onmessage` implementation should take care of this (hint: `Object.defineAttribute`). The default handler, which gets set after called `setProvider`, takes care of deserialising the payload and the associated return-callbacks (or in case of an event the corresponding event callback).
* `poll( payload, id )` **optional.** Should exists, if manual polling for watches is required (eg. HttpRpcProvider) . Is called with payload and id of watch.

There are currently two default providers which you can use:

* `WebSocketProvider` Generic WebSocket `new WebSocketProvider( "ws://host/eth" )`
* `QtProvider` Generic Qt provider `new QtProvider()`
* `HttpRpcProvider` Generic HttpRpcProvider `new HttpRpcProvider("http://localhost:8080")`

These can be found in `web3.providers`.

### Example

An example implementation using Qt's `navigator.qt.postMessage` and `onmessage` and a Go backend

#### Ethereum.js backend

```javascript
// New web3 provider
var QtProvider = function() {};
QtProvider.prototype.send = function(payload) {
    navigator.qt.postData(JSON.stringify(payload));
}`
Object.defineProperty(QtProvider.prototype, "onmessage", {
    set: function(handler) {
        navigator.qt.onmessage = handler;
    },
});
// Set new provider
web3.setProvider(new QtProvider());

// Fetch a block
web3.eth.getBlock(1);
```

#### Node Backend (Go)

```go
import "encoding/json"

struct msg struct {
    Id   int             `json:"id"`
    Method string        `json:"method"`
    Params []interface{} `json:"params"`
}

// Called when JavaScript `send`s something
func onMessage(data string) {
    var mMsg msg
    json.Unmarshal([]byte(data), &mMsg)
    
    switch mMsg.Call {
        case "getBlock":
            // handle block and return data
            ret := handleBlock(mMsg.Args...)
            send(ret, mMsg.Id)
    }
}

func send(v interface{}, id int) {
    m := json.Marshal(map[string]interface{}{
        id: id,
        results: v,
    })
    
    // Some method exposed by the `myapi` package.
    myapi.PostData(string(m))
}
```

Questions? @jeff