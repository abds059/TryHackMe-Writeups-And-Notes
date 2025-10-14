# Root Me 

## Reconnaisance

First we start with basic network scanning using nmap then we search for hidden directories using gobuster

## Answer the questions given below

- **Scan the machine, how many ports are open?**

    #### Answer: 
    
    `2`

    #### Approach:

    In order to find open ports we run an nmap scan 

    ![basic nmap scan](/IMAGES/Root%20Me/nmap%20basic%20scan.png)

    Here we can observe that only two ports are open 

    - port 22 -> ssh
    - port 80 -> http

- **What version of Apache is running?**\

    #### Answer: 
    
    `2.4.41`

    #### Approach: 

    To find this we run nmap this time with `-sV` flag setup to detect version of services running

    ![apache service version](/IMAGES/Root%20Me/apache%20service.png)

    Here we get the the version of Apache as `2.4.41`

- **What service is running on port 22?**

    #### Answer: 
    
    `ssh`

    #### Approach: 

    From our very first nmap scan it is quite visible that **ssh is running on port 22**
    
- **What is the hidden directory?**

    #### Answer: 
    
    `/panel/`

    #### Approach: 

    For this we use the gobuster tool for bruteforcing hidden directories.

    ![gobuster](/IMAGES/Root%20Me/gobuster.png)

    Here we get four hidden directories (/js, /css, /panel, /uploads) but the format matched the **/panel** directory. The others will surely be useful later.

---

## Getting A Shell

After we discovered hidden directories we can navigate to them one by one to discover for possible vulnerabilities.

First of all we go to the **/panel** directory where we see a normal form for uploading files. The form can be a possible form of gaining access to a reverse shell so we search for a php reverse shell exploit to upload.

One of them can be downloaded from [here](https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php). 

We will download it on our vm using the following command:

`wget https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php`

![wget reverse shell](/IMAGES/Root%20Me/wget%20reverse%20shell%20file.png)

After we get the file we open it using `nano` to adjust the `ip address` and change the `port number`(to listen through netcat).

![nano file](/IMAGES/Root%20Me/nano%20file.png)

![change these things](/IMAGES/Root%20Me/change%20this.png)

You can change these by setting the $ip to be your tun0 ip and $port to a random port of your choice.

I first tried with port 4444, then 9999 but these two didnt work for me so I switched to using port `1234` for my netcat listener.

After the file is ready we save it and then upload it to our the form at /panel page.

![php not permitted](/IMAGES/Root%20Me/php%20not%20permitted.png)

When we hit upload we get a php not permitted form so maybe the app is filtering `.php` extensions. 

So in order to bypass this we can try changing the extension to `.php5` to see if this works.

After uploading we will get a success message in green box this time and then we can view our uoladed file at **/uploads** url

![php5 upload](/IMAGES/Root%20Me/php5%20uploads.png)

Now we can start a netcat listener at the specified port (1234 in my case) through the command `nc -lvnp 1234`

In order to get access to reverse shell we click on the uploaded file (php-reverse-shell in my case) to run it in the `/uploads` url.

As soon as the file runs we will get the reverse shell at our netcat listener.

![reverse shell access netcat](/IMAGES/Root%20Me/reverse%20shell%20connect.png)

## Answer the questions given below

- **user.txt**

    #### Answer: `THM{y0u_g0t_a_sh3ll}`

    #### Approach:
    
    After getting access to the shell we can now explore the system for user flag. So we simply search for `user.txt` using the `find` command as follows:

    ![searching for user.txt using find command](/IMAGES/Root%20Me/finding%20user.txt.png)

    Here we will get a path so we simply move to this path and retrieve our user flag.

    ![user flag](/IMAGES/Root%20Me/user%20flag.png)

---

## Privilege Escalation

Now that we have a shell, let's escalate our privileges to root.

## Answer the questions below

- **Search for files with SUID permission, which file is weird?**

    #### Answer: `/usr/bin/python`

    #### Approach: 

    To find unusual permission file we use the following command:
    
    `find / -perm -4000 2>dev/null`.

    ![finding unusual SUID permission files](/IMAGES/Root%20Me/suid%20unsusual.png)

    
- **root.txt**

    #### Answer: `THM{pr1v1l3g3_3sc4l4t10n}`

    #### Approach: 

    Now we need to escalate to root by exploiting the unusual SUID permissions. For this we run a simple one liner to get root access using python:

    `$ /usr/bin/python2.7 -c 'import os; os.setuid(0); os.system("/bin/bash")`

    ![getting root access](/IMAGES/Root%20Me/getting%20root%20access.png)

    After running this command we will successfully gain access to root privileges.

    Now we can simply search for `root.txt` file (just like we did for `user.txt` above) by using the `find` command:

    `find / -type f -name root.txt 2>dev/null`

    After that we will simply navigate to the specified file path and retrieve the root flag.

    ![root flag](/IMAGES/Root%20Me/root%20flag.png)

---


    