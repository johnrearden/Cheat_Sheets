# Networking devices

### Router
Take data from one IP subnet and route it to another IP subnet.(Layer 3) 

### Switch 
Layer 2. Uses MAC addresses.

### Firewall 
- Traditional - filter based on TCP/UDP port number
- NGFW Next Generation Firewall - filter based on application, and decide whether
application should be allowed/blocked on the network.
- Firewalls can also encrypt traffic, through a VPN.Common to have a firewall at either end, joined together in an encrypted tunnel. Like switches, firewalls can also have Layer 3 abilities, and can act as a router.
- IDS/IPS Intrusion detection/prevention system. Watch the network and identify attacks as they traverse the network. Identify common known attack types and exploits (buffer overflows, cross-site-scripting etc.)
- Load balancer : distribute the load to multiple servers, provide fault tolerance (removing servers that are faulty)
    - TCP offload : take care of protocol paperwork for servers incl 3-way handshake SYN, SYN-ACK, ACK, maintains the connection.
    - SSL offload/termination : do TLS encryption/decryption for the servers
    - servers need only operate on HTTP requests and run their application logic. Connection management and encryption handled by load balancer.
    - data can be cached on the load balancer.
- Proxy servers : sit between users and external network. Can do caching, access control, URL filtering, content scanning
- NAS (network attached storage) - Shared storage device across the network. File-level access (entire file must be transmitted and sent back after editing)
- SAN (storage area network) - Block level access - looks and feels like using a local storage device. Very efficient at reading and writing.
- Access point : A wireless access point, not including router/switch (layer 2 device, usually connected to the rest of the network via ethernet, so not a repeater)
- Wireless LAN controller - single pane of glass to manage entire network - often proprietary systems.

# Networking Functions

- CDN : Content Delivery Network - a network of caching servers which allows users to get data from a local server
- VPN : Virtual Private Network - encrypted communication on an insecure medium. A VPN often uses a **concentrator/head-end**, a single access point for all of the users using that VPN. This is often integrated into a firewall, and is a purpose-built appliance. This is an ASIC, an application specific integrated circuit.  
- QoS : Quality of service. A real-time video or audio stream may have a higher priority than a file transfer. Set prioritization for different applications.
- TTL : Time to Live. Gives each hop (router/switch/firewall) the ability to decide if it should continue to propogate the encapsulated data (frame/packet) or drop it. Can be based either on number of network hops or a time limit. A packet or a cache is a good example. Default TTL on mac/linux is 64 hops, on windows 128. Typical hop count over the Internet is 12-16.



