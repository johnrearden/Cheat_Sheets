# Terminal commands and tools

### ping

- Tests reachability, determine RTT (round trip time)
- Uses ICMP (Internet Control Message Protocol)

### traceroute (tracert WIN)

- List the hops for the request. 
    - Linux use -I or -T flags if ICMP packets are blocked along the way

### DNS
- Linux : host/dig/nslookup + domain name
    - reverse : dig -x or IP address for other 2 (prefer dig)
- Windows : nslookup
    - reverse : use IP address as argument

### tcpdump - capture packets from the command line
- Installed in linux, WIN install WinDump utility
- Can export to pcap format file (readable by wireshark) (packet capture)
- Check out the man page. -c flag limits output to a count

### netstat (-a)
- Show network connections.
- WIN -b flag shows application names, -n flag shows IP addr rather than domain name

### arp Address Resolution Protocol
- Determine MAC address based on an IP address
- arp -a View local arp table


