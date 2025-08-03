# Nmap

## Task 1 - Deploy

### Answer the following questions below

`No answer needed`

## Task 2 - Introduction

When it comes to hacking, knowledge is power. The more knowledge you have about a target system or network, the more options you have available. This makes it imperative that proper enumeration is carried out before any exploitation attempts are made.

Say we have been given an IP (or multiple IP addresses) to perform a security audit on. Before moving foreward with anything else we need to know about which services are running on the  target. The first stage is called **port scanning**. 

### Ports

When a computer runs a service it opnes a networking construct called **port** to receive information. 

Ports are necessary for making multiple network requests or having multiple services available. 

For example, when you load several webpages at once in a web browser, the program must have some way of determining which tab is loading which web page. This is done by establishing connections to the remote webservers using different ports on your local machine.

Equally, if you want a server to be able to run more than one service (for example, perhaps you want your webserver to run both HTTP and HTTPS versions of the site), then you need some way to direct the traffic to the appropriate service. Once again, ports are the solution to this. 

### Network Connections

Network Connections are made between two ports:

- #### An Open Port Listening on the server

- #### A randomly selected port on your computer

For example, when you connect to a web page, your computer may open port 49534 to connect to the server’s port 443.

Every computer has a total of `65535` available ports; however, many of these are registered as **standard ports**.

For example, 
- HTTP Webservice can nearly always be found on port 80 of the server. 
- A HTTPS Webservice can be found on port 443. 
- Windows NETBIOS can be found on port 139 
- SMB can be found on port 445.

### Port Scanning

> If we do not know which of these ports a server has open, then we do not have a hope of successfully attacking the target; thus, it is crucial that we begin any attack with a port scan. 

Nmap can be used to perform many different kinds of port scan. Nmap connects to each port of the target in turn and depending on how the port responds, it can be determined as open, closed or filtered (usually by a firewall).

Once we know which ports are open we can look at enumaerating what srvices are running on it. 

### Answer the questions below

What networking constructs are used to direct traffic to the right application on a server?

`Ports`

How many of these are available on any network-enabled computer?

`65535`

[Research] How many of these are considered "well-known"? (These are the "standard" numbers mentioned in the task)

`1024`

## Task 3 - Nmap Switches

Nmap can be accessed by typing nmap into the terminal command line, followed by some of the "switches" (command arguments which tell a program to do different things)

The help menu of nmap can be accessed by typing `nmap -h` or `man nmap`. use any of these two commands to answer the questions below.

### Answer the questions below

What is the first switch listed in the help menu for a 'Syn Scan' (more on this later!)?

`-sS`

Which switch would you use for a "UDP scan"?

`-sU`

If you wanted to detect which operating system the target is running on, which switch would you use?

`-O`

Nmap provides a switch to detect the version of the services running on the target. What is this switch?

`-sV`

The default output provided by nmap often does not provide enough information for a pentester. How would you increase the verbosity?

`-v`

Verbosity level one is good, but verbosity level two is better! How would you set the verbosity level to two?
(Note: it's highly advisable to always use at least this option)

`-vv`

What switch would you use to save the nmap results in three major formats?

`-oA`

What switch would you use to save the nmap results in a "normal" format?

`-oN`

A very useful output format: how would you save results in a "grepable" format?

`-oG`

How would you activate this setting? (Aggressive mode)

`-A`

How would you set the timing template to level 5?

`-T5`

How would you tell nmap to only scan port 80?

`-p 80`

How would you tell nmap to scan ports 1000-1500?

`-p 1000-1500`

How would you tell nmap to scan all ports?

`-p-`

How would you activate a script from the nmap scripting library (lots more on this later!)?

`--script`

How would you activate all of the scripts in the "vuln" category?

`--script=vuln`

## Task 4 - `Scan Types` Overview

When scanning ports, there are three basic scan types:

- TCP Connect Scans (`-sT`)
- SYN "Half-open" Scans (`-sS`)
- UDP Scans (`-sU`)

Additionally some less common port scans are:

- TCP Null Scans (`-sN`)
- TCP FIN Scans (`-sF`)
- TCP XMAS Scans (`-sX`)

## Task 5 - `Scan Types` TCP Connect Scans

To understand TCP Connect scans (`-sT`), it's important that you're comfortable with the **TCP three-way handshake**. If this term is new to you then completing [Introductory Networking](https://tryhackme.com/room/introtonetworking) before continuing would be advisable.

As a recap the three way hanshake consist of the following:

-  First the connecting terminal (our attacking machine, in this instance) sends a TCP request to the target server with the SYN flag set
- The server then acknowledges this packet with a TCP response containing the SYN flag, as well as the ACK flag
- Finally, our terminal completes the handshake by sending a TCP request with the ACK flag set

A **TCP Connect scan** works by performing the three-way handshake with each target port in turn. In other words, Nmap tries to connect to each specified TCP port, and determines whether the service is open by the response it receives.

If Nmap sends a TCP request with the SYN flag set to a **closed port**, the target server will respond with a TCP packet with the `RST` (Reset) flag set. By this response, Nmap can establish that the port is closed.

If, however, the request is sent to an **open port**, the target will respond with a TCP packet with the `SYN/ACK` flags set. Nmap then marks this port as being open (and completes the handshake by sending back a TCP packet with ACK set).

**What if the port is open, but hidden behind a firewall?**

Many firewalls are configured to simply drop incoming packets. Nmap sends a TCP SYN request, and receives nothing back. This indicates that the port is being protected by a firewall and thus the port is considered to be filtered.

It is very easy to configure a firewall to respond with a RST TCP packet. For example, in IPtables for Linux, a simple version of the command would be as follows:

`iptables -I INPUT -p tcp --dport <port> -j REJECT --reject-with tcp-reset`

This can make it extremely difficult (if not impossible) to get an accurate reading of the target(s).

### Answer the questions below

Which [RFC](https://datatracker.ietf.org/doc/html/rfc9293)  defines the appropriate behaviour for the TCP protocol?

`RFC 9293`

If a port is closed, which flag should the server send back to indicate this?

`RST`

## Task 6 - `Scan Types` SYN Scans

As with TCP scans, SYN scans (`-sS`) are used to scan the TCP port-range of a target or targets; however, the two scan types work slightly differently. SYN scans are sometimes referred to as **"Half-open" scans, or "Stealth" scans**.

Where TCP scans perform a full three-way handshake with the target, SYN scans sends back a `RST` TCP packet after receiving a `SYN/ACK` from the server (this prevents the server from repeatedly trying to make the request).

### Advantages

This has a variety of advantages for us as hackers:

- It can bypass older Intrusion Detection Systems (IDS) as they are looking for a three way handshake. This is often no longer the case with modern IDS solutions; it is for this reason that SYN scans are still frequently referred to as "stealth" scans.

- SYN scan are often not logged by by applications listening on open ports, as standard practice is to log a connection once its been fully established. 

- They dont bother about completing (and disconnecting from) a three way handshake for every port, so SYN scans are generally faster than TCP scans

### Disadvantages

However, there are a couple of disadvantages to SYN scans, namely:

- They **require sudo permissions** in order to work correctly in Linux. This is because SYN scans require the ability to create raw packets (as opposed to the full TCP handshake), which is a privilege only the root user has by default.
- **Unstable services** are sometimes **brought down** by SYN scans, which could prove problematic if a client has provided a production environment for the test.

SYN scans are the default scans used by Nmap if run with sudo permissions. If run without sudo permissions, Nmap defaults to the TCP Connect scan.

When using a SYN scan to identify closed and filtered ports, the exact same rules as with a TCP Connect scan apply.

- If a port is closed then the server responds with a RST TCP packet
- If the port is filtered by a firewall then the TCP SYN packet is either dropped, or spoofed with a TCP reset

In this regard, the two scans are identical: the big difference is in how they handle open ports.

> SYN scans can also be made to work by giving Nmap the CAP_NET_RAW, CAP_NET_ADMIN and CAP_NET_BIND_SERVICE capabilities; however, this may not allow many of the NSE scripts to run properly.

### Answer the questions below

There are two other names for a SYN scan, what are they?

`Half-open, Stealth`

Can Nmap use a SYN scan without Sudo permissions (Y/N)?

`N`

## Task 7 - `Scan Types` UDP Scans

Unlike TCP, UDP connections are **"stateless"**. 

This means that, rather than initiating a connection with a back-and-forth "handshake", UDP connections rely on sending packets to a target port and essentially hoping that they make it.

This makes UDP superb for connections which rely on speed over quality (e.g. video sharing), but the lack of acknowledgement makes UDP significantly more difficult (and much slower) to scan. The switch for an Nmap UDP scan is (`-sU`)

When a packet is sent to an open UDP port, there should be **no response**. When this happens, Nmap refers to the port as being **open|filtered**. In other words, it suspects that the port is **open**, but it could be **firewalled**. 

If it **gets a UDP response** (which is very unusual), then the port is marked as **open**. 

More commonly there is no response, in which case the request is sent a second time as a double-check. If there is still no response then the port is marked open|filtered and Nmap moves on.

When a packet is sent to a **closed UDP port**, the target should **respond with an ICMP (ping) packet** containing a message that the port is unreachable. This clearly identifies **closed ports**, which Nmap marks as such and moves on.

Since, it is difficult to find if a UDP port is opened or not, UDP scans are slow. 

For this reason it's usually good practice to run an Nmap scan with `--top-ports <number>` enabled. For example, scanning with  `nmap -sU --top-ports 20 <target>`. Will scan the top 20 most commonly used UDP ports, resulting in a much more acceptable scan time.

### Answer the questions below

If a UDP port doesn't respond to an Nmap scan, what will it be marked as?

`open|filtered`

When a UDP port is closed, by convention the target should send back a "port unreachable" message. Which protocol would it use to do so?

`ICMP`

## Task 8 - `Scan Types` Null, FIN and Xmas

NULL, FIN and Xmas TCP port scans are less commonly used.

All three are interlinked and are used primarily as they tend to be even stealthier, relatively speaking, than a SYN "stealth" scan. 

Beginning with NULL scans:

- As the name suggests, NULL scans (`-sN`) are when the **TCP request is sent with no flags set at all**. As per the [RFC](https://datatracker.ietf.org/doc/html/rfc9293), the target host should respond with a `RST` if the **port is closed**

- **FIN scans** (`-sF`) work by not sending a completely empty packet but a **request is sent with the `FIN` flag** (usually used to gracefully close an active connection). Once again, Nmap expects a `RST` if the port is **closed**

-  **Xmas scans (`-sX`) send a malformed TCP packet** and expects a `RST` response for **closed ports**.

The expected response for open ports with these scans is also identical, and is very similar to that of a UDP scan. 
- If the port is open then there is no response to the malformed packet
- If a port is identified as filtered with one of these scans then it is usually because the target has responded with an ICMP unreachable packet

It's also worth noting that while [RFC 793](https://datatracker.ietf.org/doc/html/rfc9293)  793 mandates that network hosts respond to malformed packets with a RST TCP packet for closed ports, and don't respond at all for open ports; this is not always the case in practice. In particular Microsoft Windows (and a lot of Cisco network devices) are known to respond with a RST to any malformed TCP packet -- regardless of whether the port is actually open or not. This results in all ports showing up as being closed.

The goal here is, of course, **firewall evasion**. Many firewalls are configured to drop incoming TCP packets to blocked ports which have the SYN flag set (thus blocking new connection initiation requests). By sending requests which do not contain the SYN flag, we effectively bypass this kind of firewall. Whilst this is good in theory, most modern IDS solutions are savvy to these scan types, so these are not 100% effective when dealing with modern systems.

### Answer the questions below

Which of the three shown scan types uses the URG flag?

`xmas`

Why are NULL, FIN and Xmas scans generally used?

`Firewall Evasion`

Which common OS may respond to a NULL, FIN or Xmas scan with a RST for every port?

`Microsoft Windows`

## Task 9 - `Scan Types` ICMP Network Scanning

On first connection to a target network in a black box assignment, our first objective is to obtain a "map" of the network structure -- or, in other words, we want to see which IP addresses contain active hosts, and which do not.

One way to do this is to perform a `ping sweep` which sends an ICMP packet to each possible IP address for the specified network. When it receives a response, it marks the IP address that responded as being alive.

To perform a ping sweep, we use the `-sn` switch in conjunction with IP ranges which can be specified with either a hypen (-) or CIDR notation. i.e. we could scan the 192.168.0.x network using:

`nmap -sn 192.168.0.1-254`

OR

`nmap -sn 192.168.0.0/24`

The `-sn` switch tells Nmap not to scan any ports -- forcing it to rely primarily on ICMP echo packets (or ARP requests on a local network, if run with sudo or directly as the root user) to identify targets. 

In addition to the ICMP echo requests, the `-sn` switch will also cause nmap to send a TCP SYN packet to **port 443** of the target, as well as a TCP ACK (or TCP SYN if not run as root) packet to **port 80** of the target.

### Answer the questions below

How would you perform a ping sweep on the 172.16.x.x network (Netmask: 255.255.0.0) using Nmap? (CIDR notation)

`nmap -sn 172.16.0.0/16`

## Task 10 - `NSE Scripts` Overview

The **Nmap Scripting Engine (NSE)** is an incredibly powerful addition to Nmap, extending its functionality quite considerably. NSE Scripts are written in the Lua programming language, and can be used to do a variety of things: from scanning for vulnerabilities, to automating exploits for them. The NSE is particularly useful for reconnaisance.

Some useful categories are:

- `safe`: Wont affect the target
- `intrusive`: Not safe, likely to affect target
- `vuln`: Scan for vulnerabilities
- `exploit`: Attempt to exploit a vulnerability
- `auth`: Attempt to bypass authentication for running services (e.g, Log onto an FTP server anonymously)
- `brute`: Attempt to brutefroce credentials for running services
- `discovery`: Attempt to query running services for further information about the network (e.g, query an SNMP server)

More can be found [here](https://nmap.org/book/nse-usage.html)

### Answer the questions below

What language are NSE scripts written in?

`Lua`

Which category of scripts would be a very bad idea to run in a production environment?

`intrusive`

## Task 11 - `NSE Scripts` Working with the NSE

To run a specific script we use `--script=<script-name>`, e.g, `--script=http-fileupload-exploiter`

Multiple scripts can be run simultaneously by seperating them by a comma. For e.g, `--script=sub-enum-users,sub-enum-shares`

Some script require arguments. These can be given with the `--script-args`

For e.g, the `http-put` script (used to upload files using PUT method) requires two arguments:

- URL to upload the file to 
- File's location on disk

An example command would look like this:

`nmap -p 80 --script http-put --script-args http-put.url='/dav/shell.php',http-put.file='./shell.php'`

Note that the arguments are separated by commas, and connected to the corresponding script with periods (i.e.  `<script-name>.<argument>`).

A full list of scripts and their corresponding arguments (along with example use cases) can be found [here](https://nmap.org/nsedoc/).

Nmap scripts come with built-in help menus, which can be accessed using `nmap --script-help <script-name>`. This tends not to be as extensive as in the link given above, however, it can still be useful when working locally.

### Answer the questions below

What optional argument can the `ftp-anon.nse` script take?

`maxlist`

## Task 12 - `NSE Scripts` Searching For Scripts

We have two options for searching scripts:

- The first is through the [Nmap](https://nmap.org/nsedoc/) website
- The second is by searching through your local machine

Nmap stores its scripts on Linux at `/usr/share/nmap/scripts`. All of the NSE scripts are stored in this directory by default -- this is where Nmap looks for scripts when you specify them.

There are two ways to search for installed scripts:

- One is by using the `/usr/share/nmap/scripts/script.db` file. Despite the extension, this isn't actually a database so much as a formatted text file containing filenames and categories for each available script.

    Nmap uses this file to keep track of (and utilise) scripts for the scripting engine; however, we can also grep through it to look for scripts. For example: `grep "ftp" /usr/share/nmap/scripts/script.db`.

- The second way to search for scripts is quite simply to use the ls command. For example, we could get the same results as in the previous screenshot by using `ls -l /usr/share/nmap/scripts/*ftp*`.

The same techniques can also be used to search for categories of script. For example:
`grep "safe" /usr/share/nmap/scripts/script.db`

### Installing New Scripts

If a script is missing a standard `sudo apt update && sudo apt upgrade nmap` can fix it. But we can also install scripts manually from Nmap (`sudo wget -O /usr/share/nmap/scripts/<script-name>.nse https://svn.nmap.org/nmap/scripts/<script-name>`.nse). This must then be followed up with `nmap --script-updatedb`, which updates the script.db file to contain the newly downloaded script.

### Answer the questions below

Search for "smb" scripts in the `/usr/share/nmap/scripts/` directory using either of the demonstrated methods.
What is the filename of the script which determines the underlying OS of the SMB server?

`smb-os-discovery.nse`

Read through this script. What does it depend on?

Used the `cat /usr/share/nmap/scripts/smb-os-discovery.nse | grep "dependencies"` command

![nmap-grep-smb](/IMAGES/nmap-grep-smb.png)

`smb-brute`

## Task 13- Firewall Evasion

We have already seen some techniques for bypassing firewalls (think stealth scans, along with NULL, FIN and Xmas scans); however, there is another very common firewall configuration which it's imperative we know how to bypass.

Your typical Windows host will, with its default firewall, block all ICMP packets. This presents a problem: not only do we often use ping to manually establish the activity of a target, Nmap does the same thing by default. This means that Nmap will register a host with this firewall configuration as dead and not bother scanning it at all.

Fortunately Nmap provides an option for this: `-Pn`, which tells Nmap to not bother pinging the host before scanning it. This means that Nmap will always treat the target host(s) as being alive, effectively bypassing the ICMP block; however, it comes at the price of potentially taking a very long time to complete the scan (if the host really is dead then Nmap will still be checking and double checking every specified port).

> It's worth noting that if you're already directly on the local network, Nmap can also use ARP requests to determine host activity.

There are a variety of other switches which Nmap considers useful for firewall evasion. We will not go through these in detail, however, they can be found [here](https://nmap.org/book/man-bypass-firewalls-ids.html)

The following switches are of particular note:

- `-f`:- Used to fragment the packets (i.e. split them into smaller pieces) making it less likely that the packets will be detected by a firewall or IDS

-  `--mtu <number>`: Alternative of `-f` and provides more control and accepts a maximum transmission unit size to use for the packets sent which must be a multiple of 8

- `--scan-delay <time>ms`:- used to add a delay between packets sent. This is very useful if the network is unstable, but also for evading any time-based firewall/IDS triggers which may be in place.

- `--badsum`:- this is used to generate in invalid checksum for packets. Any real TCP/IP stack would drop this packet, however, firewalls may potentially respond automatically, without bothering to check the checksum of the packet. As such, this switch can be **used to determine the presence of a firewall/IDS**.

### Answer the questions below

Which simple (and frequently relied upon) protocol is often blocked, requiring the use of the -Pn switch?

`ICMP`

[Research] Which Nmap switch allows you to append an arbitrary length of random data to the end of packets?

`--data-length`

## Task 14 - Practical

Use what you've learnt to scan the target machine and answer the following questions!

### Answer the questions below

- Does the target ip respond to ICMP echo (ping) requests (Y/N)?

    `N`

    ![nmap-ICMP-scan](/IMAGES/nmap-ICMP-scan.png)

    The basic command used here is: `nmap -sN <target-ip>`

- Perform an Xmas scan on the first 999 ports of the target -- how many ports are shown to be open or filtered?

    `999`

    ![nmap-xmas-scan](/IMAGES/nmap-xmas-scan.png)

    The basic command used here is: `nmap -sX -p1-999 <target-ip>`

- There is a reason given for this -- what is it?

    Note: The answer will be in your scan results. Think carefully about which switches to use -- and read the hint before asking for help!

    `No Response`

- Perform a TCP SYN scan on the first 5000 ports of the target -- how many ports are shown to be open?

    `5`

    ![nmap-TCP-scan](/IMAGES/nmap-TCP-scan.png)

    Basic Command Syntax: `nmap -sT -p1-5000 <target-ip> -Pn`

-  Deploy the ftp-anon script against the box. Can Nmap login successfully to the FTP server on port 21? (Y/N)

    `Y`

    ![nmap-FTP-scan](/IMAGES/nmap-FTP-scan.png)

    Basic Command Syntax: `nmap --script=ftp-anon -p21 <target-ip> -Pn`

## Task 15 - Conclusion

Highly extensive official docs on Nmap are [here](https://nmap.org/book/)

### Answer the questions below

Read the conclusion

`No Answer Needed`