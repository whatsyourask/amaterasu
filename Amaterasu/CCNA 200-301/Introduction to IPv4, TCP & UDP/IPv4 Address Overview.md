[[Introducing IPv4]]
- 32-bit addressing system
- Logical address for a network defined by IANA
	- Network portion of address
	- Host portion of address
- IPv4 addresses are comprised of 4 octets
	- 11000000 00000001 00000001 00000011
- Dotted decimal notation is used to segment the octet
	- 192. 1. 1. 3
# Network & Host Bits
- Devices with IPv4 addresses need to be able to identify the "network" portion of their address
	- Determines the necessity of directing packets to a default gateway for off-network destinations
	- Determines if ARP is needed to reach local hosts
# IP Subnet Masks
In a past there were a few classes defined by first bits of ip address: A, B, C, D, E
Later, CIDR was invented. Classless Inter-Domain Routing.
# IPv4 Address Types
- Unicast
	- one-to-one communication
	- IPv4: utilizes class-a, b & c address space
- Multicast
	- one-to-many communication
	- IPv4: utilizes class-d address space
- Broadcast
	- one-to-all communication
	- general broadcast - 255.255.255.255
	- directed broadcast - 255.255.0.0 with subnet mask 1.1.0.0. thus directed broadcast will be 1.1.255.255