# OSI Model
-  Open Systems Interconnection Reference Model. Unique protocols at every layer.
-  Mnemonic: "All People Seem To Need Data Processing"

### Layers
7: Application  
6: Presentation  
5: Session  
4: Transport  
3: Network  
2: Data-link  
1: Physical  

### Layer 1: Physical
Concerned with signaling, cabling, connectors (no protocols) - check connections, swap adapter cards
Working with : physical cable, looking at EM transmission across wireless

### Layer 2: Data-link
Data Link Control (DLC) protocols / switching layer
- MAC (Media Access Control) address on Ethernet : communication between cards
Working with : Switch forwarding, MAC addresses
Transmission unit : frame, used by ethernet

### Layer 3: Network
Routing layer - layer associated with IP addresses
Unit of communication : Frame 
Working with: IP addresses
Transimission unit : packet, used by TCP or UDP

### Layer 4: Transport
Post office layer - TCP (Transmission Control Protocol) and UDP (User Datagram Protocol)
Working with: TCP and UDP protocols and the port numbers associated with these

### Layer 5: Session Layer
Communication Management between devices - start, stop, restart  
Control protocols, tunneling protocols
Working with: Tunnelling or setting up sessions between hosts

### Layer 6: Presentation Layer
Encryption/decryption
Character encoding
Working with: Encryption

### Layer 7: Application Layer
This is where you come in 
Working with: Code, Browser

![wrappers at layers](assets/data_communication_osi_layers.png)

## Maximum Transmission Unit (MTU)
The largest group of data that can be sent across a connection without fragmenting.
Staying within the MTU minimizes overhead of chopping up data and reassembling.

---

# Network Topologies
- Star/ Hub and Spoke - one central device
- Ring Networks (used in MAN and WAN networks) - data can avoid a break in the circle
by looping back in the opposite direction
- Bus network - single cable runs through a floor with a branch for each device - breaks can create
segmentation or complete failure
- Mesh network - many/all nodes connected to many/all others, common with IOT devices
- Hybrid network - combination of some/all of the above

# Network Types
- ### Peer to peer
    - Low cost
    - Easy to deploy (no server)
    - Difficult to administer
    - Difficult to secure
- ### Client/server
    - No client/client communication
    - High performance
    - Easy to maintain - one central configuration point
    - Cost (hardware needed) monetary and space
- ### LAN 
    - Local is relative - building or group of buildings
    - Fast communication - 802.11 wireless and ethernet
- ### MAN Metropolitan Area Network
    - Geographically dispersed buildings/installations
    - Used by local government, as they control the right-of-way to install fibre
- ### WAN
    - Connects LANs over distance
    - Much slower than LANs

### Storage
    - NAS (Network Attached Storage) - file level access - entire file needs to be overwritten
    - SAN (Storage Area Network) - more like local file access
    - both require high network throughput, and usually require a dedicated network

### MPLS (Multi Protocol Label Switching):
    - Packets through the WAN have a Label
    - Routing decisions are Easy
    - Allow any kind of traffic - packet/frame etc.

### mGRE (Multipoint Generic Router Encapsulation)
    - Used extensively for Dynamic Multipoint VPN (DM-VPN)
    - Common on CISCO routers
    - VPN builds itself/ remote sites communicate to each other
    - Tunnels are built dynamically, depending on demand, using a dynamic mesh

### Software Defined WAN (SD-WAN)
    - WAN built for the cloud
    - Individual nodes on the network connect individually to services in the cloud








  
