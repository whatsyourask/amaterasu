[[Original Netflow Theory & Configuration]]
- Version 1 in 1996 by Cisco.
- Version 2 -4  Never released
- Version 5 - Added several new fields to the flow-cache and flow-record including BGP ASN, flow sequence numbers and IP mask information
- Version 6 - no longer supported by Cisco
- Version 7 - Old version that supported Hybrid and Native IOS Switches
- Version 8 - Supports router-based Flow Aggregation
- Version 9 - Template-based, also support IPv6
# NetFlow Version 5
- Limited to IPv4
- Packet format fixed, predictable and unchanging
- Collector software is hardcoded to know what to expect in a received NetFlow packet
# NetFlow Version 9
- Current industry standard
- Supports both IPv4 and IPv6
- Template-based
	- A Template defines exactly what information you want exported to the Collector
	- You define the Template
	- Template definitions periodically sent to the Collector