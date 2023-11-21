# What is Security Engineering?
- Technical position
- Develop & enforce plans, procedures
- Proactive work
- Implementing, configuring, and monitoring
- Incident Response
## Security Engineer Job
- Configure security controls
- Set up security systems/appliances
- Assist with policy creation
- Manage and tweak security controls
- Education
## The Threat Landscape
- Phishing - compromise system by downloading malware or stealing credentials
- Financial Fraud - cards theft or any payment information
- Health-care
- Unsecured public access - more misconfigured systems than threat
## Keep in mind...
- Budget - how much is this going to cost?
- Acceptable Level of Risk
- Not if, but when - no system will be a 100% secure
## Our Network
![[Screenshot 2023-11-16 at 20.14.53.png]]
## The Attack 
![[Screenshot 2023-11-16 at 20.15.52.png]]
Receives a email with a link. He clicks on it. He types his creds. 
Attacker receives creds and logs in to VPN.
MegaCorp is hacked.
# Perimeter Security
![[Screenshot 2023-11-16 at 20.21.11.png]]Network perimeter - red zone.
We have:
- Perimeter firewall
- Network equipment
- Servers
- Another firewall
## What is a Firewall?
- Monitor network traffic
- Permits or blocks based on rules
- Barrier between network segments
- First line of defense
- Different types of firewalls
	- Web App Firewall
	- Next Generation Firewall
- Important things to block to/from the internet
	- Direct RDP - a lot of attackers scan RDP ports and brute creds
	- Email direct from workstations - if a workstation became compromised, then it can send spams, for example.
	- Any additional ports/traffic that aren't necessary
## Firewall Locations
Perimeter Firewall - between internet and DMZ, first start of defense
Another firewall between DMZ and Internal network.
## DMZ
- Boundary between internal and external networks
- Network segment used for public systems
- Provides secure way to host public resources
- What is in a DMZ?
	- Public web servers
	- Edge mail servers
	- VPN termination points
	- Supporting infrastructure