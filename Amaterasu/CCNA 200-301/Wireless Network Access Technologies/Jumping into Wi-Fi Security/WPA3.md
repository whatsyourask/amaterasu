[[Wi-Fi Security Authentication]]
- Adds several new features to WPA
- Still retains concept of WPA Personal and Enterprise
- Different features available in Personal vs Enterprise
802.11s - about Mesh networks and its security
# WPA3 Personal
- Provides more robust password-based authentication, even on networks with weak passwords
	- Replaced Pre-Shared Key (PSK) with Simultaneous Authentication of Equals (SAE)
	- Protects against offline dictionary attacks
	- Per-user encryption keys that are not linked to a shared passphrase
	- Implements Forward Secrecy
- Requires the use of PMF (Protected Management Frames)
# WPA3 Enterprise
- Requires the use of PMF (Protected Management Frames)
- Introduces a new 192-bit-minimum cryptographic security suite "aligned with the recommendations from the Commercial National Security Algorithm (CNSA) Suite"
# Other WPA3 Enhancements
- Wi-Fi Enhanced Open
	- Utilizes Opportunistic Wireless Encryption (OWE)
	- Allows for encrypted Wi-Fi sessions over Open Networks
	- No passphrase or 802.1x required
- Wi-Fi Easy Connect
	- Simplifies the process of configuring security for devices that have limited or no display interface (think, IoT)