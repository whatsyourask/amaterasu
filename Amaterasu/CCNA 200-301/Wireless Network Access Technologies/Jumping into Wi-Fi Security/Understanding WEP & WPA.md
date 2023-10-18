[[Wi-Fi Security Authentication]]
# Where Encryption Takes Place
- Only the Wi-Fi data gets encrypted
- The access point can be enabled to advertise various encryption standards and protocols
- Encryption and decryption happen between access point and Wi-Fi client:
	- Only Data Frames encrypted, not management frames
# WEP vs WPA
- WEP
	- Wired Equivalent Privacy
	- Early form of Wi-Fi security written into original 802.11-1997 standard
	- Utilized RC4 Encryption Cipher
	- Considered unsafe and is now deprecated
- WPA = Wi-Fi Protected Access
	- Introduced by Wi-Fi Alliance to fix Wi-Fi security problems found in WEP
	- WPA was based on a draft of IEEE 802.11i amendment
	- WPA was intended to be an intermediate measure until the full 802.11i amendment.
# WEP
- Static passphrase
	- 64 bit, 128 bit, 256 bit
	- 128 bit most common
- Data is encrypted to/from access point
	- Utilized RC4 encryption cipher
	- Easily cracked with a static passphrase
# WPA Personal
- Initial passphrase set for Authentication
- WPA base key derived for encryption of data
	- Up to 256 bits
	- Derived from the static passphrase
- Data is encrypted to/from access point
	- Utilized RC4 + TKIP encryption cipher (Temporal Key Integrity Protocol)
	- Message Integrity Check (MIC) added
	- TKIP provided a per-packet key system
# WPA Enterprise
- Client utilizes 802.1x to authenticate against Radius Server
- 802.1x exchange provides initial encryption key
	- Encrypted exchange of data between client and AP begins
- Encryption of data done by RC4 + TKIP (per-packet key rotation)