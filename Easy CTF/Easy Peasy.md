# Easy Peasy

## Task 1 - Enumeration Through Nmap

Deploy the machine attached to this task and use nmap to enumerate it

### Answer the questions below

- How many ports are open?

    #### Answer: `3`

    #### Process:

    ![basic namp scan](/IMAGES/Easy%20Peasy%20CTF/basic%20nmap%20scan.png)

- What is the version of nginx?

    #### Answer: `1.6.1`

    #### Process:

    To find this we run nmap again with `sC` and `sV` flag:

    ![nmap service scan](/IMAGES/Easy%20Peasy%20CTF/nmap%20service%20scan.png)

- What is running on the highest port?

    #### Answer: `Apache`

    #### Process: 

    ![nmap service scan](/IMAGES/Easy%20Peasy%20CTF/nmap%20service%20scan.png)

    From the above scan we can clearly see that Apache is running on port `65524`

---

## Task 2 - Compromising The Machine

Now you've enumerated the machine, answer questions and compromise it!

### Answer the questions below

- Using GoBuster, find flag 1.

    #### Answer: `flag{f1rs7_fl4g}`

    #### Process:
    
    To find this flag we run gobuster on the target machine ip to find any hidden directories:

    ![running gobuster](/IMAGES/Easy%20Peasy%20CTF/hidden%20directory.png)

    We found one hidden directory named `hidden` so we navigate to the url in the browser:

    ![hidden directory url page](/IMAGES/Easy%20Peasy%20CTF/hidden%20room.png)

    Upon inspecting the code we dont find anything interesting so we run gobuster on this url to search for anymore hidden sub directories if they exist:

    ![running gobuster on /hidden](/IMAGES/Easy%20Peasy%20CTF/whatever%20directory.png)

    Surprisingly we find a sub directory called `whatever` and check the url in the browser:

    ![whatever page](/IMAGES/Easy%20Peasy%20CTF/whatever%20page.png)

    We then inspect the code and find a base64 string:

    ![whatever inspect](/IMAGES/Easy%20Peasy%20CTF/whatever%20inspect.png)

    Upon decoding we find our first flag:

    ![flag 1](/IMAGES/Easy%20Peasy%20CTF/first%20flag.png)

- Further enumerate the machine, what is flag 2?

    #### Answer: `flag{1m_s3c0nd_fl4g}`

    #### Process:

    We check the `robots.txt` file and find a possible hash:

    ![hash robots.txt](/IMAGES/Easy%20Peasy%20CTF/robots.txt%20hash.png)

    We analyze this hash using [Hash Identifier](https://www.tunnelsup.com/) and find the following:

    ![analyzing hash](/IMAGES/Easy%20Peasy%20CTF/analyzing%20hash%20of%20hidden%20directory.png)

    We use an online tool, [Md5Hasing](https://md5hashing.net/hash) to decrypt the hash:

    ![decrypting md5 hash](/IMAGES/Easy%20Peasy%20CTF/second%20flag.png)
   
- Crack the hash with easypeasy.txt, What is the flag 3?

    #### Answer: `flag{9fdafbd64c47471a8f54cd3fc64cd312}`
    #### Process:

    Since we know that an apache server is running on port `65524` we check the relevant url for that. 

    On reading the content on the apache server we find the plain flag within the regular text:

    ![flag2 apache](/IMAGES/Easy%20Peasy%20CTF/apache%20flag2.png)

- What is the hidden directory?

    #### Answer: `/n0th1ng3ls3m4tt3r`

    #### Process:

    Opon inspecting the source code of the apache server we get an encoded string:

    ![hidden directory encoded string](/IMAGES/Easy%20Peasy%20CTF/apache2%20hidden%20directory%20encoded%20string.png)

    We decoded this base62 string in [cyberchef](cyberchef.io) and find the hidden directory name:

    ![hidden directory name](/IMAGES/Easy%20Peasy%20CTF/decoding%20directory%20name.png)
   
- Using the wordlist that provided to you in this task crack the hash? What is the password?

    #### Answer: `mypasswordforthatjob`

    #### Process:

    Now we Navigate to the hidden directory in the browser we found above. Upon closer inspection we see that an image is embedded in the page:

    ![hidden directory image](/IMAGES/Easy%20Peasy%20CTF/hidden%20directory%20image.png)

    We download the image and examine it for any hidden information using `stegseek`. We get the required passphrase for our question by this:

    ![stegseek hash crack](/IMAGES/Easy%20Peasy%20CTF/stegseek%20hash%20crack.png)


- What is the password to login to the machine via SSH?

    #### Answer: `iconvertedmypasswordtobinary`

    #### Process:

    Upon extracting the content from the output file of stegcreek command we get the ssh credentials:

    ![ssh username](/IMAGES/Easy%20Peasy%20CTF/ssh%20username%20password.png)

    Since the password is in binary we use [Cyberchef](cyberchef.io) to convert it into plain text:

    ![decoding ssh password from binary to normal format](/IMAGES/Easy%20Peasy%20CTF/decoding%20password%20binary%20format.png)

- What is the user flag?

    #### Answer: `flag{n0wits33msn0rm4l}`

    #### Process:

    Now since we have the credentials so we utilize them to login to ssh:

    ![logging into ssh](/IMAGES/Easy%20Peasy%20CTF/logging%20in%20via%20ssh.png)

    Upon examining the current folder a bit by running the `ls` command we find `user.txt` and ultimately the flag:

    ![user flag](/IMAGES/Easy%20Peasy%20CTF/user%20flag.png)

    However we are told that the flag is rotated so its not our answer. So we de-rotate it using [rot13](rot13.com):

    ![derotating the user flag](/IMAGES/Easy%20Peasy%20CTF/getting%20the%20user%20flag%20by%20derotating.png)

- What is the root flag?

    #### Answer: `flag{63a9f0ea7bb98050796b649e85481845}`

    #### Process:

    Now we need to do privilege escalation. For that we explore the system a bit by running basic linux commands. After digging through the sytem for a while we find a vulnerable cron job:

    ![finding cron job](/IMAGES/Easy%20Peasy%20CTF/finding%20cron%20job.png)

    In order to take advantage of this we need to see at which time interval this cron job is executed and with what privilege:
    
    ![finding cron job in processes](/IMAGES/Easy%20Peasy%20CTF/finding%20cron%20job%20in%20processes.png)

    We can see here the script is executed every minute as root, so we can overwrite the script possibly to gain root access.

    In order to do this, we need to check the permissions for the cron job to see if we can write to the file or not:

    ![checking permsissions for cron job](/IMAGES/Easy%20Peasy%20CTF/checking%20permissions%20for%20cron%20job.png)

    Since we can see that our user `boring` has write permissions for the cron job we overwrite it and grant it to executable using `chmod` to get the root flag:

    ![overwriting the cron job](/IMAGES/Easy%20Peasy%20CTF/overwriting%20the%20sh%20file%20and%20changing%20permissions.png)

    Since the job runs every minute, we wait 1–2 minutes, then check if we got the root flag or not:

    ![root flag](/IMAGES/Easy%20Peasy%20CTF/root%20flag.png)

