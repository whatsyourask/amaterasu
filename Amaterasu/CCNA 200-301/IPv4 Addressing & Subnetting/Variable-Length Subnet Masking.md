Same-Length subnet masking doesn't consider wasting hosts bits when we don't have so may hosts in our subnet.
VLSM takes approach from how many hosts bits we need
We start with initial network subnet - /24
We start with a large subnet with many hosts:
80 hosts - /25 mask
Then let's go with the second large subnet:
22 hosts - /27 mask
Then:
12 hosts - /28 mask
5 hosts - /29 mask
![[Screenshot 2023-11-12 at 23.20.23.png]]
SLSM:
- Each network utilizes the same mask
VLSM:
- Provides ability to allocate IPv4 as per the host requirements
- Subnet mask can be variable