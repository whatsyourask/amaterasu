# Cloud Networking
---
Building networking in a cloud a lot easier
## Basic Networking
Called by provider - Virtual Network / Virtual Private Cloud
Virtual Network/ VPC:
- Multiple subnets
- Concepts:
	- IP addresses
	- DNS
	- Security
	- Routing
- Connect VM/EC2 to this environment at the subnet level
- Look beyond VPC:
	- Platform services
	- Gateways peering - ingress and egress
	- Public IP for VM/EC2 - directly to instance or to NIC
![[Screenshot 2023-11-05 at 22.52.07.png]]
Cloud networking isn't:
- Cloud networking doesn't extend below Layer 3
	- No VLANs
- Some operations are not supported
	- Broadcast
	- Multicast
- Some protocols are not supported:
	- GRE (Azure)
## Network Connectivity
- On-premises
- Multiple Virtual Networks/VPCs
All cloud providers have a concept - Cloud Gateway
Connect on-premises to VPC through:
- VPN tunnel with Cloud Gateway
- Private socket
Connect VPCs together with:
- Peering
- Gateway between VPCs
- Network Virtual Appliance
![[Screenshot 2023-11-05 at 22.58.36.png]]
## Network Security
- ACL
- Network Security Groups
- Apply on virtual network level or on per-instance level
- For public IP address:
	- Control through NSG
# Cloud Compute
---
## Cloud-based Compute
We have:
- Instance (VM/EC2)
	- Size
		- Series - Type of vCPU 
		- Number of vCPU available
		- RAM
		- Disks (and its Type)
			- Storage
				- Disk represents an OS 
				- Data disk
			- Image
		- Network
- V-NET/VPC - Instance connected to it through NIC
## Compute Sizing
- Series/ type
	- Type and max number of CPU
	- Type and available memory
	- Network capabilities
	- other features - GPU
- Size
	- Specific combination of processor, memory, storage, networking and features
- AWS
- Azure
- Google Cloud
## Compute Images
- Standard images
	- Linux
	- Windows
- Marketplace images
- Custom images
## Compute Settings
Azure:
![[Screenshot 2023-11-06 at 17.30.56.png]]
## Accessing Cloud Machines
- VMs connected in one subnet
- On-prem connects via tunnel
- Web-based console
- Bastion host - ssh or rdp
- Public IP address
# Cloud Storage
---
## Cloud Storage Options
![[Screenshot 2023-11-06 at 17.47.13.png]]
## Controlling Access to Cloud Storage
Disks and file storage controlled as filesystem storage

We have a Storage Account:
- Container
	- File
2 ways of access:
- Control Plane
	- Cloud Identity
		- Role/Policy - defines certain rights to access
- Data Plane
	- how to control access to the content?
		- Allow anonymous access or ?
			- Always read-only
		- Authentication/Authorization ?
			- Key-based authentication
				- Access to everything or a specific access
			- Network ACL
## Disk and Image Storage
![[Screenshot 2023-11-06 at 18.13.25.png]]
You can build images