[[Wireless Architecture]]
A central point of control can be implemented using a WLAN controller.
Access points managed by a controller are called Lightweight APs (or LAPs)
# Where do we place the controller?
* Cloud-based deployments
* On-site deployments
# Cloud-based Architectures
* WLAN controller is located in a public or private cloud
- Two Cisco options available:
	- Meraki wireless cloud service
	- Cysco Catalyst 9800-CL cloud-based controller
## Cisco Meraki
- Allows centralized management of Cisco Meraki Products
	- APs
	- Switches
	- Security Products
- Automatic deployment:
	- APs contact the cloud and self-configure
	- Code upgrade and configuration changes
	- RF channel selection and transmit power
	- Collection of information from AP such as rogue devices, wireless usages statistics. 