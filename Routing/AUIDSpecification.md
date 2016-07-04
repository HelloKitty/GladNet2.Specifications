# GladNet2 AUID Specification

An AUID is an Application Unique Identifier. These are used in a handful of systems within GladNet2 such as [Routing](https://github.com/HelloKitty/GladNet2.Specifications//master/Routing/RoutingSpecification.md).

## GladNet2 AUID Requirements

AUIDs must have the following traits for a GladNet2 implementation to be correct:

* AUIDs must be integer values defined within IConnectionDetails implementation

* AUIDs must be mappable to INetPeer instances that have the associated AUID

* AUIDs must map to INetPeer instances in O(1) time

* GladNet2 must internally manage this AUID mapping service

* AUIDs of INetPeer's that are no longer connected should be removed from the map service
