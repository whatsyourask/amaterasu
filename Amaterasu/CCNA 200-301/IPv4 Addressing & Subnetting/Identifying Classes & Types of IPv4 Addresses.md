# IP Bit Patterns
- Multicast
	- one-to-many communication
	- 1110xxxx.xxxxxxxx.x.x
- Broadcast
	- one-to-all communication
	- Host portion of address all ones or entire address all ones.
	- any.11111111.11111111.11111111
	- 11111111.11111111.11111111.11111111 - for example ARP, DHCP will use that
- Unicast
	- One-to-one communication
	- All other patterns that do not start with 00000000
# Classes of IPv4
A: 0.0.0.0 - 127.255.255.255
B: 128.0.0.0 - 191.255.255.255
C: 192.0.0.0 - 223.255.255.255
D: 224.0.0.0 - 239.255.255.255
E: 240.0.0.0 - 255.255.255.255

127 ranges are considered as loopbacks
169.254 ranges are considered as APIPA

Networks for unicast transmission:
0xxxxxx.x.x.x - Class-A Address
	Hosts: 24 bits
1xxxxxxx.x.x.x
	10xxxx.x.x.x - Class B Address
	Hosts: 16 bits
110xxxxx.x.x.x - Class C Address
	Hosts: 8 bits

Class A:
0xxxxxxx.any.any.any 
0000000
	1.any.any.any - 126.any.any.any
01111111

Class B:
10000000.any.any.any = 128.any.any.any
10111111.ay.any.any = 191.any.any.any

Class C:
11000000.any.any.any = 192.any.any.any
11011111.any.any.any = 223.any.any.any

Class D reserved for multicast.
1110 - Class D

11100000 = 224.any.any.any
11101111 = 239.any.any.any

Class E (originally known as experimental)
11110000


