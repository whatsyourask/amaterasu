Network Topology Architecture depends on different things:
- Physical devices and cabling needed
- Path of data traffic
- Redundancy
- Use of 3rd party services
# 2 Tier & 3 Tier architectures
## 2 Tier architecture
Access layer
2 Tier - include core and distribution layer
![[Screenshot 2023-10-01 at 16.38.00.png]]
## 3 Tier architecture
First designed methodology
**Access Layer** - gives hosts physical connectivity to the network.
**Distribution Layer** - composed of routers or switches. Data that wants go through one group to another will go through distribution layer. Routing security is here also.
**Core Layer** - super fast layer that meant to get data from major sections. Core layer will participate in sending data between, for example, different buildings.
![[Screenshot 2023-10-01 at 16.30.36.png]]
# Spine-Leaf Architectures
Data center networks are typically designed as Spine-Leaf architectures
![[Screenshot 2023-10-01 at 16.42.20.png]]
Interconnections between switches can be L2 or L3.
Switches are very pricy and never go down.
This topology is better for east-west traffic.
Spine and leaf switches - "The fabric".
# WAN Architectures
- WANs provide a variety of connection methods
- Several transport methods:
	- point-to-point ![[Screenshot 2023-10-01 at 16.48.34.png]]For each new point it has to create a new port and a wire.
	- Broadcast![[Screenshot 2023-10-01 at 16.50.32.png]]
	- Non Broadcast Multi-Access (NBMA)![[Screenshot 2023-10-01 at 16.54.45.png]]
	- SOHO = Small Office/ Home Office. Less equipment demands. Less need for authentication and security. Difficult to manage and enforce policy from HQ.
# Supersets of Architectures above
![[Screenshot 2023-10-01 at 17.03.18.png]]
On-Premises  - all network devices in your control and physically in one building.
Cloud-based - all responsibility goes to hosting company.