# Network function virtualization (NFV)

Replace physical network devices with virtual versions, managed from the hypervisor
- same functionality as the physical devices
- push a button, deploy new VM ... push button, deploy new server

### Virtual Private Cloud (VPC)
- Pool of resources created in a public cloud
- We can connect VPCs with a transit gateway (a cloud router)
- Internet gateway - connect users on the internet
- VPC NAT gateway - network address translation, private cloud subnets connect to external resources
- VPC endpoint - direct connection between VPC on one cloud provide to a VPC on a different provider

### Security Groups and Lists
- A firewall for the cloud - controls inbound and outbound traffic flows
- Layer 4 post number (TCP or UDP)
- Layer 3 address
    - individual addresses
    - CIDR block notation (classless inter-domain routing eg 192.168.1.0/24)
    - IPv4 or IPv6

### Network security list

| direction | protocol | port | ip address |
| --- | --- | --- | --- |
| Inbound | TCP | 443 | 0.0.0.0/0 |
| Inbound | TCP | 22 | 0.0.0.0/0 |

Used to apply to each cloud network - same rules for all

### Network security group 

| group | direction | protocol | port | ip address |
| --- | --- | --- | --- | --- |
| NSG-A | Inbound | TCP | 443 | 0.0.0.0/0 |
| NSG-B | Inbound | TCP | 22 | 0.0.0.0/0 |

Individual vNICs can be added to the groups to control access

# Cloud Models

- Saas (gmail, office365)
- IaaS (linode, aws ec2)
- PaaS (heroku, render)