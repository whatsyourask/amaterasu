# Rogue AP
Force clients to connect to your AP.
Then you can perform different types of attacks:
- MiTM
- ARP poisoning
- traffic sniffing
- browser vulnerabilities
Tool: 
- airbase-ng
## Features of airbase-ng
- implementations of the Caffe-Latte and Hirte attacks
- Act as ad-hoc or infrastructure AP
- Encrypt and decrypt traffic
- Can capture WPA/WPA2 handshakes
- Packets manipulation with external commands
- Filtering by BSSIDS or client MAC
## Typical Rogue AP scenario
Sam is an employee at ACME Corporation. 
At his workspace, they have a Wi-Fi network called ACMEWiFi that uses WEP encryption with Shared Key Authentication.
He also has some backlog so he boots up his notebook to continue working on ACME's matters while sitting at the bar table.
His notebook starts to probe the neighborhood area in order to discover available networks.

Here is where our attack starts. If we could set up a fake Ap that spoofs the ACMEWiFi SSID name and advertise itself as a WEP network, we could trick Sam's notebook into starting an authentication/association message exchange with our machine.

You will be able to recover a good amount of keystream which is more than what you need to forge your own ARP requests with packetforge-ng.

## Recover PRGA with a Rogue AP
Start you wireless adapter in monitor mode:
`airmon-ng start <interface>`
Dump the incoming keystream to a file for later reuse:
`airodump-ng -c <channel> -w <outfile> <interface>`
Launch an airbase-ng:
`airbase-ng -c <channel> -e <SSID> -s -W 1 <interface>`
-s - SKA authentication
-W 1 instructs airbase-ng to set the WEP bit in the beacons as some clients can get confused otherwise.![[Screenshot 2023-09-28 at 19.39.00.png]]
![[Screenshot 2023-09-28 at 19.39.34.png]]
Save all captured information with airbase-ng:
`airbase-ng -c <channel> -e <SSID> -s -W 1 -F <file> <interface>`
## Initiate a WPA/WPA2 handshake
Configure your client on using WPA2 and give it a password.
Launch airbase-ng:
`airbase-ng -c <channel> -e <SSID> -W 1 -Z 4 <interface>`
-Z - is used to specify WPA2 options while 4 stands for CCMP encryption scheme 
The victim client is tricked into connecting to the spoofed AP and we are able to collect the WPA2 handshake:
![[Screenshot 2023-09-28 at 19.43.59.png]]
![[Screenshot 2023-09-28 at 19.45.32.png]]
Try to crack the captured handshake by passing aircrack-ng the configured password through a shell pipe:
![[Screenshot 2023-09-28 at 19.47.38.png]]
We were able to get a real handshake without actually knowing the network PSK.
The AP sends an ANonce to the authenticating client which in turn sends its SNonce plus the MIC. Now the AP has all of the needed info to actually try to crack the PSK; the subsequent steps in the handshake are not even needed.

## Man in the Middle attack
Lab setup:
- unassociated victim client from any AP
- the attacker machine should be connected to the Internet through a wired interface
We need an internet access on our machine in order to perform MitM attack that require packet forwarding towards the real destination in order to sniff the resulting traffic!

Basic setup:
- Set up a fake AP
- Start a DHCP server to provide the network configuration to connecting clients.
- Forward all the traffic toward the internet but..
- act as MitM eavesdropping all the communications

Put your interface into monitor mode:
`airmon-ng start <interface>`
Start an AP with a catchy SSID:
`airbase-ng -c <channel> -e "Free Internet" <interface>`
![[Screenshot 2023-09-28 at 20.10.47.png]]
Create a new bridge interface:
`brctl addbr br0`
`brctl addif br0 eth0`
`brctl addif br0 at0`
br0 - the name of the bridge interface we are creating, eth0 is your wired interface and at0 is the virtual interface created by airbase-ng.
`apt install bridge-utils`
Assign an Ip address to bridge:
`ifconfig br0 <ip_address> up`
Enable IP packet forwarding:
`echo 1 > /proc/sys/net/ipv4/ip_forward`
try to connect through the victim:
![[Screenshot 2023-09-28 at 20.13.04.png]]
Fire up a sniffing tool and start listening on your virtual wireless interface:
`tcpdump -nvi <interface> tcp port 80 -A`
## Rogue AP: an alternative definition
Rogue AP - it is unmanaged and unauthorized wireless AP attached to an enterprise wireless network.
# Rogue AP: Evil Twin Attack
A typical flow for an Evil-Twin attack is as follows:
- Replicate a known Access Point ESSID via creation of an access point with hostapd
- Deauthenticate a station that is associated to the real AP.
- Station reconnects to the Evil Twin AP.
- The user, upon launching a browser is presented with a web page over HTTP requesting SSID for an Important Firmware Upgrade
- We receive the SSID in plain-text via the HTTP page.
An "all-in-one" toolkit we can use to conduct this type of attack is known as Mana.
Mana allows us to quickly spin up a rogue access point, configure the necessary DHCP settings, and with some modifications to the default configuration, we can host our own web page to be server to a connected station.

It should be noted that in order for this attack to be successful, the attacker AP should be in close proximity to a station already connected to the legitimate AP. 
This way, upon de-authentication of the client, the client should auto-reconnect to the AP with the stronger signal.

# Attacks against WPA2-Enterprise
WPA2-Enterprise introduced several improvements to the WPA2-PSK model in regards to security, primarily with the support of 802.1x authentication. Therefore, requires its own set of tools and changes to the way we traditionally perform attacks against wireless networks.

In the traditional WPA2-PSK model, we typically have a client (supplicant) that connects to an access point (authenticator) the usual two-party scenario.

With WPA2-Enterprise we introduce a third-party Authentication server, which is usually a system that supports the Radius and Extensible Authentication (EAP) protocols.

A typical authentication progression for WPA2-Enterprise is as follows.

**Initialization**: on detection of a new supplicant, the port on the switch is enabled and set to the unauthorized state. In this state, only 802.1x traffic is allowed; other traffic such as the IP is dropped.

**Initiation**: to initiate authentication the authenticator will periodically transmit EAP-Request identity frames to a special Layer 2 address (01:80:C2:00:00:03) on the local network segment. The supplicant listens on this address, and on receipt of the EAP-Request Identity frame it responds with an EAP-Response Identity frame containing an identifier for the supplicant such as a User ID. The Authenticator then encapsulates this identity response in a RADIUS Access-Request packet and forwards it on to the authentication server. The supplicant may also initiate or restart authentication by sending an EAPOL-start frame to the authenticator, which will then reply with an EAP-Request Identity frame.

**Negotiation**: (technically EAP negotiation) The authentication server sends a reply (encapsulated in a RADIUS Access-Challenge packet) to the authenticator, containing an EAP Request specifying the EAP method (the type of EAP based authentication it wishes the supplicant to perform). The authenticator encapsulates the EAP Request in an EAPOL frame and transmits it to the supplicant. At this point the supplicant can start using the requested EAP method or do an NAK(Negative Acknowledgement) and respond with the EAP methods it is willing to perform.

**Authentication**: if the authentication server and supplicant agree on an EAP method, EAP requests and Responses are sent between the supplicant and the authentication server (translated by the authenticator) until the authentication server responds with either an EAP-Success message (encapsulated in a RADIUS Access-Accept packet), or an EAP-Failure message (encapsulated inaa RADIUS Access-Reject packet). If authentication is successful, the authenticator sets the port to the "authorized" state and normal traffic is allowed; if it is unsuccessful, the port remains in the "unauthorized" state. When the supplicant logs off, it sends an EAPOL-logoff message to the authenticator, the authenticator then sets the port to the "unauthorized" state, once again blocking all non-EAP traffic.

The main tool for this attacks - **eaphammer**.

# Wardriving
Wardriving is an act of searching for Wi-Fi Networks by a person on a moving vehicle (like a car) using a portable computer, a smartphone or any other Wi-Fi enabled device.
The main objective of wardriving is creating a map of Wi-Fi Access Points in a specific area.
The map can then be used to observe AP distribution and characteristics like SSID names or encryption type.