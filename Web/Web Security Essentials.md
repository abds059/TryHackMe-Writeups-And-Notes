# Web Security Essentials

## Task 1 - Introduction

### Learning Objectives

- Understand the shift from desktop applications to web applications
- Learn why web applications are common targets for attackers
- Explore web infrastructure and the tools used to protect it
- Practice application of security measures to harden a new web application

---

## Task2 - Why Web ?

The shift from desktop to web-based applications has been ongoing for decades

- In the 1990s, desktop applications were the norm because of speed and connectivity limitations
- As web technology advanced, the 2000s gave way to much more widely used dynamic web applications for email, social media, and banking
- In the 2010s, there was a massive rise in cloud computing and software as a service (SaaS), and today, nearly everything can be done in a browser

### From a Security Perspective

The shift to web apps brought amazing advantages related to speed, compatibilty and availability and reduced storage on user end

However, these benefits came with their tradeoff in terms of security. The more powerful and widespread the web became the more oppurtunities it brought for attackers.

Web applications are among the most common points for attacks because:

- They are always available and exposed
- Often connect to bacend systems like databases and other infrastructure, offering high high-impact oppurtunities
- A vulnerable web applictaion is often the first stage in a larger attack sequence

### Risk Faced By Owner and User

| **As a Web App Owner** | **As a Web App User** |
|-------------------------|------------------------|
| Your web application is always online and must be secured 24/7 | Your data is stored in a web application, potentially insecurely |
| Anyone around the world can access your app at any time | Once your browser is breached, all of your accounts are at risk |
| It is challenging to stay up to date with so many emerging threats | A breach can result in identity theft or financial loss |
| You have the responsibility of securing your users' data | Your privacy can be permanently compromised |


### Real World Examples

#### Equifax Breach - 2017

In 2017, Equifax's sensitive customer data of nearly 150 million Americans was compromised due to an Apache vulnerability

By abusing this vulnerability, the attackers were able to access internal databases storing valuable customer data

#### Capital One Breach - 2010

Capital One faced a similar-scale breach in 2019, in which a misconfigured web application firewall (WAF) exposed over 100 million customers' sensitive personal and financial information

This misconfiguration allowed internal access to the company's cloud infrastructure and databases

### Answer the questions below

- Have applications shifted from desktop to web over the past couple of decades (Yea/Nay)?

    `Yea`

- Who is ultimately responsible for ensuring the security of users' data within a web application?

    `Web App Owner`

---

## Task 3 - Web Infrastucture

When we visit a website:

- Our browser sends a request to the web server
- The server processes the request, verifies access and returns response to the user
- This response can be a webpage, an image or data like search results on your account information

This request-response cycle is the foundation of how the web functions

### Components Of A Web Service

Any web service requires the following three components to function:

- #### Application

    The code, images, styles and icons that dictate how the website looks and functions

- #### Web Server

    This component host the web application. It listens for requests and returns a response to the user.

- #### Host Machine

    The underlying operating system, Linux or Windows, that runs the web servr and application

### Web Servers

As discussed above our browser send a request to the web server and web server returns an appropriate response. Web servers are positioned in front of websites and applications, making them a crucial aspect of the internet's foundation.

Because the web servers are publicly exposed and handle all incoming web requests, web servers are a common target for attackers.

#### Most Common Web Servers

- #### Apache 

    Most commonly used to host simple websites and blogs
    
    Example: **Wordpress**

- #### Nginx 

    An industry standard for high-performance web apps
    
    Example: **Netflix, Airbnb and Github**

- #### Internet Information Services (IIS) 

    A microsoft developed web server commonly used in enterprise environments

### Answer the questions below

- What does your web browser send to a server to receive a web page?

    `Request`

- What web server is most commonly used to host WordPress websites?

    `Apache`

- What do we call the OS and environment that runs the web server and application?

    `Host Machine`

---

## Task 4 - Protecting The Web

### Best Practices

Various security measures are available for securing websites and web applications. Some solutiosn provide visibility while others can actively stop or limit an attack, known as mitigation.

Some of the protection available for application, web servers and host machine are discussed below:

### Protecting The Application

- #### Secure Coding

    Avoiding insecure functions, ensuring proper error handling and removing sensitive information

- #### Input Validation and Sanitization

    Validating and santizing user input to prevent injection attacks

- #### Access Control

    Restrict access based on user roles

### Protecting The Web Server

- #### Logging

    Keeping a detail record of all web requests with access logs

- #### Web Application Firewall (WAF)

    Filtering and blocking harmful traffic based on defined rules

- #### Content Delivery Network (CDN)

    Reducing direct exposure tou server and using integrated WAFs

### Protecting The Host Machine

- #### Least Privilege

    Granting users lowest privilege required to perform tasks

- #### System Hardening

    Disabling unnecessary service and closing unused ports

- #### Antivirus

    Adding endpoint-level protection to block malwares

### Security Tips For All Three Components

- #### Strong Authentication

    Controlling access to who can access code, admin panels and host machines 

- #### Patch Management

    Ensuring app dependencies, web server and host machine are up to date

### Logging

Web servers create logs for every requests they receive. These logs are called access logs and are incredibly valuable from security perspective because they track every interaction with the server including:

- Client's IP address
- Timestamp
- Requested page or data
- Response status from the server
- User agent

### Answer the questions below

- What cyber security concept involves stopping or limiting damage from threats?

    `Mitigation`

- What security control involves ensuring all software and components are up to date?

    `Patch Management`

---

## Task 5 - Defense Systems

### Content Delivery Network (CDN)

A CDN store and serve cached conetct from servers closer to the user to reduce latency

Beside speed CDNs also act as a buffer between users and the origin server

### Security Benefit

- #### IP Masking

    Hides the origin server IP address, which makes it harder for attackers to target

- #### DDoS Protection

    CDNs can absorb a large amount of traffic, making denial-of-service attacks less effective

- #### Enforced HTTPS

    Encrypted communication via TLS is enforced by default by most CDNs

- #### Integrated WAF

    Many CDNs, including Cloudflare CDN, Amazon CloudFront & Azure Front Door, integrate web application firewalls

In essence, CDNs allow web apps to deliver data to customers more efficiently and securely

### Web Application Firewall (WAF)

WAFs are a powerful tool that can be integrated as another layer of protection for websites and web applications

They inspect incoming HTTP traffic and block or log potentially harmful requests based on security rules

Some of the WAFs avaialable are:

- #### Cloud-based (Reverse Proxy)

    Sits in front of the web server. 
    These WAFs are easy to deploy and have great scalability.

-  #### Host-based: 

    Software deployed directly on the web server and offers control for each application.

- #### Network-based: 

    A physical or virtual appliance situated on the network perimeter. More suited for enterprise environments.

#### Functionality

WAFs inspect HTTP requests to detect anomalies, attacks, or known suspicious patterns. Below are some of the methods used, along with examples of requests that may be blocked.

| WAF Feature                  | Detection Method                                 | Example                                                                 |
|-------------------------------|--------------------------------------------------|-------------------------------------------------------------------------|
| Signature-Based Detection     | Matches known attack patterns or payloads        | A request with a User-Agent that matches a known tool, `sqlmap/1.8.1`   |
| Heuristic-Based Detection     | Analyzes the context and behavior of requests    | A long query string with special characters `search?q=%3Cscript%20(1)`  |
| Anomaly & Behavioral Analysis | Flags deviations from normal traffic behavior    | A single IP address makes repeated login attempts in a short period     |
| Location & IP Reputation Filtering | Uses location and threat intel to block IPs | A request from an IP address that is outside of your normal business area |

### Antivirus (AV)

AVs are primarily made to safeguard endpoints, such as desktops, laptops, and servers, from known malicious files and programs

Most AVs rely on signature-based detection, which means they compare files with a database of known malware or patterns

While web attacks usually target the application layer, not the host machine, AVs still play an important role in host protection

They can help detect malicious file uploads, such as web shells, post-exploitation tools, and other malicious software

AVs are just one layer in a broader defense-in-depth strategy and should be combined with other security measures to provide stronger protection

### Answer the questions below

- Which type of Web Application Firewall operates by running on the same system as the application itself?

    `Host-Based`

- Which common WAF detection technique works by matching incoming requests against known malicious patterns?

    `Signature-Based`

## Task 6 - Practice Scenario

### Answer the questions below

- What flag did you receive for securing the Web Application?

    `THM{web_app_secured!}`

- What flag did you receive for securing the Web Server?

    `THM{server_security_expert!}`

- What flag did you receive for securing the Host Machine?

    `THM{the_final_security_layer!}`

