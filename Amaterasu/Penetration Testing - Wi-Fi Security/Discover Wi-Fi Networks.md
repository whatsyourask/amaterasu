[[Wi-Fi Pentest]]
# Tools
- Kismet, airodump-ng
- InSSIDer Office
- KisMAC
## Kismet
Put your wireless interface into monitor mode:
- `kismet -c mon_interface`
Start the kismet server.
![[Снимок экрана 2023-09-22 в 15.04.41.png]]![[Снимок экрана 2023-09-22 в 15.05.42.png]]
![[Снимок экрана 2023-09-22 в 15.07.11.png]]
![[Снимок экрана 2023-09-22 в 15.07.40.png]]
Kismet uses color coding to help you identify features of listed networks (encryption type by color):
- Green - None
- Red - WEP
- O - WPA or WPA2
## Airodump-ng
- What it can:
	- Perform automatic channel switching
	- Filter captured traffic by BSSID or cipher suite
	- Determine the list of clients associated to a network and their MAC addresses
	- Provide information on signal level, network traffic, security settings
`airodump-ng <interface>`
- Airodump-ng will start to jump over the various wireless channels and display an overview of detected networks and clients as we have seen from the previous example.
- Specify channels for Airodump-ng to listen on:
	- `airodump-ng -c 1,6,11 mon0`
- Save all data to a file:
	- `airodump-ng -w <filename> <interface>`
- Capture only frames for specific target AP:
	- `airodump-ng -c <channel> -b <BSSID> mon0`
- Output only network with a specific cipher configuration:
	- `airodump-ng -t wep mon0`
	- Substitute wep with opn or wpa.
# Hidden SSID
Almost all APs have an option to cloak the SSID value they broadcast in all the beacon frames. When this option is set, the AP will simply replcce the original SSID value with a null value.
Our tools should discover these so-called hidden networks.
Many frame types transmit an SSID field in a plaintext:
- Probe requests
- Probe responses
- Association requests
- Re-association requests
> Filter beacon frames with:
> `wlan[0] == 0x80`
## Active attack to discover hidden SSID
Active attack - sending DEAUTHENTICATE messages for an active station to the station's AP. This will force the STA to rejoin the network. STA will send Probe Requests frames allowing us to intercept the Probe Responses containing the target SSID field.
With kismet:
- Monitor all APs
- Choose the channel of the target.
- Write down your victim client MAC address.
- Use aireplay-ng:
	- `aireplay-ng -0 <num> -c <client_mac> -a <BSSID> <intf>`
	- ``-0 <num>`` stands for deauth attack repeated for num times or use 0 for infinite loop.
	- `<client_mac>` is the victim client MAC address
	- `<BSSID>` is the target BSSID (the AP MAC address)
	- `<intf>` is your monitoring interface
	- ![[Снимок экрана 2023-09-22 в 15.41.13.png]]
	- Now, In Kismet window you can determine network SSID as in the next screenshot.
	- Filter with wireshark: `wlan.fc.type_subtype == 0x05`
