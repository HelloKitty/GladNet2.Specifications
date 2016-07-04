# GladNet2 Routing Specification

GladNet2 contains internal message structures that implement INetworkMessage and IRoutableMessage. These contracts/interfaces are implemented on the ResponseMessage and RequestMessage structures that serves as units of transfer between connected GladNet peers.

These NetworkMessages define two wire-ready data members: the NetSendable{PacketPayload} and an internally managed Stack{int}.

In short, there are currently two Types that can be routed and these NetworkMessage Types are:
 * **RequestMessage**
 * **ResponseMessage**

### AUID, Stack{int} and RoutingStack

This internal Stack{int} represents all the available information a **RequestMessage** or **ResponseMessage** has and needs to transmit for routing information. Using **[AUID](https://github.com/HelloKitty/GladNet2.Specifications/blob/master/Routing/AUIDSpecification.md)s (Application Unique Identifiers)** this stack preserves the history of routing accessible with LIFO semantics. Using AUIDs a RequestMessage or ResponseMessage can retain source information for a given server.

The below example routing stack contains 3 AUIDs and can be treated as a history of the RequestMessage or ResponseMessage:

{  1  } <---- Represents the AUID of the current context's **INetPeer**.

   
{  5  } <---- Represents the AUID of the previous peer's **INetPeer** thisRequestMessage or ResponseMessage orginates.

   
{  3  } <---- Represents the AUID of the first peer's **INetPeer** that recieved this RequestMessage or ResponseMessage.

Note above that AUID 3 is the source AUID but generally represents a session of a remote-peer so reaching the peer with that AUID is generally not enough to finish routing. You must finish the route by sending the response/request through the Peer with that AUID.

### AUID Pushing and Popping

GladNet2 specifications for AUID pushing and popping are as such:

* End-users should not attempt to manually push or pop AUIDs as resulting routing behaviour may become undefined

* Incoming RequestMessages or ResponseMessages on a given **Peer** will internally push their own AUID defined in their **IConnectionDetails**

* A GladNet2 INetworkMessageRouteBackService provides the following functionality
  * Can forward NetworkMessages fully intact with routing stack preserved
  * Can route back NetworkMessages by popping the routing stack and routing through the current application
    * Throws on empty routing stack
  * Can build RequestMessages or ResponseMessages around provided Payloads using IRoutingMessage's to transfer the routing stack to a new NetworkMessage type
  
### AUID Management

GladNet2 routing specifications depends on AUIDs (Application Unique Identifiers) to route. Therefore, a GladNet2 implementation must adhere to the AUID specification: [AUID](https://github.com/HelloKitty/GladNet2.Specifications/blob/master/Routing/AUIDSpecification.md)

AUIDs can be used in the routing stack and used for route back if and only if the specification is followed.

### Limitations of the GladNet2 Routing Scheme

GladNet2's routing scheme as defined above as one sigificant limitation. That limitation being we can only route linearlly and the same route path taken must be used to route back. This is not a significant limitation, especially considering the vastness of the feature space, but it is the most significant.

P1 --->  P2 --->  P3 must routeback in a the reverse linear fashion P3 -->  P2 -->  P1

This is not valid P1 --> P2 --> P3 | P3 --> P4 --> P1 as the routing stack will not know how to route to P1 from P4.

