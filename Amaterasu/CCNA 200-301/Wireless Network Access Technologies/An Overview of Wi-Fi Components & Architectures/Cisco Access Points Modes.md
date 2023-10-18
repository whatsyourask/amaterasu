[[Wireless Architecture]]
- Cisco access points can operate in any of several modes
- The mode-of-operation determines things such as:
	- Is the AP associated with a controller, or not?
	- Can the AP accept Wi-Fi clients?
	- How will the AP switch frames from Wi-Fi clients? 
	- Can the AP report on RF conditions, capture Wi-Fi traffic or detect rogue devices?
# Local & Bridged Modes
- Local mode
	- Default mode for LAPs
	- AP creates CAPWAP tunnels to WLAN controller
	- All control and data flows across CAPWAP tunnel
	- Should CAPWAP tunnel fail (WAN failure, etc) AP disconnects all WLAN clients and must find another, available controller
- Bridged mode
	- Allows an autonomous access point to act as a WLAN client and associate to a LAP
	- Wired clients (without a Wi-Fi NIC) can connect to the Ethernet port on the back of the autonomous AP and have their traffic bridged across the WLAN to gain access to the Distribution System. 
# FlexConnect Mode
- Access Point utilizes services of controller, just like Local Mode
- This mode must be supported/configured on WLAN controller and AP
- Primarily designed for access points placed at remote sites
	- Allows AP to locally bridge Wi-Fi traffic onto the wired LAN for clients associated to specific SSIDs/VLANs
	- Allows AP to continue to operate even if link to controller fails
# Monitor Mode
- Cisco access points spend about 0.2% of their time performing off-channel scanning
	- This allows the AP to generate access point rogue alerts, signature attacks, (IDS, IPS) and report about other environment events
	- This function is a part of RRM (Radio Resource Monitoring/Management)
- By default, AP spends roughly 0.2% of its total time performing off-channel scanning which translates to about 60ms per scan.
- Monitor mode is a receive-only mode (no WLAN clients allowed) which scans pre-configured channels every 12 seconds
	- Listens for (and reports to controller) any Wi-Fi traffic "heard" on the selected channel
	- AP will reboot when set to this mode
# Sniffer Mode
- Very similar to Monitor mode
- Unlike monitor mode sniffer mode requires you select an individual channel
- All Wi-Fi traffic caught on this channel sent to WLAN controller
- Controller configured with LAN IP address of a host running Wireshark
- Packets sent to LAN host encapsulated in UDP
# Sensor Mode
- An AP functioning in Sensor mode is part of a solution involving:
	- A WLAN controller
	- An Access point
	- Cisco DNA Center (high-level programming tool to implement network)
- WLAN controller receives a series of preconfigured tests to be run from DNA Center, which is then pushed down to access point 
- SSIDs are selected on controller for which tests are to be performed
- AP in sensor mode will associate to all other LAPs it can hear and perform tests including:
	- Onboarding tests
	- Network tests
	- Application tests
- All test results sent directly from PA back to DNA center appliance.
# Mesh Mode
- In some envs, providing Wi-Fi to clients is desired but there may be no physical connection for access points to connect to a wired LAN:
	- Parks
	- City streets
	- Warehouses
- APs associate with each other to carry data from a Wi-Fi client to the AP with ethernet connection.
- Access points in mesh mode operate in one-of-two ways:
	- Root access point (RAP) - has wired access to controller
	- Mesh access points (MAP) - has wireless access to controller
- A mesh network requires at least one RAP
- MAPs use the Cisco Adaptive Wireless Path Control (AWPP) to determine the best path through the other mesh access points to the controller
- Mesh access points can also simultaneously operate in any of the previously-mentioned modes.