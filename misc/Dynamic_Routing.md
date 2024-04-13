# What is Dynamic Routing ?

In system design, dynamic routing refers to the technique routers use to automatically find the best path to send data packets across a network. Unlike static routing, where paths are manually configured, dynamic routing protocols allow routers to exchange information and adapt to changes in the network topology.

Here's a breakdown of how it works:

1. **Routing Protocols:** These protocols are the workhorses of dynamic routing. They enable routers to communicate with each other, sharing information about available paths and their characteristics (like congestion or latency). Popular protocols include OSPF (Open Shortest Path First) and RIP (Routing Information Protocol).

2. **Information Exchange:** Routers advertise their connected networks and the cost (metrics) associated with reaching those networks using routing protocols. This information exchange creates a network map for each router.

3. **Path Calculation:** Based on the received information, each router employs path-finding algorithms to determine the optimal path for each destination. These algorithms consider factors like hop count (number of routers to traverse), bandwidth, and delay.

4. **Routing Table Updates:** The optimal paths are then stored in the router's routing table, which guides the router in forwarding data packets.

5. **Adaptability:** The beauty of dynamic routing lies in its ability to adapt to network changes. If a link fails or becomes congested, routers can recalculate paths and update their routing tables automatically, ensuring data reaches its destination without interruption (or with minimal disruption).

Here are some advantages of dynamic routing:

- **Scalability:** Manages large and complex networks efficiently.
- **Adaptability:** Automatically adjusts to network changes for optimal routing.
- **Efficiency:** Reduces manual configuration and maintenance overhead.

However, there are also some drawbacks to consider:

- **Complexity:** Setting up and managing routing protocols can be complex.
- **Overhead:** Routing protocol communication adds some overhead to network traffic.
- **Security:** Routing protocols can be vulnerable to manipulation if not properly secured.

Overall, dynamic routing is a powerful tool for managing large and dynamic networks. By automating path selection and adaptation, it ensures efficient and reliable data flow across the network.

# Where is Dynamic Routing Used ?

Dynamic routing is primarily used by network administrators who manage medium to large-sized computer networks. These can be found in various organizations across different industries:

- **Enterprise Networks:** Large companies with extensive internal networks rely on dynamic routing for efficient data flow between departments, servers, and connected devices.

- **Internet Service Providers (ISPs):** ISPs use dynamic routing protocols to manage the complex internet backbone, ensuring optimal routing of data packets across vast geographical distances.

- **Data Centers:** Large data centers with numerous interconnected servers utilize dynamic routing for efficient communication and data transfer within the facility.

- **Cloud Providers:** Cloud platforms leverage dynamic routing to manage traffic flow within their infrastructure and efficiently route data to users across the globe.

- **Universities and Research Institutions:** These institutions often have extensive networks with various departments and research facilities, making dynamic routing essential for smooth communication and data exchange.

In essence, any organization that manages a complex network with multiple interconnected devices and frequent changes in network topology benefits from using dynamic routing. It helps them achieve efficient data flow, adapt to network fluctuations, and reduce manual configuration efforts.

Ref:

- [Acing the System Design Interview - Zhiyong Tan - Chapter 1](https://www.manning.com/books/acing-the-system-design-interview)
