- Embedded instrumentation within Cisco IOS to characterize network operation
- NetFlow Generator & Collector
- When enabled on an interface it:
	- Sorts all packets into "flows"
	- Collects flow data and places it into a NetFlow Cache
	- Information can be viewed with IOS CLI or NetFlow Collector

# What is a flow?
- Source IP address
- Destination IP address
- Source & Destination Port Number
- Layer-3 Protocol Type
- ToS Byte
- Input interface
# The flow cache
- Every ingress packet is inspected to determine if it belongs to an existing flow or should be considered the first packet in a new flow
- Each new flow creates an entry in the NetFlow Cache
- NetFlow Cache can contain thousands or even millions of entries at any given time
- Once a flow expires, information about that flow is pushed to a NetFlow Collector.
# Exporting Flows
- NetFlow cache entries are exported once various timers expire or triggers occur:
	- Flow has been idle for a specified period of time.
	- Flow is classified as "long-lived" (i.e 30 minutes).
	- TCP connections in which the FIN or RST flags are seen.
	- If the cache becomes full, heuristics are applied to aggressively age groups of flows.
- Expired flows are grouped together into NetFlow Export datagrams (UDP port 2055) and sent to Collector