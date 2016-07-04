# GladNet2 Routing Specification

GladNet2 contains internal message structures that implement INetworkMessage and IRoutableMessage. These contracts/interfaces are implemented on the NetworkMessage structure that serves as the unit of transfer between connected GladNet peers.

NetworkMessages define two wire-ready data members: the NetSendable<PacketPayload> and an internally managed Stack<int>.

### SUID, Stack{int} and RoutingStack

This internal Stack<int> represents all the available information a **NetworkMessage** has and needs to transmit for routing information. Using **SUID (Server Unique Identifiers)** this stack preserves the history of routing accessible with LIFO semantics. Using SUIDs a NetworkMessage can retain source information for a given server.

The below example routing stack contains 3 SUIDs and can be treated as a history of the NetworkMessage:

{  1  } <---- Represents the SUID of the current context's **INetPeer**.

   
{  5  } <---- Represents the SUID of the previous peer's **INetPeer** this NetworkMessage orginates.

   
{  3  } <---- Represents the SUID of the first peer's **INetPeer** that recieved this NetworkMessage.

Note above that SUID 3 is the source SUID but generally represents a session of a remote-peer so reaching the peer with that SUID is generally not enough to finish routing. You must finish the route by sending the response/event/request through the Peer with that SUID.
