# Network Topology

*Network topology is the arrangement of the elements of a communication network*.
Network topologies can be studied under two categories: physical and logical topologies. **Physical topology** is the transmission medium used to link devices and usually refers to the physical structure of the network--layout of cabling, location of nodes and the links between them, and the cabling [1]. On the other hand, **logical topology** refers to the manner in which data passes through the network. The physical and logical topologies are not necessarily the same for a given network.

The **links** between nodes can be wired or wireless technologies. Examples of wired links include coaxial cable, twisted pairs, optical fibers, etc.; and terrestrial microwaves, communications satellites, cellular systems, free-space optical links are examples of wireless technologies.

**Nodes** are the points of connection to the transmission medium of the system. Nodes are typically associated with a computer but can also contain a microcontroller, or even no processing device at all. Typical nodes include network interface controllers (NICs), repeaters and hubs, bridges, switches, routers, modems, and firewalls. 

## Basic Topologies
Eight basic network topologies have been identified [2]. These are: 

### Point-to-Point 
Point-to-Point is the simplest of topologies, wherein a dedicated link exists between two nodes. **Advantages**: 
- Simple topology; low cost.
- Maximum utilization of bandwidth. Provides unimpeded communication between two endpoints.
- Least delay in communication.
**Disadvantages**:
- Network is limited to two nodes.
- Performance is constrained by the single link. Loss of link leads to loss of the network.
- Effective only in smaller distances

### Daisy Chaining
In a daisy chained network, each node is connected to the next in a series. Messages intended for a node are bounced along the sequence of nodes until it reaches the target. Daisy chained networks can be linear or ring shaped. The latter offers better fault tolerance since the loss of any link in the ring can still be handled.
**Advantages**:
- Simple topology; low cost.
- Easy to manage physical connections--cables are minimal.
**Disadvantages**
- The network speed is significantly affected by the addition of new nodes to the system.
- Each node requires two transmitters and receivers.

### Bus
In a bus topology, each node is connected to a central, common cable called the 'bus'. All the data transmission in the network occurs over the bus.
**Advantages**:
- Cost effective.
- Additional nodes can be added easily.
- Lesser wiring effort compared to other topologies.
**Disadvantages**
- The common cable can be a single point of failure
- Although nodes can be added easily, the network bandwidth is limited by the bus.
- Signal interference and attenuation.

### Star
In a star, every node is connected through a central hub, which serves the node for distributing all traffic.
**Advantages**:
- Simple topology.
- Very flexible.
- Robust to failure of peripheral nodes. Loss of node does not lead to loss of network. 
**Disadvantages**:
- Central hub is a single point of failure.
- Bandwidth of the system is constrained by the central hub.

### Ring
A ring is a linear daisy chain in a closed loop.
**Advantages**:
- Performance is better than bus topology.
- No need for a network server. Each node only has two peers to receive data from and send data to.
**Disadvantages**:
- Network bandwidth is bottlenecked by the slowest node.
- Link failure leads to either partial or complete loss of system. 
- Communication delays due to sequential passing of data.

### Mesh 
A mesh or a graph network can be fully or partially connected. 
**Advantages**:
- Robust to failures. Loss of node or link does not lead to loss of system. 
- Addition of nodes does not necessitate network reconfiguration.
- Network bandwidth can be scaled easily.
**Disadvantages**:
- Expensive to construct.
- Trickier to maintain. 

### Hybrid
Hybrid topologies are the result of combining two or more different basic topologies in such a way that the new network does not exhibit the topology of any of its composition. Examples include:
- star-bus or tree networks
- star-ring networks
## References
[1] Groth, David; Toby Skandier (2005). _Network+ Study Guide, Fourth Edition_. Sybex, Inc. [ISBN](https://en.wikipedia.org/wiki/ISBN_(identifier) "ISBN (identifier)") [0-7821-4406-3](https://en.wikipedia.org/wiki/Special:BookSources/0-7821-4406-3 "Special:BookSources/0-7821-4406-3").

[2] https://www.watelectronics.com/network-topology-types-advantages-disadvantages/