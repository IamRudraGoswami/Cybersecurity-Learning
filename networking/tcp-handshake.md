# TCP Handshake (Three-Way Handshake)

TCP (Transmission Control Protocol) is a connection-oriented protocol, which means a connection must be established before any data is exchanged. This connection setup process is known as the **TCP three-way handshake** and is a fundamental concept in networking and cybersecurity.

Understanding how the handshake works helps security analysts identify normal traffic, suspicious connection attempts, and common network-based attacks.

---

## How TCP Connection Is Established

When a client wants to communicate with a server using TCP, the following steps occur:

1. **SYN (Synchronize)**
   - The client sends a SYN packet to the server.
   - This packet requests to start a connection and includes an initial sequence number.

2. **SYN-ACK (Synchronize Acknowledge)**
   - The server responds with a SYN-ACK packet.
   - It acknowledges the client’s request and sends its own sequence number.

3. **ACK (Acknowledge)**
   - The client sends an ACK packet.
   - This confirms the server’s response.
   - At this point, the TCP connection is successfully established.

Only after these three steps can data transmission begin.

---

## Practical Traffic Analysis

To understand this process in real network traffic, I analyzed TCP connections using **Wireshark**.

### Steps I Followed
- Opened Wireshark and selected the active network interface
- Started packet capture
- Opened a website in the browser to generate TCP traffic
- Stopped the capture and filtered TCP packets

### Filters Used During Analysis

Show all TCP packets:

1. **Show connection initiation (SYN packets): **
   -tcp.flags.syn == 1 && tcp.flags.ack == 0

2. **Show server responses (SYN-ACK packets): **
   -tcp.flags.syn == 1 && tcp.flags.ack == 1
   
3. **Show connection confirmation (ACK packets): **
   -tcp.flags.ack == 1 && tcp.flags.syn == 0


---

## What I Observed

- Normal TCP connections always followed the SYN → SYN-ACK → ACK sequence
- Open ports responded with SYN-ACK packets
- Closed or filtered ports did not respond
- In some cases, SYN packets were sent without receiving ACK responses, resulting in incomplete (half-open) connections

These observations clearly showed how connection behavior can indicate either normal usage or suspicious activity.

---

## Security Perspective

The TCP handshake is closely related to many network attacks and defensive mechanisms.

### Common Attack Scenarios

- **Port Scanning**
  - Attackers send SYN packets to multiple ports.
  - Ports responding with SYN-ACK are identified as open.
  - This is commonly used during the reconnaissance phase.

- **SYN Flood Attack**
  - An attacker sends a large number of SYN packets but never completes the handshake.
  - The server keeps resources allocated for half-open connections.
  - This can lead to denial-of-service conditions.

---

## Detection and Defense

Suspicious indicators related to TCP handshake include:
- High volume of SYN packets without corresponding ACK packets
- Large number of half-open connections
- Repeated connection attempts from the same source IP in a short time

Defensive controls commonly used include:
- SYN cookies
- Rate limiting
- Stateful firewalls
- IDS/IPS rules to detect abnormal TCP connection patterns

---

## Key Learning

The TCP three-way handshake is a foundational networking concept with direct security implications. By analyzing handshake behavior in real traffic, security analysts can distinguish between legitimate connections and malicious activities such as scanning or denial-of-service attacks.

