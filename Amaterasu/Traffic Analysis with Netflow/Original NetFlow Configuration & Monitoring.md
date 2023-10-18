[[Original Netflow Theory & Configuration]]
- Enable Cisco Express Forwarding
	- Device(config)# ip cef
- Enable NetFlow on one-or-more interfaces
	- ip flow <ingress|egress>
- Modify timeout values for flows (optional)
	- ip flow-cache timeout <active|inactive> <1-60>
# Viewing the flow cache
- Show ip flow interface
	- Verifies that NetFlow has been configured
- Show ip cache flow
	- Provides flow statistics and summarized flow information
- Show ip cache verbose flow