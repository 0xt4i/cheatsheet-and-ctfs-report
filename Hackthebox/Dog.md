---
Category: 
Difficulty: 
Platform: 
status: 
tags:
---

{{imagine}}

{{TOC}}

---
# Resolution summary

>[!summary]
>- Step 1
>- Step 2

## Improved skills

- Skill 1
- Skill 2

## Used tools

- `nmap`: A tool to scan port on target.
- `git-dumer`: A tool to dump a git repository from a website.
- `ffuf`: A tool to 

## Video

<iframe width="660" height="415" src="https://www.youtube.com/embed/XXXXXX" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


---

# Information Gathering

Scanned all TCP ports:

```bash
sudo nmap -vvv -sC -sV -A 10.10.11.58 -oA nmap/dog                    
```

```bash
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.2p1 Ubuntu 4ubuntu0.12 (Ubuntu Linux; protocol 2.0)  
80/tcp open  http    syn-ack ttl 63 Apache httpd 2.4.41 ((Ubuntu))                                                            
|_http-generator: Backdrop CMS 1 (https://backdropcms.org)                                                                    
| http-robots.txt: 22 disallowed entries                                                                                      
| /core/ /profiles/ /README.md /web.config /admin                                                                             
| /comment/reply /filter/tips /node/add /search /user/register  
| /user/password /user/login /user/logout /?q=admin /?q=comment/reply 
| /?q=filter/tips /?q=node/add /?q=search /?q=user/password                                                                   
|_/?q=user/register /?q=user/login /?q=user/logout            
|_http-favicon: Unknown favicon MD5: 3836E83A3E835A26D789DDA9E78C5510
|_http-server-header: Apache/2.4.41 (Ubuntu)                                                                                  
|_http-title: Home | Dog                                       
| http-methods:                
|_  Supported Methods: GET HEAD POST OPTIONS                                                                                  
| http-git:                                                    
|   10.10.11.58:80/.git/
|     Git repository found!                                   
|     Repository description: Unnamed repository; edit this file 'description' to name the...
|_    Last commit message: todo: customize url aliases.  reference:https://docs.backdro..
```

So i notice port 80 and path `.git/`

Enumerated open TCP ports:

```bash
| http-git:                                                    
|   10.10.11.58:80/.git/
```

---

# Enumeration

## Port 80 - HTTP (Apache)

---
Check path based on result `scan`

![[Hackthebox/attachments/Dog.png]]

So now, i want download all of file git to my local for discover there use `git-dumer`

```bash
git-dumper http://10.10.11.58/.git/
```


![[Hackthebox/attachments/Dog-1.png]]

the content enumeration some path related user `admin` with `cat`

![[Hackthebox/attachments/Dog-4.png]]

![[Hackthebox/attachments/Dog-2.png]]

So this page is denied

![[Hackthebox/attachments/Dog-6.png]]

![[Hackthebox/attachments/Dog-7.png]]

So i thinks the is page wordpree so if have vulnerable related to `plugin` such as [CVE - CVE-2024-3469](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2024-3469), so i check plugin in folder with command `find` and `grep`

![[Hackthebox/attachments/Dog-8.png]]

I see some modules like `view`, `layout` and `ckeditor`. but i don't see vulnerability.

So i going to check file `settings.php` with command `vim`

![[Hackthebox/attachments/Dog-9.png]]

The first thing that catches my eye is the notable line about the line `$database`.

![[Hackthebox/attachments/Dog-10.png]]

I have the hint for access account, maybe username is`root` and password `BackDropJ2024DS2024`, try to log in to website.

![[Hackthebox/attachments/Dog-11.png]]

The notification says the username is unrecognized, while the password has no issue. So I will search for another username and keep the same password.

![[Hackthebox/attachments/Dog-12.png]]

I looking for another username in home page that is `dogBackDropSystem`, try to login again.

![[Hackthebox/attachments/Dog-13.png]]

The notification says incorrect password so should find other password.

I will navigate to the password reset field to check the two accounts I just found.

When i enter username `root` 
![[Hackthebox/attachments/Dog-14.png]]
When i enter to reset password with username `dogBackDropSystem`
![[Hackthebox/attachments/Dog-17.png]]
![[Hackthebox/attachments/Dog-16.png]]

I discovered an issue: when using the username `dogBackDropSystem` in the reset password section, it redirects back to the login page with a notification `Unable to send email`, i this system is execute when i enter username that system expect.

# Exploitation

##  Injection Vulnerability
So now I will use **Burp Suite** to modify the request when entering the username `dogBackDropSystem` in the password reset field, and attempt to inject a command for the system to execute. At this point, I will send this request to the Repeater to modify it and analyze the response received. 

![[Hackthebox/attachments/Dog-21.png]]

When i change become username `root`, i received the entire page, whereas I was hoping for a simple API-style response and with `Error Messange`

![[Hackthebox/attachments/Dog-22.png]]

Now I want to perform brute-forcing and fuzzing on this website, so I will copy the file named `login.req` and use the `ffuf` tool. I will determine the position to brute-force by adding `FUZZ`

![[Hackthebox/attachments/Dog-24.png]]

Now I want to send requests using the list of collected usernames for brute-forcing, and I want to search for a specific string on the page using the `-fr` option to filter it out. In the command, I will filter out any result containing `Error Message` so that it will not appear in the output.

```bash
ffuf -request login.req -request-proto http -w /opt/SecLists/Usernames/Names/names.txt -of ffuf-result -fr 'Error message'
```

![[Hackthebox/attachments/Dog-25.png]]

Other i want to fuzz with request of log in, so i copy file request and want to filter `unrecognized username` in respone.

![[Hackthebox/attachments/Dog-26.png]]









---

# Lateral Movement to xxx

## Local enumeration


## Lateral movement vector

---

# Privilege Escalation to xxx

## Local enumeration


## Privilege Escalation vector


---

# Trophy

{{image}}

>[!todo] **User.txt**
>flag

>[!todo] **Root.txt**
>flag

**/etc/shadow**

```bash

```