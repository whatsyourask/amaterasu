Soon became apparent that assigning multiple Classfull networks to each company would result in network depletion.
A single Classfull network was assigned to each company with the expectation of subnetting.
![[Screenshot 2023-11-12 at 22.15.32.png]]
Instead of looking at bits of address, look at something else - subnet mask
You receive from ISP:
- Network part
- Host part
ISP doesn't care what you do with Host bits, but you are not allowed to modify the "network" bits.

For example
ISP leases you the following network: 129.1.0.0
![[Screenshot 2023-11-12 at 22.18.19.png]]
Convert host bits to network bits.
![[Screenshot 2023-11-12 at 22.19.53.png]]
red bits - networking bits
green bits - subnet-bits
How do these hosts know how many bits represent the network? SUBNET MASK!
![[Screenshot 2023-11-12 at 22.23.43.png]]
Subnet mask helps identify network and host portion of network
3 representations:
- Binary
- Dotted decimal
- Backslash 