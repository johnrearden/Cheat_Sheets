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






  
