[[Wireless Architecture]]
# Connection of WLAN Components
- The components of a WLAN consist of:
	- Access points (Autonomous and/or Lightweight)
	- Wireless NICs on host devices
	- Wireless transceivers built into Wi-Fi hosts and access points
	- WLAN controllers (optional)
- WLAN access points and controllers rely on physical connections in order to exchange data and control messages
- So what do these connections look like?
	- Uplink ports
# LAG
- LAG = Link Aggregation Group
- Bundles multiple physical interfaces into a single logical port group
- Can be implemented on access points, controllers, or both.
- Provides redundancy and load-balancing
- Requires that the connected switch support load balancing on the layer 4 (L4) source and destination ports.
## Access Point LAG considerations
- Only works when access point is in Local Mode.
- Not tested between Cisco access points and non-Cisco switches
- WLAN controller must be configured to support LAG on the access points
- Required on the AP in order to get maximum 802.11ac phase-2 (and above) supported bandwidth
- Access points only support LACP or ON modes. PAgP not supported
## Controller LAG considerations
- Controllers configured for LAG don't support LACP or PAgP
- Load-balancing of frames leaving the controller across the LAG is done in a proprietary manner by the controller.
- Only one(1) AP-Manager interface is allowed when ports are bundled into a LAG.
- Without LAG, if a physical port fails, any access points that registered acroess the port must reboot.