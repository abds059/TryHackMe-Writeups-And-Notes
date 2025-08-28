# Cyber Kill Chain

## Task 1 - Introduction

The cyber kill chain divides the attack into seven stages:

- **Reconnaisance** -> Attacker gathers info
- **Weoponisation** -> Attacker creates payload or modifies existing one based on vulnerabilities found
- **Delivery** -> Attacker sends payload to target
- **Exploitation** -> Payload exploits the targeted vulnerability
- **Installation** -> Payload enables attackers to open a backdoor entry to the system
- **Command and Control (C2)** -> Attacker can compromise the system through the backdoor installed
- **Actions and Objectives** -> Attacker can carry out other actions such as data exfiltration or other system's exploitation

### Answer the questions below

- How many phases comprise the Cyber Kill Chain?

    `7`

---

## Task 2 - Reconnaisance

The act of collecting information about the target’s vulnerabilities and weaknesses to discover potential entry points

### Types Of Reconnaisance

- Passive Reconnaisance
- Active Reconnaisance

#### Passive Reconnaisance

- Attacker performs recon without making noise (e.g, OSINT)
- Using WHOIS database to identify contact info, registration dates and domain owners
- Website recon by searching and scraping
- Google Dorking 

#### Active Reconnaisance

- Can get noisy as it requires interaction with the target organisation
- Using network port scanning to identify live and active hosts
- Doing physiacal recon (Visiting the premises physically to identify entry points, security measures and personal behaviour)

### Counter Measure Against Reconnaisance

#### 1. Minimising Public Info Exposure

This is achieved by:

- Limiting info on websites
- Social media accounts
- DNS records

#### 2. Monitoring and Analysing Network Traffic and Logs

A log is a must to detect vulnerability and network port scans

The security team must:

- Monitor network traffic actively to detect scanning traffic
- Check service logs to uncover reconnaisance attempt

### Answer the questions below

- What is the term for using search engines to reveal sensitive information and confidential files?

    `Google Dorking`

- What type of reconnaissance is it where the attacker checks the social media pages?

    `Passive Reconnaisance`

## Task 3 - Weoponisation

The goal of this phase to create a payload to exploit the vulnerabilites discovered. The attacker might create a new one or modify an existing payload.

The attacker also use obfuscation or encryption to evade detection. Aslo they can hide the payload inside a harmless-looking file such as PDF or Word file.

### Weoponisation : Examples

- An attacker can weoponise anything from an email attachment to a USB
- They can also use an exploit kit
- Attacker mostly exploit macros in word documents

Once the file is ready the attacker might:

- Craft a phishing email
- Set up a webpage to host it
- Save it on a USB drive

> An exploit kit is an automated platform containing various exploits for various vulnerabilities

### Counter Measures Against Weoponisation

#### 1. User Training

A user must be trained enough to be able to inspect email source code for legitimacy if the email contains any attachments

#### 2. Disabling 

It is reaonable to:

- Disable any unncessary features
- Uninstall unnecessary software
- Remove unnecessary browser plugins
- Restrict risky feature like disabling macros in office documents or limiting them to trusted sources only

### Answer the questions below

- What technique is mentioned to evade detection by making it challenging to analyse the malicious code?

    `Obfuscation`

- What built-in feature makes creating a malicious MS Office document possible?

    `Macro`

---

## Task 4 - Delivery

After a payload is crafted an attacker need a way to deliver it 

### Delivery Examples

- #### Phishing Emails

    These mails may contain malicious documents as well as links to malicious downloads

    Here file naming plays an important role as `invoice.pdf.exe` looks more likely to be clicked than `invoice.exe`

- #### Spear Phishing Emails

    Refers to targeted attack by impersonating a trusted source 

    For e.g, the sender's anme and address are spoofed to appear as receipent's manager

- #### Malicious Web Links

    Attackers might host the exploit kits on public websites

    Domain spoofing and URL shortening can be used to make the links appear less suspicious

- #### File Sharing Platforms

    Attackers might just upload the file on file-sharing services exploting the people trust on these platforms

- #### Malvertising

    The attacker might dispay adds on legitimate websites to redirect users to mailcious webpages

- #### SMS Phishing (Smishing)

    The attacker sends text messages with malicious links or instructions to download malware

- #### Social Engineering

    The attacker might convince a user to download and run a malicious program

- #### Physical Means Of Delivery

    The malicious code might be saved on a USB flash memory and left in an accessible location
    
    Another example is mailing an innocuous-looking DVD containing the malicious program. The attacker must always provide a convincing context, such as the DVD containing a catalogue of interest to the target personnel.

### Countermeasures Against Delivery 

#### 1. User Training

The users should be educated about safe browsing, phishing and social engineering attacks

#### 2. Email and Web filtering

Email and web filtering are the standard in many organisations. Web application firewalls (WAFs) are indispensable in blocking malicious files available via the web.

#### 3. Network Monitoring and Patch Management

This is also increaingly vital in large corporations

### Answer the questions below

- What method involves showing advertisements on legitimate websites to redirect users to malicious pages?

    `Malvertising`

- What phishing attack sends text messages with malicious links or instructions to download malware?

    `Smishing`

---

## Task 5 - Exploitation

Following the successful delivery of the malicious payload comes the exploitation stage

### Exploitation Examples

- #### Targeting Password-Based Authentication

    The most starightforward methos is targeting a password-based authentication system

    If the password is weak the attacker might gain easy access

    Alternatively the attcker may also use social engineering to get the user password

- #### Software Vulnerabililties

    Software vulnerabilities open another door for attackers

    Vulnerabilites can be exploited on both clients systems and network services

    If the attacker discovers a vulnerability and exploits it before the vendor is even aware of the vulnerability, then its called a `zero-day exploit`

### Counter Measure Against Exploitation

- #### Password Requirement and MFA

    Stricter password policies should be enforced
    
    MFA should be enabled to prevent access even with valid password credentials

- #### Patch Management and Vulnerability Scanning

    Patch mangements for servers and clients is necessary to eradicate known vulnerabilities

    Vulnerability scanning is also necessary to discover any unpatched vulnerabilities

- #### Intrusion Prevention Systems (IPSs)

    IPSs can play a significant role in blocking various exploitation attempts by inspecting the traffic for known exploits
    
    Web Application Firewalls (WAFs) can block a variety of attacks against web applications, such as:
    
    - SQL injection
    - Cross-site scripting (XSS)
    - Cross-site request forgery (CSRF)

### Answer the questions below

- What type of exploit is used before the vendor becomes aware of a vulnerability?

    `Zero-day Exploit`

- What technology is mentioned to prevent an attacker from gaining access even with valid login credentials?

    `MFA`

---

## Task 6 - Installation

After the successful exploitation the main goal of attacker is to maintain persistent access to the system so he/she can return to system later without exploitation phase again

### Installation Examples

- #### Scheduled Tasks or Cron Jobs

    To maintain persistent access, sometimes creating scheduled tasks in MS Windows or setting a cron job in Linux systems is necessary
    
    Other times, they need to modify startup scripts or configuration files. 
    
- #### New Service or Daemon

    Installing a new service in Windows or a daemon in Linux systems is one possible approach. The service approach makes it easier to maintain persistent access

- #### Malware, Backdoors and Rootkits

    Attackers might resort to installing malware, creating backdoors, or installing rootkits
    
    Furthermore, they try to take advantage of the system’s built-in functions, such as legitimate Windows tools and binaries, also known as living-off-the-land binaries (LOLBins)

- #### Webshell 

    Attackers might deploy a web shell after exploiting a web application. 
    
    A web shell is a small script written in a programming language that is supported by the exploited server; it allows the attacker to execute operating system commands on the target via a web browser interface. 
    
    Running a web shell over a standard protocol such as HTTPS will ensure they can log in to their target system while camouflaging their activity within HTTPS traffic.

### Counter Measures Against Installation

- #### End point Detection and Response (EDR)

    EDR allows monitoring endpoints for suspicious activities, such as unusual process creation, file modification in sensitive directories, and unexpected network connections

- #### Regular Audits

    Regular system audits and comparisons against a secure baseline are necessary to identify unauthorised changes, such as newly created user accounts or newly installed services, and revert them to their original state
    
- #### Application Allowlisting

    This prevents the execution of unauthorised or malicious software by allowing only approved applications to run

### Answer the questions below

- What tactic allows attackers to execute operating system commands on a target via a web browser interface?

    `web shell`

- What technique is mentioned to prevent the execution of unauthorised or malicious software by only allowing approved applications to run?

    `allowlisting`

---

#### Note:

> In Linux, a daemon is a long-running background process that operates without direct user interaction or a controlling terminal. Daemons are designed to provide services, manage system resources, or perform scheduled tasks (http, ssh, syslog, Network Manager serices)

---

## Task 7 - Command and Control (C2)

After successful installation of persistent access the attacker establishes a covert communication channel between attacker and victim system through Command and Control phase

### Command and Control Examples

- #### Common Application Layer Protocols

    C2 often uses common protocols such as HTTP, HTTPS, DNS, and SMTP to blend in with legitimate traffic

    Encrypted channels like HTTPS are used to hide from network monitoring tools

    DNS tunneling may also be employed, where data is encoded in DNS requests to bypass firewalls and security solutions

- #### Social Media and Cloud Services

    Attackers may leverage social media platforms (e.g., X direct messages) to send commands to botnets

    Legitimate cloud services such as Dropbox or Google Docs are also used, particularly for data exfiltration

- #### Domain Generation Algorithms (DGAs)

    DGAs generate thousands of domain names (e.g., 50,000) using a predefined algorithm

    The C2 registers only a small percentage (1–2%), and malware iterates through the list until it finds an active one

    If a domain is blocked or seized, the malware switches to the next available domain

- #### Fast Flux

    Fast Flux maps hundreds or thousands of IP addresses to a single domain name

    These IP addresses are swapped every few minutes to evade detection

    Compromised devices (like IoT devices) serve as proxies, forwarding traffic to the hidden C2 server

If one proxy IP is blocked, the malware seamlessly switches to another

### Counter Measures Against Command and Control

- #### Network Monitoring

    Firewalls, IDS (Intrusion Detection Systems), and IPS (Intrusion Prevention Systems) are crucial for detecting and blocking C2 traffic

    Security teams monitor unusual traffic patterns, unexpected volumes, and connections to known malicious IPs

- #### DNS Traffic Analysis

    Since DNS can be abused for tunneling, monitoring DNS traffic is critical

    Indicators include unusually long DNS queries or high volumes of requests to suspicious domains

- #### Web Traffic Inspection

    Because attackers often use HTTP/HTTPS for C2 communication, monitoring web traffic can reveal suspicious activity

    Techniques like content filtering help block malicious URLs, while encryption inspection enables detection of hidden C2 traffic within HTTPS

- #### Honeypots

    Deploying honeypots provides a way to detect and study C2 communication attempts

    Honeypots also allow defenders to observe attacker behaviour in a controlled environment


### Answer the questions below

- What is the name of the tactic where data is hidden within DNS queries?

    `DNS Tunnelling`

- What protocol would the attacker use to smuggle his data as encrypted web traffic?

    `HTTPS`

---

## Task 8 - Actions On Objectives

After establishing a covert C2 communication channel, the attacker can carry out their original goals. In this phase, the attacker executes their original goals, which range from data exfiltration (information theft) to service disruption.

### Actions on Objectives Examples

- #### Destructive Attacks

    Some attackers aim purely to cause damage

    This may include deleting or corrupting data to disrupt normal system operations

- #### Financial Gain

    Attackers often seek quick riches through ransomware attacks

    Others may pursue financial theft via unauthorised wire transfers or fraudulent transactions, which is a more discreet approach

- #### Lateral Movement

    Attackers may try to compromise other systems on the same network

    This stealthy expansion gives them deeper access within the target environment

- #### Industrial Control Systems (ICS) Manipulation

    In critical infrastructure, attackers may target ICS systems

    These attacks can cause severe disruption in industrial or utility operations

- #### Long-Term Persistent Presence

    Some attackers focus on maintaining a stealthy, long-term foothold in the system

    This persistence allows them to launch attacks at a time of their choosing

### Counter Measures Against Actions on Objectives

- #### Data Loss Prevention (DLP)

    DLP solutions help prevent unauthorised data exfiltration

    They monitor and control the flow of sensitive data, reducing the risk of leaks

- #### Backup and Recovery

    A reliable backup and recovery plan is essential to mitigate ransomware and destructive attacks

    Regular backups ensure data can be restored, minimising downtime and loss

- #### Network Segmentation and Access Controls

    Network segmentation isolates critical systems, preventing attackers from moving laterally if one system is compromised

    Strict access controls and the principle of least privilege ensure only authorised users can access sensitive data and systems

- #### User Activity Monitoring

    Monitoring user activity helps detect suspicious behaviours

    Example: detecting unusual DNS queries at odd hours (e.g., midnight)

- #### Endpoint Detection and Response (EDR)

    EDR solutions monitor and detect suspicious activity on endpoints.

    They can identify behaviours such as:

    - Processes accessing or modifying sensitive data

    - Attempts to encrypt files

    - Establishing unauthorised network connections

### Answer the questions below

- What is the term for stealing sensitive files from a target network?

    `Data Exfiltration`

- What principle limits who can access sensitive systems and data to minimise damage caused by an attacker?

    `Principle of Least Privilege`

- What type of attack involves encrypting files and demanding payment in exchange for the decryption key?

    `Ransomware`

---

## Task 9 - Conclusion

### Answer the questions below

- What is the flag after you complete the static site?

    `THM{CKC_NJHERDX327}`

