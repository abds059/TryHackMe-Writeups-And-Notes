# Vulnversity

## Task 1 - Deploy the machine

### Answer the questions below

- Deploy the machine

    `No answer needed`

## Task 2 - Reconnaissance

Nmap is a free, open-source and powerful tool used to discover hosts and services on a computer network. In our example, we use Nmap to scan this machine to identify all services running on a particular port

| **Nmap Flag** | **Description** |
|---------------|-----------------|
| `-sV`         | Attempts to determine the version of the services running |
| `-p <x>` or `-p-` | Port scan for port `<x>` or scan all ports |
| `-Pn`         | Disable host discovery and scan for open ports |
| `-A`          | Enables OS and version detection, executes in-build scripts for further enumeration |
| `-sC`         | Scan with the default Nmap scripts |
| `-v`          | Verbose mode |
| `-sU`         | UDP port scan |
| `-sS`         | TCP SYN port scan |


### Answer the questions below

- There are many Nmap "cheatsheets" online that you can use too.

    `No answer needed`

- Scan the box; how many ports are open?

    `6`

    Use the command: `nmap -p- -T4 <target-ip> -Pn`

- What version of the squid proxy is running on the machine?

    `4.10`

    ![nmap_version](/IMAGES/Vulnversity(1)_nmap.png)

    Basic Command Syntax: `nmap -sV -p3128 -T4 <target-ip>` (Squid proxy was running on port 3128 at my time)

- How many ports will Nmap scan if the flag -p-400 was used?

    `400`

- What is the most likely operating system this machine is running?

    `Ubuntu`

    ![nmap_OS](/IMAGES/Vulnversity(2)_nmap.png)

    I have used the command: `namp -A -T4 <target-ip>`
    You should use teh command: `nmap -O <target-ip>` as its more easier and straightforward

- What port is the web server running on?

    `3333`

    ![nmap_webserver](/IMAGES/Vulnversity(3)_nmap.png)

    Basic Command Syntax: `nmap -sV -T4 <target-ip>`

- It's essential to ensure you are always doing your reconnaissance thoroughly before progressing. Knowing all open services (which can all be points of exploitation) is very important, don't forget that ports on a higher range might be open, so constantly scan ports after 1000 (even if you leave checking in the background).

    `No answer needed`

- What is the flag for enabling verbose mode using Nmap?

    `-v`

## Task 3 - Locating directories using Gobuster

Gobuster is a tool for brute-forcing URIs (directories and files), DNS subdomains, and virtual host names. For the given machine, we will focus on using it to brute-force directories.

Download Gobuster [here](https://github.com/OJ/gobuster), or if you're on Kali Linux run `sudo apt-get install gobuster`.

To get started, you will need a wordlist for Gobuster (which will be used to quickly go through the wordlist to identify if a public directory is available).

If you use Kali Linux, you can find many wordlists under `/usr/share/wordlists`. You can also use the wordlist for directories located at `/usr/share/wordlists/dirbuster/directory-list-1.0.txt` in the AttackBox.

Now let's run Gobuster with a wordlist using `gobuster dir -u http://10.201.1.164:3333 -w` .

| Flag         | Description                                                 |
|--------------|-------------------------------------------------------------|
| `-e`         | Print the full URLs in the console output                   |
| `-u`         | Specify the target URL                                      |
| `-w`         | Path to your wordlist for brute-forcing                     |
| `-U`         | Username for HTTP Basic Authentication                      |
| `-P`         | Password for HTTP Basic Authentication                      |
| `-p <x>`     | Proxy to use for requests (e.g., `http://127.0.0.1:8080`)   |
| `-c <cookie>`| Specify a cookie to simulate an authenticated session       |


### Answer the questions below

- I have successfully configured Gobuster. 

    `No answer needed`

- What is the directory that has an upload form page?

    `/internal/`

    ![gobuster_directory(1)](/IMAGES/Vulnversity(4)_gobuster.png)

    Basic Command Syntax: `gobuster dir -u http://<target-ip> -w /usr/share/wordlists/dirb/common.txt`

    ![gobuster_directory(2)](/IMAGES/Vulnversity(5)_gobuster.png)

    Basic Command Syntax: `curl http://<target-ip>/<directory-name>`

    Or you can go to the url/<directory-name> in the browser and inspect the page by `Ctrl + U`

