[[Split-MAC Architecture]]
# Overview
- Control and Provisioning of Wireless Access Points.
- Tunneling protocol between LAP and WLAN controller.
- Encapsulates data between LAP and controller in a new IP headers.
# CAPWAP Tunnels
- Access Points and controllers build 2 CAPWAP tunnels between themselves.
	- One tunnel is used to transmit CAPWAP Control messages.
	- The other is for tunneling CAPWAP data
# CAPWAP Tunnels for Control
- Control Messages
	- Used to control and manage LAP
	- Messages are authenticated and encrypted
		- Utilizes DTLS with X.509 Digital Certificates. DTLS - Datagram TLS.
		- Pre-installed on devices at time of purchase.
	- Utilizes UDP port 5246 at the controller
# CAPWAP Tunnels for Data
- Data
	- All Wi-Fi data frames use this tunnel, even frames going between two Wi-Fi clients on the same AP.
	- Uses UDP port 5247
	- Not encrypted by default
	- DTLS can be added for encryption