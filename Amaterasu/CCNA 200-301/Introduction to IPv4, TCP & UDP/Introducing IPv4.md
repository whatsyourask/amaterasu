# IPv4 Header Fields
![[Screenshot 2023-10-15 at 17.06.54.png]]
**IHL** - Header Length (20 bytes for example)
**Type of Service (ToS)** - priority of packets
**Total Length** - length of header + all behind data
**Identification** - number of packet
**Flags** - 
**Fragment Offset** - offset of the packet from the previous one
**Time To Live** - decremented each time the packet goes through the router
**Protocol** - The protocol of payload data
**Header Checksum** - destination checks the checksum if the packet was modified
**Source IP Address** - from where the packet came from
**Destination IP Address** - to where the packet goes

**MTU** - Maximum Transmission Unit. For Ethernet it's 1518 bytes
Fragmentation:![[Screenshot 2023-10-15 at 17.18.07.png]]
![[Screenshot 2023-10-15 at 17.21.42.png]]