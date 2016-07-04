# GladNet2 Routing Specification

GladNet2 contains internal message structures that implement INetworkMessage and IRoutableMessage. These contracts/interfaces are implemented on the NetworkMessage structure that serves as the unit of transfer between connected GladNet peers.

NetworkMessages define two wire-ready data members: the NetSendable<PacketPayload> and an internally managed Stack<int>.

### Stack{int}

This internal Stack<int> represents all the available information a NetworkMessage has and needs to transmit for routing information. Using SUID (Server Unique Identifiers) this stack preserves the history of routing accessible with LIFO semantics. Using SUIDs a NetworkMessage can retain source information for a given server.

Each SUID seen in the example structure below is unique per-server and serves as a history:

{  1  } <---- Represents the SUID of the current context's INetPeer.
   |
{  5  } <---- Represents the SUID of the previous subserver's INetPeer this NetworkMessage orginates from.
   |
{  3  }
