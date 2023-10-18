[[Wireless Architecture]]
Deploying WLAN controllers on-site:
- Centralized
- Converged
# Centralized Wireless Network Architecture
- Single controller placed in a centralized location.
- Maximizes quantity of APs each controller can control.
- RTT (Round-trip time) should be 100ms (or less) between LAP and controller.
# Centralized Architecture - Pros & Cons
- Benefits:
	- Controller placed in datacenter or close to Internet access point
	- Assumption is that most client data would need to reach that point anyway
- Drawbacks:
	- All WLAN client data must pass through CAPWAP tunnel before it can be placed natively onto the wired network.
	- Not a great option when destination resources needed. by WLAN client are located closer to the AP
	- NOt a great option for client-to-client communications (long traffic paths)
	- When branch office LAPs are associated to a controller at the central office:
		- Long hair-pins induced for branch office users to access local resources.
		- If CAPWAP tunnel goes down, no Wi-Fi connectivity to anything.
# FlexConnect Wireless Network Architecture
- A mode available on LAPs
- Primarily designed for Remote-Site/Branch LAPs utilizing a controller located at the HQ.
- Designed to alleviate long hairpins.
- Wireless traffic destined for local resources switched locally by LAP onto wired LAN.
- LAP also allowed to locally authenticate WLAN clients.
- Works even when CAPWAP tunnel goes down due to WAN failure
- FlexConnect LAPs operate in two modes:
	- Connected mode - When LAP has connectivity to remote controller
	- Standalone mode - When LAP has lost connectivity to remote controller
# Converged Wireless Network Architecture
- Moving the WLAN controller away from the core towards the access or distribution layers.
- Implementing multiple controllers to divide the load from APs
- Typically implemented using embedded WLAN controllers based on IOS-XE software
![[Снимок экрана 2023-09-12 в 04.29.19.png]]
Benefits:
- Short CAPWAP data tunnels reduces the impact of hairpins
- Better support for faster Wi-Fi technologies (802.11ad, 802.11ax, etc)
	- When these technologies used, Wi-Fi clients are operating at multi-gigabit speeds which would be hard to support using long CAPWAP tunnels
- Less APs per embedded controller reduces load on the controller.
- Controller functionality can be placed at the Branch site
Drawbacks - more initial dollars outlay because several controllers needed or higher-cost switches are required