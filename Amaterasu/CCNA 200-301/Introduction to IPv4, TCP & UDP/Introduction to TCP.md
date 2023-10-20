UDP:
- Connectionless
- Expects to receive small, discrete messages from upper-layer protocols
- no reliability or retransmission
TCP:
- Connection-Oriented
- Can work with any format of data
- Reliability with Acknowledgements and Retransmissions 
# TCP Header Fields
**TCP SRC port 
TCP DST port** 
**Sequence Number** - confirmation that you actually received your data
**Acknowledgement** -Number - 
**Hdr Length** - 
**Resvd** - 
**Control flags**:
- SYN flag to synchronize 
- ACK flag to 
**Window Size** - 
**TCP checksum** - 
**Urgent Pointer** - process urgently the data
**TCP options** - 
**Payload** - 
# TCP 3-Way Handshake
- Starts the process of Opening a TCP connection
- Initial contact and proof-of-existence
- Sequence Numbe Synchronization
**TCB** - block of memory space allocated by CPU to maintain state-information for a single TCP session.
Creation of TCBs can happen in one-of-two ways:
- Active Opens
- Passive Opens
## TCP Active Opens
TCP Clients:
- Laptop
- Smartphone
- TCB on client is not created until an application requests the service of TCP
Client pre-determines elements required for TCP socket
TCB created utilizing socket information, Initial Sequence Number (ISN), etc. TCP "SYN" transmitted
## TCP Passive Opens
TCP Servers:
- Typically designed to only recognize certain TCP applications
- TCB created "in advance" to allow capability of listening for any incoming requests
Server pre-determines elements required for TCP socket
TCB created implementing partial socket information, ISN, etc
TCB "passively" waits for any incoming requests
![[Screenshot 2023-10-19 at 23.57.37.png]]