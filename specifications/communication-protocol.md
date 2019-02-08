* [FOUNDAMENTALS](../index.md)
* [**SPECIFICATIONS**](client-management.md)
  1. [Client Management](client-management.md)
  2. [**Communication protocol**](communication-protocol.md)
  3. [Node management](node-management.md)
* [DEVELOPMENT](../development/get-started.md)
# Communication protocol
Nile offers two communcation protocols:
* websockets
* http

## Message elements
* **Channel**: specifies who is communicating with  whom
  * **websockets**: it is rappresented by the name of the event
    * **syntax**: "sender.to.recipient"
    ```javascript
    ws.emit("node.to.client", {}})
    ```
  * **http**: it is rappresented by the path
    * **syntax**: "sender.to.recipient"
    ```http
    http://localinstance/node/to/client
    ```
* **Properties (JSON)**: The properties of the channel
  * **instance.to.node**, **instance/to/node**:
    * **action**: the called action
    * **parameters**: the properties of the action
  * **instance.to.client**:
    * **action**: the called action
    * **parameters**: the properties of the action
  * **instance.to.client.from.node**
    * **response**: the response
    * **sender**: the node id
  * **instance.to.node.from.client**
    * **response**: the response
    * **sender**: the node id
  * **node.to.instance**, **/node/to/instance**:
    * **action**: the called action
    * **parameters**: the properties of the action
  * **node.to.instance.for.client**
    * **response**: the response
    * **recipient**: the client id
  * **client.to.instance**
    * **action**: the called action
    * **parameters**: the properties of the action
  * **client.to.instance.for.node**
    * **response**: the response
    * **sender**: the node id

# Next

On the next page you can read more about the [node management](../specifications/node-management.md).


Please {% github_edit_link "help improve this page" %}.
