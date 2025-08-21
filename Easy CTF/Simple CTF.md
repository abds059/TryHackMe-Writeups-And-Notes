# Simple CTF

Deploy the machine and attempt the questions!

## Answer the questions below

- **How many services are running under port 1000?**

    #### Answer: `2`

    #### Approach:
    
    In order to find which ports are open we run nmap scan for all ports with the `-p-` flag to find all possible open ports.

    ![nmap scan](/IMAGES/Simple%20CTF/nmap(Q1).png)

    Here we find that there are two open ports below 1000 `21` and `80`. We can confirm this answer by running a scan for port range `1-1000`

    ![namp scan 1-1000 ports](/IMAGES/Simple%20CTF/nmap(Q1(1)).png)

- **What is running on the higher port?**

    #### Answer: `ssh`

    #### Approach:

    For the port `2222` which is the higher port (non-standard / uncommon port) in this case we are getting `EtherNetIP-1`. To confirm this we can run a specific scan for port `2222` with the flag `-p2222` and `-sV` to check version of service running

    ![nmap higher port](/IMAGES/Simple%20CTF/nmap(Q2).png)

    We get the service running on port `2222` as `ssh`

- **What's the CVE you're using against the application?**

    #### Answer: `CVE-2019-9053`

    #### Process:

    In order to find out the CVE we need to find what is running on http at port `80`. On putting the IP in firefox it turns out to be an apache2 webpage. 

    ![apache2 webpage](/IMAGES/Simple%20CTF/apache2-webpage.png)

    **So maybe there is a hidden directory ?**

    To find any hidden directory we use `gobuster`

    ![gobuster hidden directory finding](/IMAGES/Simple%20CTF/hidden_dir.png)

    So the hidden directory was `/simple`. Appending this in the IP we get a CMS page

    ![cms](/IMAGES/Simple%20CTF/cms.png) 

    For the CVE we need the version of CMS this is running. Upon inspecting the page we find the version listed in the footer of the website as `CMS Made Simple version 2.2.8` 

    ![cms version](/IMAGES/Simple%20CTF/cms%20version.png)

    A simple search give us the CVE as `CVE-2019-9053` 

- **To what kind of vulnerability is the application vulnerable?**

    #### Answer: `sqli`

    #### Approach:

    Clicking in the first link we discovered that the CVE was related to `sqli` (SQL Injection)

    ![Vulnerability](/IMAGES/Simple%20CTF/Vulnerability.png)

- **What's the password?**

   #### Answer: `secret`

   #### Approach:

   To get this answer we need to search for an exploit for CMS version 2.2.8. Upon searching through I got a few exploits which are as follows:

   - **Exploit 1:**

        You can find this exploit here:

        link: https://www.exploit-db.com/exploits/46635

        After that copy and paste this exploit and run it through the folowing command:

        `python CVE-2019-9053.py -u http://10.x.x.x/simple/ --crack -w 10k-most-common.txt`



    - **Exploit 2:**

        You can find the exploit by running this:

        `searchsploit cms made simple 2.2.8`

        ![finding exploit through searchsploit](/IMAGES/Simple%20CTF/searchsploit.png)

        After that you can copy this exploit to your local machine and run it:

        `cp /usr/share/exploitdb/exploits/php/webapps/46635.py ./46635.py`

        ![copying exploit](/IMAGES/Simple%20CTF/Copying-exploit.png)

        The command to run the exploit is as follows: 
        
        `python2 46635.py -u http://10.x.x.x/simple --crack -w /usr/share/wordlists/rockyou.txt` 


    You can also find multiple other exploits by a simple google search and run them and get the desired answer

- **Where can you login with the details obtained?**

    #### Answer: `ssh`

    #### Approach:

    ![connecting through ssh](/IMAGES/Simple%20CTF/connecting_through_ssh.png)

    Since we knew from our nmap scan that `port 2222` is running ssh we simply connect through it and login with our username (`mitch`) found by running the exploit

- **What's the user flag?**

    #### Answer: `G00d j0b, keep up!`

    #### Approach:

    ![user_flag](/IMAGES/Simple%20CTF/user_flag.png)

    We examined the permissions of our current user in the current directory. Here we find read permissions for `user.txt` file which on running the `cat` command gives us the user flag 

- **Is there any other user in the home directory? What's its name?**

    #### Answer: `sunbath`

    #### Approach:

    ![second_user](/IMAGES/Simple%20CTF/second_user.png)

    In the home directory all the users in Linux have their respective folders. So a basic `ls` command gives us the name of the second user.

- **What can you leverage to spawn a privileged shell?**

    #### Answer: `vim`

    #### Appoach:

    ![vim](/IMAGES/Simple%20CTF/vim.png)

    By checking through `sudo -l -l` command we come to know that we can run vim as root without password which clearly indicates that we can leverage this to gain root access.

- **What's the root flag?**

    #### Answer: `W3ll d0n3. You made it!`

    #### Approach: 

    ![root access](/IMAGES/Simple%20CTF/root_access.png)

    Since we know that we can utilize vim as a method to escalate our privileges to root user so we use the `sudo vim -c ':!/bin/sh'` command. This command does the following:

    - `-c` tells vim to run a command on startup.

    - `:!<cmd>` in vim, the `:!` command means run a shell command outside of vim



    So `:!/bin/sh` means:
    - Open vim as root
    - Immediately execute /bin/sh (a shell)
    - Since vim is running as root, that shell inherits root privileges.

    After that we check what our current user is by `whoami` command which confirms our current user as root.

    Aftergaining root previleges we explore a bit with ls command and get our final flag in the `root.txt` file

    ![final flag](/IMAGES/Simple%20CTF/final_flag.png)


