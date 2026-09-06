# TCP Flags


## Overview
TCP flags are control bits in the TCP header that indicate connection state, control how a segment is handled, and support specific TCP functions.


---

## Core TCP Flags 
- **SYN (Synchronize):** Initiates a connection between two hosts and synchronizes sequence numbers *(i.e. to tell the other end which sequence number they should accept)* to initiate a connection during the 3-way handshake. Only the first packet from sender as well as receiver should have this flag set.

- **ACK (Acknowledgment):** Confirms the successful receipt of packets and indicates the next expected sequence number. The flag is set if the acknowledgement number field contains a valid acknowledgement number. e.g The receiver sends an ACK = 1 as well as SYN = 1 in the second step of connection establishment to tell sender that it received its initial packet. 

- **FIN (Finish):** It is used to request for connection termination i.e. when there is no more data from the sender, it requests for connection termination. This is the last packet sent by sender. It frees the reserved resources and gracefully terminate the connection. 

- **RST (Reset):** Abruptly terminates and resets a connection due to an unexpected packet or closed port error. It is used to terminate the connection if the RST sender feels something is wrong with the TCP connection or that the conversation should not exist. It can get send from receiver side when packet is send to particular host that was not expecting it. 

- **PSH (Push):** Tells the receiver to pass data immediately to the application layer instead of buffering it. It is used to request immediate data delivery to the receiving host, without waiting for additional data to be buffered on the sender's side. This flag is commonly used in applications such as real-time audio or video streaming.

- **URG (Urgent):** Marks specific data as high priority so it is processed immediately using the urgent pointer. It is used to indicate that the data contained in the packet should be prioritized and handled urgently by the receiver. This flag is used in combination with the Urgent Pointer field to identify the location of the urgent data in the packet.


## Additional Flags
- **ECE (ECN-Echo):** Used for explicit congestion notification to inform a peer of network congestion. It indicates that the network path is experiencing congestion (Explicit Congestion Notification).

- **CWR (Congestion Window Reduced):** Sent by a host to acknowledge it received an ECE congestion warning.

- **NS (Nonce Sum):** Provides extra protection against hidden or malicious manipulation of congestion signals.

---


## Important Terminologies
**- Window (WND) -** It is used to communicate the size of the receive window to the sender. The window size is the amount of data that the receiving host is capable of accepting at any given time. The sender should limit the amount of data it sends based on the size of the window advertised by the receiver.

**- Checksum (CHK) -** It is used to verify the integrity of the TCP segment during transmission. The checksum is computed over the entire segment, including the header and data fields, and is recalculated at each hop along the network path.

**- Sequence Number (SEQ) -** It is a unique number assigned to each segment by the sender to identify the order in which packets should be received by the receiver. The sequence number is used in conjunction with the acknowledgement number to ensure reliable data transfer and to prevent duplicate packets.

**- Acknowledgement Number (ACK) -** It is used to acknowledge the receipt of a TCP segment and to communicate the next expected sequence number to the sender. The acknowledgement number field contains the sequence number of the next expected segment, rather than the number of the last received segment.


---


## How Security Tools Abuse TCP Flags (Nmap Scanning)

Network scanners like Nmap exploit how the TCP/IP stack responds to unusual flag combinations to map open or closed ports:

- **SYN Scan (-sS):** The standard "stealth" scan. It sends a SYN packet. If the port responds with SYN-ACK, the port is open (the scanner immediately drops the connection with a RST to avoid completing the handshake). If it responds with RST, the port is closed.

- **Xmas Scan (-sX):** Sets the FIN, PSH, and URG flags simultaneously, lighting the packet up "like a Christmas tree." According to RFC rules, a closed port must reply with an RST, while an open port will completely ignore the packet.

- **FIN Scan (-sF):** Sends a packet with only the FIN flag set. Closed ports reply with RST; open ports ignore it.Null Scan (-sN): Sends a packet with zero flags set in the header. Just like the Xmas and FIN scans, closed ports reply with an RST, while open ports drop the packet.
