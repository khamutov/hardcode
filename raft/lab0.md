# Raft Consensus Algorithm Implementation Challenge: Lab 0

It's expected that raft-servers works in a network environment. However, we have no description of network protocols and OSI level. Raft is network agnostic.

Raft assumes we known about other nodes and communicates through RPC. Our mission for Lab 0 is to implement environment in which Raft will work.

Key features of environment:

1. No real network IO.
2. Each node should work in the separate thread.
3. Node makes RPC using binary/text payload, not objects or structures.
4. The network can lose packets, splits into local subnetworks, exclude some node.
5. Packet in the network has random time to deliver. As a result, packets will reorder from time to time.

## Implementation details

1. We need a method to create the network and add servers to it. Each server has access to the list of servers in the network.
2. Each message in RPC should be delayed for random time from interval.
3. A node sends a message using an interface like `bool callRPC(byte *body, string serverId)`. It can be blocked or not; it's the design decision. Boolean shows message delivery status, true if ok, false if an error occurred.
4. There should be the separate thread (message loop) for message delivery between nodes. Message loop should not preserve ordering.
5. It should be run on multi-core processor on several threads. Say buy-buy to javascript.