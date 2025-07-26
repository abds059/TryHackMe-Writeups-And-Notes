# DNS In Detail

## Task 1 -  What Is DNS

- Domain Name System (DNS) provides simple way to communicate with devices on the internet
- Helps in reaching websites tgrough domains rather than their assigned unique IPs

---

## Task 2 - Domain Hierarchy

- Root Domain
- Top Level Domain
- Second Level Domain

- ### TLD 

    - Most right hand part of a domain
    - Tryhackme.**com**
        - Here **.com** is a **TLD**
    
    - **Two Types:**
        - **gTLD (Geneic Top Level)**
            - Tells the domain name purpose
            - For e.g, 

                - .com(commercial)
                - .org (organisation)
                - .edu(education) 
                - .gov (government)

        - **ccTLD (Country Code Top Level)**
            - Used for geographical purposes
                - For e.g,
                    - .ca (For sites in Canada)
                    - .uk (For UK based sites)

- ### Second Level Domain
    - For tryhackme.com
        - Here **tryhackme** is **second level domain**

    - Limited to **63 characters only**
    - Can use only
        - a-z
        - 0-9
        - hyphens (not in starting and ending place / neither consecutive places)

- ### SubDomain
    - Sits on the left hand side of second level domain
    - For e.g, **admin**.tryhackme.com
    - Same naming rules as second level domains
    - Limiteless subdomains can be created for a domain name

## Answer The Questions Below

- What is the maximum length of a subdomain?
    - 63

- Which of the following characters cannot be used in a subdomain ( 3 b _ - )?
    - (Hyphen) -

- What is the maximum length of a domain name?
    - 253

- What type of TLD is .co.uk?
    - ccTLD

----

## Task 3 - Record Types

```
A DNS record is a set of instructions within the Domain Name System (DNS) that links a domain name (like google.com) to its corresponding IP address (like 172.217.160.142).

```

- DNS isn't just for websites though, and multiple types of DNS record exist.

- ### A Record  
    - Resolves to IPV4 addresses, for example 104.26.10.229

- ### AAAA Record
    - Resolves to IPV6 addresses, for example 2606:4700:20::681a:be5

- ### CNAME Record
    - Resolves to another domain name
    - For e.g, tryhackme.com has a subdomain store.tryhackme.com which returns a cname record shop.shopify.com
    - Another DNS request would then be made to shops.shopify.com to work out the IP address.

- ### MX Records
    - Resolves to address of the servers whose email for the domain we are querying
    - For e.g, MX record response for tryhackme.com would be like alt1.aspmx.l.google.com
    - Also comes with priority flags that identify the order to try the servers if
        - Main server goes down
        - Email has to be sent to a backup server

- ### TXT Records
    - Used to store any text based data
    - Common usage
        -  To list servers that have the authority to send an email on behalf of the domain (this can help in the battle against spam and spoofed email)
        - To verify ownership of the domain name when signing up for third party services

## Answer the questions below

- What type of record would be used to advise where to send email?
    
    **MX** 

- What type of record handles IPv6 addresses?
    
    **AAAA**

---

## Task 4 - Making A Request

### What Happens When You Make A DNS Request ?

- #### 1 - Check Local Cache 
    - The computer first looks in its local cache to determine whether it already knows the domain or not
    - If found it uses the cached IP

- #### 2 - Asks Recursive DNS Cache
    - If not in local cache, it proceeds to ask a **Recursive DNS Cache** (usually from your ISP or a custom one like Google DNS)
    - This server also checks its cache

- #### 3 - Go To Root Server
    - If not found it goes to root Server that identifies the **TLD** from the domain (like .com, .org)
    
- #### 4 - Redirects To TLD Server
    - The TLD server points to the **Authoritative DNS Server** based on the TLD identified

- #### 5 - Get From Authoritative DNS Server
    - This server holds the actual DNS record
    - Sends the answer back to recursive DNS server

- #### 6 - Cache And Respond
    - At the Recursive DNS server a local copy is maintained for future reference
    - The record fetched is kept on the basis of TTL

**TTL (Time To Live)** -> 
This value is a number represented in seconds that the response should be saved for locally until you have to look it up again

## Answer the questions below

- What field specifies how long a DNS record should be cached for?
    
    **TTL**

- What type of DNS Server is usually provided by your ISP?
    
    **Recursive**

- What type of server holds all the records for a domain?
    
    **Authoritative**

---

## Task 5 - Practical

- What is the CNAME of shop.website.thm? 
    - shops.myshopify.com

- What is the value of the TXT record of website.thm?
    - THM{7012BBA60997F35A9516C2E16D2944FF}

- What is the numerical priority value for the MX record?
    - 30

- What is the IP address for the A record of www.website.thm?
    - 10.10.10.10


