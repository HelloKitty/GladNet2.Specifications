# GladNet2 Routing Specification

GladNet2 contains internal message structures that implement INetworkMessage and IRoutableMessage. These contracts/interfaces are implemented on the NetworkMessage structure that serves as the unit of transfer between connected GladNet peers.

NetworkMessages define two wire-ready data members: the NetSendable{PacketPayload} and an internally managed Stack{int}.

### AUID, Stack{int} and RoutingStack

This internal Stack{int} represents all the available information a **NetworkMessage** has and needs to transmit for routing information. Using **AUID (Application Unique Identifiers)** this stack preserves the history of routing accessible with LIFO semantics. Using AUIDs a NetworkMessage can retain source information for a given server.

The below example routing stack contains 3 AUIDs and can be treated as a history of the NetworkMessage:

{  1  } <---- Represents the AUID of the current context's **INetPeer**.

   
{  5  } <---- Represents the AUID of the previous peer's **INetPeer** this NetworkMessage orginates.

   
{  3  } <---- Represents the AUID of the first peer's **INetPeer** that recieved this NetworkMessage.

Note above that AUID 3 is the source AUID but generally represents a session of a remote-peer so reaching the peer with that AUID is generally not enough to finish routing. You must finish the route by sending the response/event/request through the Peer with that AUID.

### AUID Pushing and Popping

GladNet2 specifications for AUID pushing and popping are as such:

* End-users should not attempt to manually push or pop AUIDs as resulting routing behaviour may become undefined

* Incoming NetworkMessages on a given **Peer** will internally push their own AUID defined in their **IConnectionDetails**

* A GladNet2 routing service provides the following functionality
  * Can forward NetworkMessages fully intact with routing stack preserved
  * Can route back NetworkMessages by popping the routing stack and routing through the current application
    * Throws on empty routing stack
  * Can build NetworkMessages around provided Payloads using IRoutingMessage's to transfer the routing stack to a new NetworkMessage type
  
### AUID Management

GladNet2 routing specifications depends on AUIDs (Application Unique Identifiers) to route. Therefore, a GladNet2 implementation must adhere to the AUID specification:
