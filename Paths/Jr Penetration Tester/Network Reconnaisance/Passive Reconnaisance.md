# Passive Reconnaisance

## Task 1 - Introduction

Passive Reconnaisance refers to gathering information about a target without interacting with it. In contrast, active reconnaisance refers to gathering information through interaction with the target.

Passive Reconnaisance is one of the earliest, lower risk and most powerful stage in pentesting, bug bounties and threat hunting. Even in place of laws like GDPR and CCPA, large amounts of data is still exposed through DNS, WHOIS, certificate logs, search engines, and device census platforms.

### Objectives

- Use `whois` to query domain registration details.
- Use `dig` and `nslookup` to query DNS records.
- Understand why querying public WHOIS and DNS servers is considered passive.
- Discover subdomains using DNSDumpster and Certificate Transparency logs.
- Gather intelligence on exposed services using Shodan.io.

---

## Task 2 - Passive Vs Active Recon

Before computer networks existed, Sun Tzu wrote in The Art of War: 

> "If you know the enemy and know yourself, your victory will not stand in doubt."

In cybersecurity, this principle maps to two roles:
 
- As an attacker (or ethical hacker), you gather intelligence about the target to find weaknesses. 
- As a defender, you must understand what an adversary can discover about your systems from public sources and minimise that exposure.

Reconnaissance (recon) is the preliminary survey to collect information about a target. It remains the first phase in modern attack frameworks like the Unified Kill Chain (where recon helps gain initial understanding before any foothold) and variations of the classic Cyber Kill Chain. 

Reconnaissance divides into two main types:

- Passive Recon
- Active Recon

### Passive Reconnaissance

Passive Recon relies entirely on publicly available information without sending any data packets to the target.

Common passive activities include:

- Querying public DNS records from open resolvers (A, MX, TXT, etc.).
- Searching certificate transparency logs (e.g., crt.sh) for subdomains and issued certificates.
- Reviewing job postings on LinkedIn or company career pages for tech stack hints.
- Reading public news, press releases, or leaked documents on paste sites.
- Checking exposed devices via search engines like Shodan or Censys.
- Scanning public GitHub repositories for hardcoded credentials or configuration files.

### Active Reconnaissance

Active Recon requires direct engagement with the target. Our actions can be logged, detected, or blocked. 

Common active activities include:

- Sending packets to discover live hosts (e.g., ICMP pings, ARP requests).
- Port scanning or service enumeration (Nmap, masscan).
- Interacting with web applications or APIs (fuzzing endpoints, directory brute-forcing).
Social engineering attempts (phishing, vishing, pretexting phone calls).
- Physical approaches (tailgating, posing as a vendor).

Because active reconnaissance is detectable (IDS/IPS, WAFs, logging), it carries a higher risk of alerting the target. Without explicit authorisation (e.g., bug bounty scope or pentest contract), it can lead to legal issues. Passive recon is far stealthier and is often the practical first step.

### Answer the questions below

- You visit the Facebook page of the target company, hoping to get some of their employee names. What kind of reconnaissance activity is this? (A for active, P for passive)

    `P`

 
- You ping the IP address of the company webserver to check if ICMP traffic is blocked. What kind of reconnaissance activity is this? (A for active, P for passive)

    `A`
 
- You happen to meet the IT administrator of the target company at a party. You try to use social engineering to get more information about their systems and network infrastructure. What kind of reconnaissance activity is this? (A for active, P for passive)

    `A`

---

## Task 3 - Whois

WHOIS is a query/response protocol defined in RFC 3912. WHOIS servers listen on `TCP port 43` and provide registration information for domain names. The domain registrar mainatains these records for the domains they lease.

From a WHOIS response, the following details may be available (when not redacted):

- **Registrar:** The company that registered the domain (like GoDaddy, Namecheap).

- **Registrant Contact Information:** Name, organization, phone and email. However, privacy services (standard since GDPR 2018) usually replace this with "Withheld for Privacy" or similar.

- **Dates:** Creation (registration), Updation (last change) and Expiration (renewal deadline).

- **Name Servers:** The DNS servers authoritative for the domain.

- **Status Codes:** For example,`clientTransferProhibited` indicates the domain is locked against unauthorised transfers.
 
- **Abuse contacts:** The registrar's email and phone for reporting issues.

Full personal details are now rare due to privacy laws (GDPR, CCPA) and widespread use of privacy proxies. 

In practice, attackers focus on:

- dates (for estimating age or timing social engineering around renewal periods)
- the registrar (for phishing patterns)
- name servers (potential weak points), and historical changes. 

Services like whoxy.com provide historical WHOIS snapshots. Historical WHOIS data can reveal previous owners, registrar changes, or name server migrations that may indicate past compromises or infrastructure shifts.

### WHOIS Is Being Replaced By RDAP

As of 28 January 2025, ICANN officially sunsetted the traditional WHOIS protocol for generic top-level domains (gTLDs) in favour of the Registration Data Access Protocol (RDAP). 

RDAP is the modern successor: it uses HTTPS (secure), returns structured JSON (machine-readable and consistent), supports internationalisation, provides better privacy controls (differentiated access), and aligns with current data protection rules. While legacy whois clients still work (often via failover or older servers), RDAP is now the authoritative standard. Many tools and browsers redirect to RDAP automatically, and command-line access is straightforward with curl or dedicated clients like OpenRDAP.

To query WHOIS, use the whois command-line client (faster than most web tools) or online viewers for legacy queries.

**Syntax:** `WHOIS <domain name>`

![]()

### RDAP Example

Use curl to query a public RDAP endpoint (e.g., Verisign for .com domains). The jq utility formats the JSON output for readability; it is pre-installed on the AttackBox. If you are using your own system, install it via your package manager (e.g., sudo apt install jq).

![whois](../Images/Network%20Reconnaisance/Passive%20Reconnaisance/whois.png)

RDAP output is structured (easy to parse and script) and more secure. Expect to see this format increasingly in modern tooling.

#### What to look for:

- Redirection chain (Verisign to registrar server).
- Dates: useful for estimating company age or identifying renewal phishing windows.
- Name servers: potential new targets (if in scope).
Status: locked domains (e.g., clientTransferProhibited) are harder to hijack.

#### Online alternatives (if the whois command behaves unexpectedly):

- `https://whois.icann.org/` (legacy WHOIS)
- `https://lookup.icann.org/` (modern RDAP-focused lookup)
- `https://www.whoxy.com/` (historical WHOIS snapshots, free limited use)

### Answer the questions below
- When was TryHackMe.com registered?
    
    `20180705`

- What is the registrar of TryHackMe.com?

    `namecheap.com`  

- Which company is TryHackMe.com using for name servers?

    `cloudflare.com`

---

## Task4 - nslookup and dig

In the previous task, WHOIS gave us the authoritative name servers for the domain. This task moves to querying DNS records, which is still fully passive because the queries go to public or open resolvers, not to the target's servers directly.

These tools translate domain names to IP addresses, find mail servers, reveal TXT records (SPF, DMARC, verification strings), and more.

### Why prefer dig over nslookup?

`dig` (historically a backronym for "Domain Information Groper") is the modern, preferred option. It provides cleaner output, displays TTL values by default (showing how long records are cached), and is more reliable for complex queries and scripting. 

`nslookup` is covered here for compatibility, since you will encounter it in older documentation and on Windows systems, but dig should be your default tool.

### nslookup

`nslookup (Name Server Lookup)` is the older of the two tools.

**Syntax:**

`nslookup DOMAIN_NAME` performs a simple lookup using your default resolver.

`nslookup -type=TYPE DOMAIN_NAME [SERVER]` specifies a record type and an optional DNS server.

**Common DNS Record Types:**

## DNS Query Types

| Query Type | Result |
| :--- | :--- |
| **A** | IPv4 address(es) for the domain |
| **AAAA** | IPv6 address(es) for the domain |
| **CNAME** | Canonical Name: an alias that points one domain name to another |
| **MX** | Mail Servers: the servers responsible for handling email for the domain |
| **SOA** | Start of Authority: the primary name server, admin email, and zone serial number |
| **TXT** | Text Records: arbitrary text, commonly used for SPF, DKIM, DMARC, and domain verification |

**Example (IPv4 addresses via Cloudflare's resolver):**

![nslookup](../Images/Network%20Reconnaisance/Passive%20Reconnaisance/nslookup.png)

These IPs are often anycast (Cloudflare in this case). For penetration testing, each IP may host different services, so check whether they fall within scope.

**MX example (mail servers):**

![mailservers](../Images/Network%20Reconnaisance/Passive%20Reconnaisance/mailservers.png)

### dig

dig is the modern, preferred DNS query tool.

**Syntax:** `dig [@SERVER] DOMAIN_NAME [TYPE]`

**Example (MX records via Cloudflare):**

![dig](../Images/Network%20Reconnaisance/Passive%20Reconnaisance/dig.png)

> Privacy tip: Use public resolvers like 1.1.1.1 (which supports DNS over HTTPS and DNS over TLS) to avoid your ISP logging your queries.

> Defender note: Monitor for unexpected DNS changes (new MX records, rogue TXT entries). These can be signs of subdomain takeover or configuration errors.

On the AttackBox, open a terminal and use nslookup or dig (recommended) to answer the following questions.

### Answer the questions below

- Check the TXT records of thmlabs.com. What is the flag there?
    
    ![flag](../Images/Network%20Reconnaisance/Passive%20Reconnaisance/flag.png)

---

## Task 5 - DNS Dumpster

Standard dns lookups like `dig` and `nslookup` only resolve names that we already know. They dont reveal unadvertised subdomains like `blog.tryhackme.com`, `app.tryhackme.com`, or `dev.internal.company.com`.

Subdomains matter because they often expose forgotten or vulnerable services (outdated CMS installations, development panels), shadow IT or misconfigured applications, and additional attack surface such as exposed APIs or admin portals.

In passive recon, these subdomains are discovered using public OSINT sources with no queries sent to the target.

One well-known free tool is `DNSDumpster`. It aggregates public DNS data from sources such as search engine caches, zone transfer databases, and certificate records. It does not perform brute-force enumeration, which means it remains fully passive. The results include subdomains and hosts, resolved IPs with geolocation, MX, TXT, and CNAME records, and visual maps showing the relationships between these.

Search for `tryhackme.com` on DNSDumpster and you will see entries like `blog.tryhackme.com` that basic DNS lookups miss.

![dnsdumpster results](../Images/Network%20Reconnaisance/Passive%20Reconnaisance/dnsdumpster%20results.png)

DNSDumpster also graphs the data, showing how subdomains, IPs, and mail servers relate to each other:

![dnsdumpster graph](../Images/Network%20Reconnaisance/Passive%20Reconnaisance/dnsdumpster%20graph.png)

### Certificate Transparency (CT) Logs

The most effective method for subdomain discovery in passive mode is by using `Certificate Logs`  through `crt.sh`.

Certificate Transparency is a public logging framework (mandatory since approximately 2015) that records every SSL/TLS certificate issued by participating Certificate Authorities. Each certificate contains a **Subject Alternative Name (SAN)** field listing the domains and subdomains it covers. By searching these logs, you can discover subdomains without sending any traffic to the target.

To use crt.sh, visit `https://crt.sh` and search for `%.tryhackme.com`. The % wildcard matches any subdomain. The results will list every certificate issued for subdomains of tryhackme.com, often revealing 10 to 100 times more subdomains than DNSDumpster alone.

crt.sh is fully passive, operates in real time, and has no rate limits for basic use.

Other passive subdomain discovery options include SecurityTrails (free limited searches) and command-line tools like Subfinder, which aggregate multiple passive sources.

> Defender perspective: Organisations monitor CT logs and subdomain lists to catch dangling DNS records (which carry subdomain takeover risk) or unauthorised subdomains.

We can use the web browser on the AttackBox, or our own system, to answer the following questions.

### Answer the questions below

- Lookup tryhackme.com on DNSDumpster. Under Services / Banners, which one has the highest count?

    `cloudflare`

---

## Task 6 - Shodan.io

In passive reconnaisance tools like Shodan allow us to gather intelligence on the target's public facing assets without sending any traffic to them.

`Shodan` is a search engine for internet-connected devices. It continuously scans the public internet, collects banners and responses from open ports and services, and indexes them for search. Unlike Google, which indexes web pages, Shodan focuses on devices: servers, IoT equipment, cameras, routers, industrial control systems, and more.

> Defensive value: Organisations monitor Shodan (via alerts or manual checks) to identify unintended exposures such as rogue servers, forgotten test machines, or vulnerable services.

### Navigating the Shodan Interface

To begin, navigate to `https://www.shodan.io`. No account is required for basic searches. Enter a domain name (e.g., tryhackme.com) or an IP address from your earlier DNS lookups (e.g., 104.26.10.229) into the search bar.

The results page displays a list of matching hosts. Selecting a host opens a detailed view containing the following information:

- **IP address and ASN (Autonomous System Number):** identifies the network block.
- **Hosting provider/organisation** (e.g., Cloudflare, AWS): reveals the infrastructure behind the domain.
- **Geographic location (country, city):** approximate physical location of the server.
- **Open ports and services:** with version strings and banners (e.g., HTTP server type and version).
- **Tags:** such as cdn or vuln if a known vulnerability matches the detected service version.

### Search Tips

Shodan supports a range of search filters for narrowing results:

- `hostname:tryhackme.com` matches a specific hostname.
- `org:"TryHackMe" filters` by organisation name.
- `port:443 country:US` filters by port and country.
http.component:"wordpress" identifies technology stack (if exposed).

For the full reference, see: `https://help.shodan.io/the-basics/search-query-fundamentals`

For further exploration, `Censys.io` (free basic searches) provides similar host and certificate data. It can serve as a useful complement when cross-referencing results.

### Exercise

- Go to https://www.shodan.io (no account needed for basic searches).
- Search one of tryhackme.com's IPs (from Task 4, e.g., 104.26.10.229) or use hostname:tryhackme.com.
Explore the results: note the provider (Cloudflare), location (US), open ports (443/HTTPS is common), and banners.
- Use Shodan to answer the questions below. All answers are visible without a premium account.

### Answer the questions below

- According to Shodan.io, what is the first country in the world in terms of the number of publicly accessible Apache servers?

    `United States`

    

- Based on Shodan.io, what is the 3rd most common port used for Apache?

    `8080`

- Based on Shodan.io, what is the most common port used for nginx?

    `80`

---

## Task 7 - Summary

This room covered gathering intelligence without direct interaction with the target: the stealthiest phase of reconnaissance.

### Key tools and techniques:

- WHOIS: Domain registration details including registrar, dates, and name servers. Most personal details are now redacted for privacy.
- DNS lookups: A/AAAA (IP addresses), MX (mail servers), TXT (SPF/DMARC/verification), and other record types, queried via public resolvers like 1.1.1.1.
- Subdomain enumeration: DNSDumpster for DNS aggregation and graphing, and crt.sh for Certificate Transparency log searches, which is the most effective passive method for discovering subdomains via public SSL/TLS certificates.
- Exposed services: Shodan.io for device banners, ports, and hosting information.

The practical value of these methods is that they are fully passive. They trigger no alerts, carry minimal legal risk (when used ethically and within scope), yet they often uncover forgotten subdomains, outdated services, or misconfigurations.

### Tips:

- Use `DoH/DoT resolvers` (e.g., 1.1.1.1) to keep your own queries private.
- As a defender, monitor your footprint: set Shodan/Censys alerts, watch CT logs for new certificates, and track DNS changes for takeover risks.
- Even though passive recon does not touch the target directly, always ensure your overall engagement is authorised and within scope.
- Results change over time. IPs rotate (Cloudflare anycast), subdomains appear and disappear, and privacy redactions increase.

---

