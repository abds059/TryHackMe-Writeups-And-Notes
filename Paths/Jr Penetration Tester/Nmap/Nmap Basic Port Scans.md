# Nmap Basic Port Scans

## Task 1 - Introduction

### Learning Objectives

This room explains:

- TCP connect port scan
- TCP SYN port scan
- UDP port scan

---

## Task 2 - TCP and UDP Ports

In the same manner an IP address maps to a host on a network, a TCP and UDP port identifies what services are running on that host. A server provides a network service and adheres to a specific network protocol. Examples include providing time, responding to DNS queries, and serving web pages. 

A port is usually linked to a service using that specific port number. For instance, an HTTP server would bind to TCP port 80 by default; if it supports SSL/TLS, it would also listen on TCP port 443. (TCP ports 80 and 443 are the default ports for HTTP and HTTPS; however, the web server administrator might choose other port numbers if necessary.) Furthermore, no more than one service can listen on any TCP or UDP port (on the same IP address).

### Understanding Network Ports

If we oversimplify stuff, then porst can be divided into two types:

- Open ports on which a service is running
- Closed ports on which no services are running

But in reality ports can be blocked by firewall for outside traffic, so Nmap divides ports into 6 states:

- **Open** port indicating hat a service is listening on that port
- **Closed** port indicating that no service is running on that but it is accessible
- **Filtered** indicating that Nmap cannot determine its state as it is not accessible (Canbe due to firewall blocking).
- **Unfiltered** indicating that Nmap cannot determine its state but the port is accessible.
- **Open | Filtered** indicating that Nmap cannot determine whether port is open or filtered.
- **Closed | Filtered** indicating that Nmap cannot determine whether the port is closed or filtered.

### Answer the questions below

- Which service uses UDP port 53 by default?

    `DNS`

- Which service uses TCP port 22 by default?

    `SSH`

- How many port states does Nmap consider?

    `6`

- Which port state is the most interesting to discover as a pentester?

    `Open`

---

## Task 3 - TCP Flags

Nmap supports different types of port scans. To understand the difference between these port scans, we need to review the TCP header. The TCP header is the first 24 bytes of a TCP segment. 

The figure below shows what a TCP header looks like as in RFC 793. In the first row we have the source TCP port and Destination port. We can see that port number is allocated 16 bits (2 bytes). In the second and third rows we have sequence number and acknowledgement number. Each row has 32 bits (4 bytes) allocated, for a total of 6 rows making 24 bytes.

![TCP Header RFC 793](../Images/Nmap/Nmap%20Basic%20Port%20Scans/TCP%20header%20.png)

Specifically we need to focus on the flags that Nmap can set or unset. 

- **URG:** This flag marks that urgent pointer field is set. This indiactes that incoming data is urgent and should be processed immediately withour waiting for previously sent TCP segments.
- **ACK:** This indicates that acknowledgment number is significant and acknowledges the receipt of a TCP segment.
- **PSH:** Push flag asking TCP to pass data to the application promptly.
- **RST:** The reset flag is used to reset the connection. A device like firewall might send it to tear down a TCP connection. The flag is also used when data is send to a host and there is no service on the receiving end to answer.
- **SYN:** Synchronise flag is used to initiate a TCP 3 way handshake and to synchronise sequence numbers with the other host. The sequence number should be randomly set during TCP connection establishment.
- **FIN:** This indicates that sender has no more data to send.

### Answer the questions below

- What 3 letters represent the Reset flag?

    `RST`

- Which flag needs to be set when you initiate a TCP connection (first packet of TCP 3-way handshake)?

    `SYN`

---

## Task 4 - TCP Connect Scan

The TCP connect scan works by completing the TCP 3 way handshake. In standard TCP connection establishment, the client sends a TCP packet with the SYN flag set, and the server responds with SYN/ACK if the port is open; finally, the client completes the 3-way handshake by sending an ACK.

We are interested in learning whether the TCP port is open, not in establishing a TCP connection. Hence, the connection is torn as soon as its state is confirmed by sending a `RST/ACK`. You can choose to run a TCP connect scan using `-sT`.

> It is important to note that if you are not a privileged user (root or sudoer), a TCP connect scan is the only possible option to discover open TCP ports.

In the following Wireshark packet capture window, we see Nmap sending TCP packets with the SYN flag set to various ports, 5900, 22, 80, and so on. By default, Nmap will attempt to connect to the 1000 most common ports. A closed TCP port responds to a SYN packet with RST/ACK to indicate that it is not open. This pattern will repeat for all the closed ports as we attempt to initiate a TCP 3-way handshake with them.

![Wirehark TCP scan](../Images/Nmap/Nmap%20Basic%20Port%20Scans/wireshark%20TCP%204.1.png)

We notice that port 80 is open, so it replied with a SYN/ACK, and Nmap completed the 3-way handshake by sending an ACK. The figure below shows all the packets exchanged between our Nmap host and the target system’s port 80. The first three packets are the TCP 3-way handshake. Then the fourth packet tears it down with an RST/ACK.

![Wireshark TCP connection tear down](../Images/Nmap/Nmap%20Basic%20Port%20Scans/wireshark%20TCP%204.2.png)

We can the `nmap -sT <Machine-ip>` command to get a detailed list of open ports:

![nmap TCP scan](../Images/Nmap/Nmap%20Basic%20Port%20Scans/nmap%20-sT%20scan.png)

> We can use -F to enable fast mode and decrease the number of scanned ports from 1000 to 100 most common ports. The -r option can also be added to scan the ports in consecutive order instead of random order.

### Answer the questions below

- What is the state of the FTP service running on port 21?

    `open`

- What is Nmap’s guess about the service running on port 53?

    `domain`

---

## Task 5 - TCP SYN Scan

Unpriviledged users (non sudo/root users) are limited to TCP Connect scan only. However, the default mode is TCP SYN scan that doesnot complete the TCP 3 way handshake and tears down the connection once the server returns a response. 

Since, no connection is established the scan is less likely to get logged. We can select this scan type by using `-sS` option. 

The following screenshot from Wireshark shows a TCP SYN scan. The behaviour when TCP ports are closed is similar to that of a TCP connect scan.

![Wireshark TCP SYN Scan](../Images/Nmap/Nmap%20Basic%20Port%20Scans/5.1.png)

![Wireshark TCP SYN Scan](../Images/Nmap/Nmap%20Basic%20Port%20Scans/5.2.png)

To better see the difference between the two scans, consider the following screenshot. In the upper half of the following figure, we can see TCP connect scan -sT traffic. Any open TCP port will require Nmap to complete the TCP 3-way handshake before closing the connection. In the lower half of the following figure, we see how a SYN scan -sS does not need to complete the TCP 3-way handshake; instead, Nmap sends an RST packet once a SYN/ACK packet is received.

#### TCP Connect Scan

![TCP Connect Scan](../Images/Nmap/Nmap%20Basic%20Port%20Scans/5.3.png)

#### TCP SYN Scan

![TCP SYN Scan](../Images/Nmap/Nmap%20Basic%20Port%20Scans/5.4.png)

TCP SYN scan is the default scan mode when running Nmap as a privileged user (root or sudo), and it is a very reliable choice. Here we are getting blocked by the firewall when tried on local machine (kali).

![TCP SYN Scan Nmap](../Images/Nmap/Nmap%20Basic%20Port%20Scans/nmap%20-sS%20Scan.png)



### Answer the questions below

- After launching a TCP SYN scan, how many SYN-ACK packets are successfully received in AttackBox?

    `4`

- How many ports are open on the target machine?

    `4`

---

## Task 6 - UDP Scan

UDP is a connectionless protocol, so it doesnot require to complete a handshake for connection establishment. So if we send a UDP packet we cannot expect a reply. However, if the packet is send to a closed port then we will receive an `ICMP port unreacheable error (type 3, code 3)`. We can select the UDP scan by the option `-sU`.

In the Wireshark capture below, we can see that every closed port generates an ICMP destination unreachable (port unreachable) message.

![Wireshark UDP Capture](../Images/Nmap/Nmap%20Basic%20Port%20Scans/Wireshark%20UDP.png)

We can perform UDP scan for top 10 ports through Nmap by the following command: 

`nmap -sU --top-ports 10 <Machine-ip>`

![Nmap UDP Scan](../Images/Nmap/Nmap%20Basic%20Port%20Scans/nmap%20UDP%20scan.png)

### Answer the questions below

- What is the state of port number 161 over UDP in the target machine?

    `closed`

- What is the service name according to Nmap on port 161?

    `snmp`

---

## Task 7 - Fine Tuning and Performance


