* [**FOUNDAMENTALS**](../index.md)
  1. [Introduction](../index.md)
  2. [Architecture](architecture.md)
  3. [**How entities communicate**](communication.md)
  4. [Entities decentralized information](decentralization.md)
  5. [Components](components.md)
* [SPECIFICATIONS](../specifications/client-management.md)
* [DEVELOPMENT](../development/get-started.md)

# Communication

## How entities communicate

Inside the Nile network there are entities using different communication protocols, we created a wrapper so every communication protocol can be called in a standardized way.

## Entities
The entities have the following **types**:
* node
* client
* instance

The other important entity's properties for the communication are:
* the **id**: an unique identifier of the entity
* the **protocol**: the communication protocol of the entity (_e.g. http, ws_)
* the **resource**: the resource identifier in the protocol (_e.g. the domain in the http protocol_)

Learn more about each entity role in the "Architecture" section.

## Channels

Entities communicates with each other sending messages in specific channels. A channel has a specific syntax:

`sender_type.to.recipient_type` (_e.g. node.to.instance_)

Every message is encrypted before sending the message, so the mediator knows only the sender and the recipient, but not the message itself.

## Direct and Forwarded communication
Not all communication protocols allow direct messages between two entities so a mediator can be used to forward messages.

## Message specification
Every message should contain:
* a **recipient**:
  * In the case of direct communication, the recipient is the **resource** of the entity (_e.g. in http the recipient is the domain or the ip_)
  * In the case of forwarded communication, the recipient is an object with:
  &nbsp;&nbsp; - **recipient**: the recipient_id of the entity
  &nbsp;&nbsp; - **mediator**: the resource of the entity that forward the message
* an **action**: that is the action processed by the recipient. (_e.g. a node registers to an instance sending a message with  action=**register** to the **node.to.instance** channel, the action is processed by the instance_)
* **parameters** the parameters for the action, each action has different parameters.

## Protocol wrapper
A protocol wrapper is an object that creates a standard layer over the communication protocols (websockets, http...). Each protocol wrapper is in _src/protocols/_.
It defines the **to** and the **on** methods. Each method calls the respective protocol's function to send and receiving messages.

### The "on" method.
Each time a message arrives in a channel, a function can be executed. In order to execute the function it must first be binded to the channel using the **on** method.

The **on** method takes as parameters (in the following order):
* **channel**: the channel in which the message arrives
* **action**: the action to which you want to respond
* **resource**: the entity's resource object that is sending the message
* **callback**: the function called once the message is received. The callback's parameters are:
  * **protocol**: the protocol id (websocket, http)
  * **sender_id**: the enity's identifier
  * **parameters**: the parameters of the incoming message
  * **reply(parameters)**: a function used to reply to the incoming message. You can pass the response's parameters as an object to the reply function. The response's channel and action are specified in the next "on" parameter: "response".
* **[response]**: an optional object that defines where to publish a response to the message. It has two properties:
  * **channel**: the channel where to response is published
  * **action**: the response's action

In order to listen for a channel there must be a direct or forwarded communication to every entity in that channel. Each time a new connection with an entity is established, the listener has to bind functions to every channel involved in the communication.

_E.g.
A node creates a new connection with an instance. The instance binds all the functions involved in nodes management to the node.to.instance channel. The node, from the other side, binds all the functions involved in its management to the instance.to.node channel._

The on bindings has to be specified in the **loadListeners** method. The **loadListeners** method is called every time a new connection occours.

In the following examples there is an explicit websocket connection between an instance and a node. The following code rappresents an instance that is binding the registerNode function to every message with "register" as action sent in the "node.to.instance" channel:

```javascript
const WebSockets = require('./protocols/WebSockets')
const wsServer = require('socket.io')
const ws = new WebSockets(wsServer)

const registerNode = (protocol, sender_id, parameters, reply) => {
  // Using the reply function a message {action: "registerConfirm", parameters: {success: true}, recipient: the_sender}
  // is published in the 'instance.to.node' channel. The channel and the action are specified as the lat parameter of
  // the "on" method, the parameters are specified as reply's parameter
  reply({success: true})
}

wsServer.on('connection', (resource) => { // Each time a new node creates a websocket connection with the instance
  // The resource parameter is the entity's resource object used in the websocket's wrapper
  ws.on('node.to.instance', 'register', resource, registerNode, {
    channel: 'instance.to.node',
    action: 'registerConfirm'
  })
})
```

### The "to" method
The **to** method is used to send a message to one or all entities that are listening to a channel. It has the following parameters:
* **recipient**: the resource of the recipient
* **channel**: the channel in which to publish the message
* **action**: the action of the message
* **parameters**: the parameters of the message
* **[response]**: an optional object that defines how to listen to the response for this message
  * **channel**: the channel of the response
  * **action**: the action of the response
  * **callback**: the function to be executed once the response is received. The callback has the same parameters as the callback parameter in the "on" method, except for the reply function.

```javascript
const httpNode = require('http').createServer(handleRequest)
httpNode.listen(8888)
let http = new HttpProtocol(httpNode)

http.to('https://nodedomain.com/', 'instance.to.node', 'registered', { success: true }, {
  channel: 'node.to.instance',
  action: 'registeredConfirmed',
  callback: (protocol, sender, parameters) => {
    //
  }
})
```

## Non-standard communication
External ecommerces can join Nile. If an ecommerce has existing APIs then the communication occours following the specifications of the ecommerce's APIs.

# Next
Learn how entities save information in the decentralized storage [here](decentralization.md).

Please {% github_edit_link "help improve this page" %}.
