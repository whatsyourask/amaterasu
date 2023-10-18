[[Wireless Architecture]]
* Autonomous access point = individually managed access point.
* Each has its own GUi and/or CLI for configuration and management.
* May advertise one or more WLANs (i.e. SSIDs)
	* Multiple SSIDs utilize unique VLAN numbers
	* Within a single access point, multiple SSIDs typically share a single RF channel.
	* RF contention exists whether the PA is advertising one, or several SSIDs.
* Typically a separate management VLAN utilized for placement of the access point's IP address.
# Autonomous Decisions & Challenges
Decisions made by the access point:
* Which RF channel to use
* How to authenticate connected clients
* How to implement QoS
* Segmenting traffic into different VLANs for bridging into the wired Distribution System.
Challenges with this architecture:
* Additions of new SSIDs must be created one by one
* Changes to security or QoS policies must be implemented one by one
* Standardization across the entire corporate WLAN difficult to maintain
* Detection and mitigation of rogue APs is difficult.
![[Снимок экрана 2023-09-11 в 02.18.38.png]]