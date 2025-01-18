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




