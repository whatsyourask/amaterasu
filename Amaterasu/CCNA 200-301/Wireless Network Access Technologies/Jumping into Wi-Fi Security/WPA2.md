[[Wi-Fi Security Authentication]]
# The Downside of WPA
- WPA initially only supported TKIP
- Once 802.11i was formally ratified, it became clear that all of the standard's components provided stronger security than WPA.
- WPA2 was created to be fully-compliant with 802.11i security standards including:
	- Included the mandatory use of AES-CCMP encryption for WPA2 Enterprise (RC4 and TKIP no longer an option)
	- 802.1x could be used in Ad Hoc mode (rarely used)
	- Options for speeding 802.1x re-authentication were added.
# WPA & WPA2 Key Management
- TKIP
	- Temporal Key Integrity Protocol
	- Provides for dynamic rotation of encryption keys
	- Worked with RC4 encryption cipher
	- Utilized for WPA
- CCMP
	- Counter Mode with Cipher Block Chaining Message Authentication Code Protocol
	- Based off of AES encryption (much stronger than TKIP)
	- Also provided for dynamic rotation of encryption keys
	- Used with WPA2
# WPA2 Personal
- Initial passphrase set for Authentication
- WPA2 Base Key derived for encryption of data
	- Up to 256-bits
	- Derived from the static passphrase (plus other elements)
- Data is encrypted to/from access point
	- Utilized RC4 + TKIP or ...
	- CCMP-AES encryption ciphers (recommended)
# WPA2 Enterprise
- Client utilizes 802.1x to authenticate against Radius Server
- 802.1x exchange provides initial encryption key
	- Encrypted exchange of data between client and AP begins
- Encryption of data must be done my CCMP-AES (per-packet key rotation)
# WPA & WPA2 Summary
- WPA = draft of 802.11i
- WPA2 = Fully compliant with 802.11i 
- Both offer Personal and Enterprise editions:
	- Personal meant for SOHO use
	- Enterprise meant for larger-scale Wi-Fi deployments
- Many companies use WPA/WPA2 Personal (instead of Enterprise) due to easier implementation