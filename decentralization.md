1. [Introduction](index.md)
2. [Architecture](architecture.md)
3. [How entities communicate](communication.md)
4. [**Entities decentralized information**](decentralization.md)
5. [Components](components.md)


# Decentralization

## Entities decentralized information

Entities information are store using decentralized protocols. Like the communication, also the storage is protocol agnostic. Right now we only support IPFS, but the system is built to handle different decentralized databases / filesystems.

# A tree of hashes!
Every node (store) publishes its information in a decentralized storage getting back a unique identifier for that information.
```bash
$ ipfs add node_information.json
> store_information.json added, the unique identifier is QmZtmD2qt6fJot32nabSP3CUjicnypEBz7bHVDhPQt9aAy
```
The unique identifier is then used to register the node in an instance. Once the instance receives the node's unique identifier it saves it in a list containing all the nodes and their corrisponding unique identifier
```json
{
  "node1": "QmZtmD2qt6fJot32nabSP3CUjicnypEBz7bHVDhPQt9aAy",
  "node2": "Ah88HnGyguyGG670U444EBuhhfGY690Ngff54fGhBv864V",
  "node3": "neuIUH75FhghguygUG8780HBkpoij53ggGYIV7876fF6th"
}
```
This list is then, again, published in a decentralized storage getting back a another unique identifier that points to this list.
```bash
$ ipfs add instance_nodeslist.json
> instance_nodeslist.json added, the unique identifier is jhGYG7hc78y234y2n42HYGD89n9nyHgg67BBFF7687gG63
```
Finally we, as **Nile foundation**, will create a repository of all the _certified_ instances. So, again, each instance will be listed in a file and the published in a decentralized storage.

So, to recap. Lets start from the end, the repository of instances:
 ```json
{
  "instance1": "jhGYG7hc78y234y2n42HYGD89n9nyHgg67BBFF7687gG63",
  "instance2": "mtwirsqawjuoloq2gvtyug2tc3jbf5htm2zeo4rsknfiv3",
  "instance3": "7hc78y234y2oloqn42ioH3jbf5htm2zeo4rsknfivdp46a"
}
```
If we ask to decentralized storage to get the file related to "instance1" we should receive:
```json
{
  "node1": "QmZtmD2qt6fJot32nabSP3CUjicnypEBz7bHVDhPQt9aAy",
  "node2": "Ah88HnGyguyGG670U444EBuhhfGY690Ngff54fGhBv864V",
  "node3": "neuIUH75FhghguygUG8780HBkpoij53ggGYIV7876fF6th"
}
```
Finally, following the unique identifier of "node1" we should get the node_information.json that we published at the beginning!

# Entities specific information
## Node
The node saves its components in a decentralized storage. We will cover what components are and how they works in the next section.

## Instance
The only information published by the instance on a decentralized storage is the list of its nodes. Every time a new node changes or joins the instance, a new list is published.

## Client
The client doesn't save anything in a decentralized way.

# Next
In the next section we will cover how components work. [Go ahead](components.md)!
