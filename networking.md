## 1 - Layering and services
1. Which five levels is the TCP/IP protocol stack made out of?
- Application
- Transport
- Network
- Link
- Physical

2. For the following protocols, which layer in the stach are they implemented in?

- Domain Name System
DNS runs in the application layer parallel to the program using it.

- Internet Control Message Protocol (ICMP)
Network layer

- Ethernet IEEE 802.3
Physical

- Dynamic Host Configuration Protocol (DHCP)
Application

3. Which levels in the TCP/IP protocol stack is implemented on a router?
Network, Link and Physical.

In a host, all five layers are implemented.

4. What is muliplexing and demultiplexing? Give an example of how they are used in the TCP/IP stack.

The job of delivering data in the transport-layer to the correct socket is called demultiplexing. The job of gathering data chunks at the source host from different sockets, encapsulating each data chunk with header information (that later will be used in demultiplexing) to create segments, and passing the segments to the network layer is called multiplexing.

5. What is the difference between a hub and a switch?
A hub will only take an input and provide more than one output, or the other way, and it operates on the physical layer. A switch on the other hand can redirect the segments to the correct destinations based on header information. So it also operates on the link layer.

## 2 - Transport protocols

