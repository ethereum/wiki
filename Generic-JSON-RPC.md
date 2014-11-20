In order to free ethereum implementation developers from the hassle of writing their own JavaScript implementation for browser-to-node communication there's now [ethereum.js](https://github.com/ethereum/ethereum.js).

Ethereum.js provides a full communication bridge between your node (backend) and browser (frontend) given that you **tell** it how _speak_. Communication is done through a `Provider`.

Ethereum.js exports one single container; `web3` which contains the `eth` object as well as a few helper methods (see [[JavaScript-API]] for more details).

### JSON RPC

The following RPC messages should be accepted by the RPC-backend:

* `blockByHash( hash : String )`
* `blockByNumber( number : Integer )`
* `transactionByHash( hash : String, nth : Integer )`
* `transactionByNumber( number : Integer, nth : Integer )`
* `uncleByHash( hash : String, nth : Integer )`
* `uncleByNumber( number : Integer, nth : Integer )`
* `transact( parameters : Object )`
* **optional** `compile( code : String )`
* `balanceAt( address : String )`
* `countAt( address : String )`
* `codeAt( address : String )`
* `stateAt( address : String, state_address : String )`
* `call( parameters : Object )`
* `coinbase`
* `listening`
* `mining`
* `peerCount`
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