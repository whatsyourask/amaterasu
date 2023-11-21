![[Screenshot 2023-11-12 at 22.38.45.png]]
![[Screenshot 2023-11-12 at 22.41.18.png]]
Quantity of subnets you've created:
2 * sn  where sn = subnetting bits

sn <= the quantity of host-bits in original network.
We need 8 networks:
2 * sn >= # networks needed
2 * sn >= 8 = 3 bits
The more you steal for network bits the less you receive host bits.

2 * h - 2 = Quantity of hosts available per subnet. where h = host bits
Each subnet requires two, reserved bit-patterns:
- Network address - host-bits all zeroes
- Broadcast address - host-bits all ones


