[[Wireless Architecture]]
# Divide & Conquer
- Split-MAC architectures involve the following:
	- Implementing an on-premise controller
	- Utilizing lightweight access points (LAP) that essentially split the functionality of autonomous APs into 2 sections.
- Real-time functions handled locally by Lightweight AP (LAP).
- Management functions offloaded to a WLAN controller.
# What is Split?
- Real-time functions handled locally by Lightweight AP (LAP).
	- RF transmit/receive of frames (management, control and data)
	- MAC management (DCF)
	- Encryption
- Management functions offloaded to a WLAN controller.
	- RF management:
		- Channel selection
		- Transmit power
		- etc
	- Association and Roaming Management
	- Client authentication
	- Security Policy Management
	- QoS