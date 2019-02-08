* [FOUNDAMENTALS](../index.md)
* [**SPECIFICATIONS**](client-management.md)
  1. [Client Management](client-management.md)
  2. [Communication protocol](communication-protocol.md)
  3. [**Node management**](node-management.md)
* [DEVELOPMENT](../development/get-started.md)
# Node management
## States
* **Database**: the node is saved in a table containing:
  * **ID (int)**: the unique identifier of the node
  * **IPFS Hash (string)**: the IPFS Hash that points to the file containing the node's information
  * **Secret Token (string)**: a secret token used for authentication
  * **Information (json)**: the node's basic information
    * **Name (string)**: the name of the node
    * **Location (string)**: the location of the node
  * **Protocol (string)**: a string indicating if the node is based on websocket or http
* **Online Nodes**: a list containing all the online nodes, the fields are:
  * **WebSocket ID (string)**: the unique WebSocket Identifier, null if the node is permanent
  * **ID (int)**: the unique identifier of the node
  * **IPFS Hash (string)**:  the IPFS Hash that points to the file containing the node's information
## Events
### Registration
When a new node registers to the local instance, it sends the IPFS hash containing its information. If the local instance accepts the node, the node will be added into the local nodes list, the instance then reply with the node's secret token. This list is published on IPFS every time a new user is added.
#### Request (Node)
* **WebSocket**: When the request comes the websocket protocol the node is saved with **Protocol** equals `websocket`
  * **Event (string)**: "node.to.instance"
  * **Body (JSON)**:
    * **Action (string)**: "register"
    * **Parameters (JSON)**:
      * **hash (string)**: the new node's IPFS Hash
      * **information (JSON)**: the new node's basic information
* **Http**: When the request comes the http protocol the node is saved with **Protocol** equals `http`,
    * **Method**: "PUT"
    * **Route**: "/node/to/instance"
    * **Body (JSON)**:
      * **Action (string)**: "register"
      * **Parameters (JSON)**:
        * **hash (string)**: the new node's IPFS Hash
        * **information (JSON)**: the new node's basic information
#### Response (Local Instance)
* **WebSocket**
  * **Event (string)**: "instace.to.node"
  * **Body (JSON)**:
    * **Action (string)**: "registered"
    * **Parameters (JSON)**:
      * **Token (string)**: the secret token used for node's authentication
* **Http**:
  * **Body (JSON)**:
    * **Token (string)**: the secret token used for node's authentication

![image](https://user-images.githubusercontent.com/10226180/46701722-f66a9500-cc20-11e8-9404-db1dd841a647.png)
<!---
https://www.websequencediagrams.com/
Node->Local Instance: {action: "register", parameters:{ipfs_hash:"IPFS_HASH"}}
note right of Local Instance: The local instance accepts the node
note right of Local Instance: The local instance pins the node's IPFS Hash
Local Instance->Node: {action: "registered", parameters:{token:"SECRET_TOKEN"}}
note right of Local Instance: A new nodes list is published on IPFS
-->

### Login
In order to be inserted into the **Online Nodes List** a node must be authenticated by the local instance. The node sends the secret token to the local instance, the local instance authenticate it and replies with the hash and the pending requests queue.
#### Request (Node)
* **WebSocket**: When the request comes the websocket protocol the node is added to the **Online Nodes** list  with the `WebSocket ID` set to its websocket id
  * **Event (string)**: "node.to.instance"
  * **Body (JSON)**:
    * **Action (string)**: "login"
    * **Parameters (JSON)**:
      * **Token (string)**: the secret token received during registration

A node based on the http protocol doesn't login.
#### Response (Local Instance)
* **WebSocket**
  * **Event (string)**: "instace.to.node"
  * **Body (JSON)**:
    * **Action (string)**: "logged"
    * **Parameters (JSON)**:
      * **Hash (string)**: the node's IPFS hash
      * **Queue (Array(Objects))**: the node's pending requests list

![image](https://user-images.githubusercontent.com/10226180/46701254-66781b80-cc1f-11e8-84cb-47c6a01c64dc.png)
<!---
https://www.websequencediagrams.com/
Node->Local Instance: {action: "login", parameters:{token:"SECRET_TOKEN"}}
note right of Local Instance: The local instance authenticate the node
Local Instance->Node: {action: "logged", parameters:{ipfs_hash:"IPFS_HASH", queue:[]}}
note right of Local Instance: The node is added to the online nodes list
-->

### Update
A node can update its information sending the new information with the secret token.
#### Request (Node)
* **WebSocket**
  * **Event (string)**: "node.to.instance"
  * **Body (JSON)**:
    * **Action (string)**: "update"
    * **Parameters (JSON)**:
      * **token (string)**: the secret token received during registration
      * **hash (string)**: the new node's IPFS Hash
      * **information (JSON)**: the new node's basic information
* **Http**
    * **Method**: "POST"
    * **Route**: "/node/to/instance"
    * **Body (JSON)**:
      * **Action (string)**: "update"
      * **Parameters (JSON)**:
        * **token (string)**: the secret token received during registration
        * **hash (string)**: the new node's IPFS Hash
        * **information (JSON)**: the new node's basic information
#### Response (Local Instance)
* **WebSocket**
  * **Event (string)**: "instace.to.node"
  * **Body (JSON)**:
    * **Action (string)**: "updated"
    * **Parameters (JSON)**
* **Http**:
  * **Body (JSON)**

![image](https://user-images.githubusercontent.com/10226180/46701633-ac81af00-cc20-11e8-9b29-ecd26c2f01f8.png)
<!---
https://www.websequencediagrams.com/
Node->Local Instance: {action: "update", parameters:{ipfs_hash"IPFS_HASH", :token:"SECRET_TOKEN"}}
note right of Local Instance: The local instance authenticate the node
note right of Local Instance: The local instance update the node's information
Local Instance->Node: {action: "updated", parameters:{}}
note right of Local Instance: The local instance pins the new node's IPFS Hash
-->

# Next

Now you are ready to setup your environment, check [how to get started](../development/get-started.md)!

Please {% github_edit_link "help improve this page" %}.
