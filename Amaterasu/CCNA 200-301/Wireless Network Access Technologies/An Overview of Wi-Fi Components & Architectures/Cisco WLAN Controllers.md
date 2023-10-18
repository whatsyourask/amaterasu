[[Wireless Architecture]]
- Cisco WLAN controllers fall into 4 categories:
	- Physical
# Physical Controllers
- Provide a dedicated appliance for WLAN access point control
- Provide the greatest quantity of features of all the different types of WLAN controllers
# Cloud-based Controllers
- Meraki wireless cloud service
	- Scales to support an unlimited number of APs and clients
	- Requires a subscription to the Meraki cloud service
- Catalyst 9800-CL
	- Cloud-based controller for installation on public or private clouds
		- AWS,KVM and VMware
	- Packages as a virtual machine
	- Supports up to 6000 APs and 64000 clients.
# Embedded Wireless
- WLAN controller functionality is embedded within another network device (typically a switch)
- Cisco 9800 can be embedded within Catalyst 9300 switch
# Cisco Mobility Express
- WLAN controller functionality built into certain models of Cisco access points.
- Allows one access point to serve as a WLAN controller for (up to 100) other LAPs.
- Supports redundancy in which multiple Mobility Express controllers elect one to service all APs and clients.
	- AP serving as a controller can also service local WLAN clients
	- This is a special software image that replaces the normal CAPWAP AP image
	- No special license is required