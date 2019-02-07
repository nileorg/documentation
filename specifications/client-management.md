* [FOUNDAMENTALS](../index.md)
* [**SPECIFICATIONS**](client-management.md)
  1. [**Client Management**](client-management.md)
  2. [Communication protocol](communication-protocol.md)
  3. [Node management](node-management.md)
* [DEVELOPMENT](../development/get-started.md)

# Client Management
## States
* **Database**: the client is saved in a table containing:
  * **ID (int)**: the unique identifier of the client
  * **Secret Token (string)**: a secret token used for authentication
  * **Information (json)**: the client's basic information
    * **Name (string)**: the name of the client
    * **Location (string)**: the location of the client
* **Online clients**: a list containing all the online clients, the fields are:
  * **WebSocket ID (string)**: the unique WebSocket Identifier
  * **ID (int)**: the unique identifier of the clientclient's information
## Events
### Registration
When a new client registers to the local instance, the client is added in the database, the instance then reply with the client's secret token.
#### Request (client)
* **WebSocket**:
  * **Event (string)**: "client.to.instance"
  * **Body (JSON)**:
    * **Action (string)**: "register"
    * **Parameters (JSON)**:
      * **information (JSON)**: the new client's basic information
#### Response (Local Instance)
* **WebSocket**
  * **Event (string)**: "instace.to.client"
  * **Body (JSON)**:
    * **Action (string)**: "registered"
    * **Parameters (JSON)**:
      * **Token (string)**: the secret token used for client's authentication

![image](https://user-images.githubusercontent.com/10226180/47583779-b6155180-d958-11e8-85a7-5b4fa7d97828.png)
<!---
https://www.websequencediagrams.com/
client->Local Instance: {action: "register", parameters:{information:INFORMATION}}
Local Instance->client: {action: "registered", parameters:{token:"SECRET_TOKEN"}}
-->

### Login
In order to be inserted into the **Online clients List** a client must be authenticated by the local instance. The client sends the secret token to the local instance, the local instance authenticate it and replies with the pending responses.
#### Request (client)
* **WebSocket**: When the request comes the websocket protocol the client is added to the **Online clients**
  * **Event (string)**: "client.to.instance"
  * **Body (JSON)**:
    * **Action (string)**: "login"
    * **Parameters (JSON)**:
      * **Token (string)**: the secret token received during registration

#### Response (Local Instance)
* **WebSocket**
  * **Event (string)**: "instace.to.client"
  * **Body (JSON)**:
    * **Action (string)**: "logged"
    * **Parameters (JSON)**:
      * **Queue (Array(Objects))**: the client's pending responses

![image](https://user-images.githubusercontent.com/10226180/47583802-caf1e500-d958-11e8-8bde-28b7219e9df8.png)
<!---
https://www.websequencediagrams.com/
client->Local Instance: {action: "login", parameters:{token:"SECRET_TOKEN"}}
note right of Local Instance: The local instance authenticate the client
Local Instance->client: {action: "logged", parameters:{queue:[]}}
note right of Local Instance: The client is added to the online clients list
-->

### Update
A client can update its information sending the new information with the secret token.
#### Request (client)
* **WebSocket**
  * **Event (string)**: "client.to.instance"
  * **Body (JSON)**:
    * **Action (string)**: "update"
    * **Parameters (JSON)**:
      * **token (string)**: the secret token received during registration
      * **information (JSON)**: the new client's basic information
#### Response (Local Instance)
* **WebSocket**
  * **Event (string)**: "instace.to.client"
  * **Body (JSON)**:
    * **Action (string)**: "updated"
    * **Parameters (JSON)**

![image](https://user-images.githubusercontent.com/10226180/47583828-e230d280-d958-11e8-90c3-911ff9c37381.png)
<!---
https://www.websequencediagrams.com/
client->Local Instance: {action: "update", parameters:{token:"SECRET_TOKEN", information: INFORMATION}}
note right of Local Instance: The local instance authenticate the client
note right of Local Instance: The local instance update the client's information
Local Instance->client: {action: "updated", parameters:{}}
-->

Please {% github_edit_link "help improve this page" %}.
