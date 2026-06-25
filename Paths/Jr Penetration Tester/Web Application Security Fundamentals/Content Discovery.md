# Content Discovery

## Task 1 - Introduction

In Web App Security content refers to anything that is hosted on a web server, such as files, pages, directories, admin portals, config files and backup archives. 

In Content Discovery our aim is to find content that is not meant to be publicly accessible but unintentionally exposed. This content could be staff portals, older versions of the site, exposed backup files, or administration panels. Finding it is a core part of any web application penetration test.

There are three main approaches to content discovery: 

- Manual
- Automated
- OSINT (Open-Source Intelligence)

---

## Task 2 - Manual Discovery - Common Files

Several files that web servers expose by convention can reveal more info than intended. Chceking these manually should be our first priority.

### robots.txt

The robots.txt file tells web browsers which pages to index. Site owners often list sensitive files here to avoid them getting indexed making it useful enough for a pentester. 

![robots.txt](../Images/Web%20Application%20Security%20Fundamentals/Content%20Discovery/robots.txt.png)

This robots.txt file tells web crawlers (like search engines) how to interact with the site. It allows all bots to access most of the site (Allow: /) but asks them not to visit /staff-portal. Keep in mind, this is only a guideline for bots, not a security control, so restricted paths may still be accessible if visited directly.

### sitemap.xml

Unline robots.txt (which restricts crawlers), `sitemap.xml` tells search engines which page the owner wanst to be listed. These files sometimes include staging pages, old content, or URLs that are hard to reach via the normal site.

![sitemap.xml](../Images/Web%20Application%20Security%20Fundamentals/Content%20Discovery/sitemap.xml.png)

As shown in the image, this sitemap lists specific endpoints available on the target application, including standard pages like `/news`, `/contact`, and multiple article IDs (`/news/article?id=1,2,3`). More importantly, it reveals sensitive or interesting paths such as `/customers/login` and `/s3cr3t-area`, which may not be easily discoverable through normal browsing. The presence of parameters like id= also hints at potential input points worth testing. This makes the sitemap a valuable source during reconnaissance for mapping the attack surface.

### Answer the questions below

- What is the directory in robots.txt that isn't allowed to be viewed by web crawlers?

    `/staff-portal`

- What is the path of the secret area found in sitemap.xml?

    `/s3cr3t-area`

---

## Task 3 - Manual Discovery - Headers and Frameworks Stacks

### HTTP Headers

When a web server responds to requests it includes HTTP headers in the response that can reveal useful information. Headers like `Server` and `X-Powered-By` often reveal web server software and the language or framework the application runs on.

![curl](../Images/Web%20Application%20Security%20Fundamentals/Content%20Discovery/curl.png)

Look through the response headers carefully; there may be a custom header containing a flag.

### Framework Stack

Once you've identified the framework (from the favicon, headers, or by inspecting the page source for comments and copyright notices), visit the framework's own website to learn more. Documentation pages often describe default directory structures, admin panel paths, and default credentials.

![curl framework](../Images/Web%20Application%20Security%20Fundamentals/Content%20Discovery/curl%20framework.png)

Lets visit this URL in the browser:

![framework website documentation](../Images/Web%20Application%20Security%20Fundamentals/Content%20Discovery/framework.png)

View the Acme IT Support page source at `http://<Machine-ip>`, there's a comment at the bottom of every page with a link to the framework's website. Follow that link and check the documentation to find the administration portal path. Access that path on the Acme IT Support site and log in with the default  `admin / admin` credentials to retrieve the flag.

### Answer the questions below

- What is the flag value from the X-FLAG header?

    `THM{HEADER_FLAG}`

- What is the flag from the framework's administration portal?

    Visit the address `http://<Machine-ip>//thm-framework-login`

    Then login with `admin / admin` credentials.

    ![framework flag](../Images/Web%20Application%20Security%20Fundamentals/Content%20Discovery/framework%20flag.png)

---

## Task 4 - OSINT - Search Engines and Web Tools

There are also external resources available that can help in discovering information about your target website; these resources are often referred to as OSINT (Open-Source Intelligence), as they're freely available tools that collect information.

### Google Dorking / Hacking

Google's advanced search operators let we filter results in ways that can surface sensitive content indexed from your target. By combining operators, we can find exposed admin panels, leaked documents, and login pages that the site owner never intended to be public.

| Filter | Example | Description |
|:---|:---|:---|
| `site` | `site:tryhackme.com` | Returns results only from the specified domain |
| `inurl` | `inurl:admin` | Returns results with the specified word in the URL |
| `filetype` | `filetype:pdf` | Returns results of a specific file type |
| `intitle` | `intitle:admin` | Returns results with the specified word in the page title |
| `intext` | `intext:password` | Returns results containing the specified word in the body |
| `cache` | `cache:tryhackme.com` | Shows Google's cached version of the page |

For example, site:tryhackme.com filetype:pdf would return all PDFs indexed from tryhackme.com. You can combine multiple filters in a single query. More information is available at Wikipedia: [Google Hacking](https://en.wikipedia.org/wiki/Google_hacking).

### Wappalyzer

Wappalyzer is a browser extension and online tool that identifies the technologies a website uses, frameworks, CMS platforms, CDNs, analytics tools, payment processors, and more. It can often detect version numbers, which helps when searching for known vulnerabilities.

### Answer the questions below

- What Google dork operator limits results to a specific site?

    `site:`

- What online tool and browser extension identifies what technologies a website is running?

    `Wappalyzer`

---

## Task 5 - OSINT Repositories and Archives

### WayBack Machine

The [Wayback Machine](https://web.archive.org/) is an archive of the Internet dating back to the late 1990s. Search for a domain, and you'll see every snapshot captured over time. This is useful for finding pages that have been removed from the live site but may still be accessible: old login forms, forgotten API endpoints, or content that was published briefly before being taken down.

### GitHub

[Git](https://git-scm.com/) is a version control system that tracks changes to files over time. GitHub is the most widely used cloud-hosted platform for Git repositories. Developers sometimes accidentally commit sensitive data: API keys, credentials, configuration files, and .env files, before realising the repository is public.

Search GitHub for the company name or domain you're targeting. Once you find a relevant repository, look through the commit history, not just the current files. Sensitive data is often removed in a later commit, but remains in the history.

### S3 Buckets

[Amazon S3](https://aws.amazon.com/pm/serv-s3/) (Simple Storage Service) is a cloud storage platform that many organisations use to host files and static website content. The URL format for an S3 bucket is `https://{name}.s3.amazonaws.com`. Bucket owners set permissions, but misconfigurations are common: a publicly accessible bucket can expose files that were never meant to be seen.

Common naming patterns include `{company}-assets`, `{company}-backup`, `{company}-www`, and `{company}-dev`. Try these patterns against your target's company name. You can also find bucket URLs in the website's page source or in GitHub repositories.

### Answer the questions below

- What is the website address for the Wayback Machine?

    `https://web.archive.org/`

- What URL format do Amazon S3 buckets end in? (Answer starts with a .)

    `.s3.amazonaws.com`

---

## Task 6 - Automated Discovery - Gobuster Fundamentals

Manual and OSINT techniques can only take you so far. Automated discovery uses tools to rapidly send hundreds or thousands of requests to a web server to check whether directories, files, or other resources exist. This process relies on wordlists, text files containing commonly used directory names, file names, and paths.

### Gobuster

[Gobuster](https://github.com/OJ/gobuster) is an open-source enumeration tool written in Go. It supports multiple modes: directory/file enumeration (dir), DNS subdomain enumeration (dns), and virtual host enumeration (vhost). It's pre-installed on the AttackBox and included by default in Kali Linux.

Run `gobuster --help` to see the available commands and global flags:

| Item / Flag | Details |
| :--- | :--- |
| **Directory starting with `/mo`** | `/more` (Inferred from common Gobuster wordlists and context; text mentions `/customers`, `/private`, `/assets`, `/news`, `/contact`, `/development.log`, `robots.txt`, `sitemap` but explicitly asks for the `/mo` directory which is typically `/more` in this specific TryHackMe room context) |
| **Log file discovered** | `/development.log` |
| **Recommendation Likelihood** | Highly likely (Context implies an "Easy" 30-min room with valuable findings like `/customers` and `/development.log`) |
| **`-t` / `--threads`** | Number of concurrent threads (default: 10). Increase for faster scans. |
| **`-w` / `--wordlist`** | Path to the wordlist file. Required for all modes. |
| **`-o` / `--output`** | Write results to a

### Wordlists

A good wordlist is critical. [SecLists](https://github.com/danielmiessler/SecLists) is the most widely used collection and is pre-installed on the AttackBox at `/usr/share/wordlists/SecLists/`. For directory enumeration, `Discovery/Web-Content/common.txt` and `Discovery/Web-Content/directory-list-2.3-medium.txt` cover most scenarios.

### dir Mode

The dir mode brute-forces directories and files on a web server. The basic syntax is:

`gobuster dir -u "http://<Machine-ip>" -w /path/to/wordlist`

The `-u` flag specifies the target URL that Gobuster will run its discovery against. The `-w` flag specifies the wordlist file; a list of directory and file names Gobuster will try against the target one by one. Both `-u` and `-w` are required for Gobuster to run; omitting either will result in an error.

Some additional useful flags for dir mode:

| Flag | Description |
| :--- | :--- |
| `-t` / `--threads` | Number of concurrent threads (default: 10). Increase for faster scans. |
| `-w` / `--wordlist` | Path to the wordlist file. Required for all modes. |
| `-o` / `--output` | Write results to a file instead of stdout. |
| `--delay` | Wait time between requests: useful against rate-limited servers. |
| `-x` / `--extensions` | File extensions to search for (e.g., `-x .php,.txt,.js`). |
| `-r` / `--followredirect` | Follow HTTP redirects. |
| `-k` / `--no-tls-validation` | Skip TLS certificate verification (useful in lab environments). |
| `-s` / `--status-codes` | Only show specific status codes (e.g., `-s 200,301`). |

Run the following command against the Acme IT Support web server and review the results:

![gobuster scan](../Images/Web%20Application%20Security%20Fundamentals/Content%20Discovery/gobuster.png)

The Gobuster scan discovered several accessible directories and files on the target web application. It reveals common pages like `/contact` and `/news`, along with interesting endpoints such as `/customers` (which redirects to a login page) and `/development.log`, which may contain sensitive information. Additionally, directories such as `/private` and `/assets` were found, along with files such as robots.txt and sitemap.xml, which can further aid reconnaissance. This output helps map the application structure and identify potential entry points for further testing.

### Answer the questions below

- What is the name of the directory beginning with /mo that was discovered?

    `/monthly`

- What is the name of the log file that was discovered?

    `/development.log`

---

## Task 7 - Automated Discovery - Subdomains and Virtual Hosts

The dns mode allows Gobuster to brute-force subdomains. During a penetration test, checking the subdomains of your target’s top domain is essential. Just because something is patched in the regular domain, it doesn't mean it is also patched in the subdomain. An opportunity to exploit a vulnerability in one of these subdomains may exist.

For example, if TryHackMe owns tryhackme.thm and mobile.tryhackme.thm, there may be a vulnerability in mobile.tryhackme.thm that is not present in tryhackme.thm. That is why it is important to search for subdomains as well!

### Subdomains vs Virtual Hosts

A subdomain is resolved through DNS. For example, blog.example.thm is a DNS record that points to an IP address.

A virtual host (vhost) on the other hand is resolved by the web server. Multiple sites can run on the same IP address, with the server using the Host: HTTP header to decide which site to serve.
As mentioned, Gobuster has separate modes for each, dns for subdomains and vhost for virtual hosts.

### Preparing the Environment

We are going to work in a local network with a DNS server on the web server. To ensure we can resolve the domains used throughout this room, you need to change the `/etc/resolv-dnsmasq` file:

- Open up a terminal on the AttackBox and enter the command: sudo nano `/etc/resolv-dnsmasq`.
- Insert nameserver `10.112.181.124` as the first line.
- Save the file by pressing `CTRL+O`, followed by pressing ENTER, and then exit the editor by pressing `CTRL+X`.
- Enter the command `/etc/init.d/dnsmasq` restart to restart the Dnsmasq service.

The file should look something like this:

![cat command](../Images/Web%20Application%20Security%20Fundamentals/Content%20Discovery/preparing%20environment.png)

### Updating the Host File

To ensure the domain used in this room resolves correctly, we need to manually map it to the target IP using the `/etc/hosts` file:

- Open a terminal on the AttackBox and run: `sudo nano /etc/hosts`.
- Add the following line at the end of the file: `10.112.181.124 example.thm`.
- Save the file by pressing `CTRL+O`, then ENTER, and exit using `CTRL+X`.
- You can verify the change by running: `ping example.thm`.

The file should look something like this:

![vhost file](../Images/Web%20Application%20Security%20Fundamentals/Content%20Discovery/vhost%20config%201.png)
![vhost file](../Images/Web%20Application%20Security%20Fundamentals/Content%20Discovery/vhost%20config%20.png)

### dns Mode

The dns mode performs DNS lookups using wordlist entries as subdomain candidates. The required flags are `-d` (domain) and `-w` (wordlist). The `--wildcard` option in Gobuster is used to force enumeration even when wildcard DNS is detected, allowing results to be returned despite potential false positives.

In the AttackBox or in local vm, enter the following command:

`gobuster dns -d example.thm \
-r <Machine-ip> \
-w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt \
--wildcard`

![dns mode](../Images/Web%20Application%20Security%20Fundamentals/Content%20Discovery/gobuster%20dns.png)

Some useful flags for dns mode are:

- `-d / --domain:` The target domain to enumerate
- `-i / --show-ips:` Show the IP addresses that subdomains resolve to
- `-r / --resolver:` Use a custom DNS server for lookups

### vhost Mode

The vhost mode doesn't use DNS. Instead, it sends HTTP requests to the target IP, cycling through wordlist entries as the `Host:` header value. This finds virtual hosts that aren't registered in public DNS.

Run the vhost scan with the following commands:

` gobuster vhost -u "http://<Machine-ip>" --domain example.thm -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt --append-domain --exclude-length 250-320`

The `--append-domain` flag tells Gobuster to combine each wordlist entry with the domain, and `--exclude-length` filters out false positives that share a common response size.

### Answer the questions below

- Apart from dns and -w, which shorthand flag is required for dns mode?

    `-d`

- How many virtual hosts on acmeitsupport.thm respond with status code 200?

    ![gobuster vhost acmeitsupport.thm](../Images/Web%20Application%20Security%20Fundamentals/Content%20Discovery/vhost%20acmeitsupport.png)

---