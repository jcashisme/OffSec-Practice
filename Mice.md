<h1>NMAP</h1>

```
nmap -sC -sV -Pn -p- -oA mice 192.168.195.199
Starting Nmap 7.93 ( https://nmap.org ) at 2022-10-30 22:34 CDT
Nmap scan report for 192.168.195.199
Host is up (0.042s latency).
Not shown: 65530 filtered tcp ports (no-response)
PORT     STATE SERVICE        VERSION
1978/tcp open  unisql?
| fingerprint-strings: 
|   DNSVersionBindReqTCP, GenericLines, GetRequest, HTTPOptions, Help, NULL, RTSPRequest, SSLSessionReq, TLSSessionReq: 
|_    SIN 15win nop nop 300
1979/tcp open  unisql-java?
1980/tcp open  pearldoc-xact?
3389/tcp open  ms-wbt-server  Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: REMOTE-PC
|   NetBIOS_Domain_Name: REMOTE-PC
|   NetBIOS_Computer_Name: REMOTE-PC
|   DNS_Domain_Name: Remote-PC
|   DNS_Computer_Name: Remote-PC
|   Product_Version: 10.0.19041
|_  System_Time: 2022-10-31T03:40:10+00:00
|_ssl-date: 2022-10-31T03:40:41+00:00; +51s from scanner time.
| ssl-cert: Subject: commonName=Remote-PC
| Not valid before: 2022-10-29T23:00:13
|_Not valid after:  2023-04-30T23:00:13
7680/tcp open  pando-pub?
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port1978-TCP:V=7.93%I=7%D=10/30%Time=635F42D0%P=x86_64-pc-linux-gnu%r(N
SF:ULL,15,"SIN\x2015win\x20nop\x20nop\x20300")%r(GenericLines,15,"SIN\x201
SF:5win\x20nop\x20nop\x20300")%r(GetRequest,15,"SIN\x2015win\x20nop\x20nop
SF:\x20300")%r(HTTPOptions,15,"SIN\x2015win\x20nop\x20nop\x20300")%r(RTSPR
SF:equest,15,"SIN\x2015win\x20nop\x20nop\x20300")%r(DNSVersionBindReqTCP,1
SF:5,"SIN\x2015win\x20nop\x20nop\x20300")%r(Help,15,"SIN\x2015win\x20nop\x
SF:20nop\x20300")%r(SSLSessionReq,15,"SIN\x2015win\x20nop\x20nop\x20300")%
SF:r(TLSSessionReq,15,"SIN\x2015win\x20nop\x20nop\x20300");
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 49s, deviation: 1s, median: 48s

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 307.77 seconds

```

https://github.com/p0dalirius/RemoteMouse-3.008-Exploit

https://raw.githubusercontent.com/martinsohn/PowerShell-reverse-shell/main/powershell-reverse-shell.ps1

<h1> Windows Enumeration</h1>

```
SHELL> netstat -ano

Active Connections

  Proto  Local Address          Foreign Address        State           PID
  TCP    0.0.0.0:135            0.0.0.0:0              LISTENING       904
  TCP    0.0.0.0:445            0.0.0.0:0              LISTENING       4
  TCP    0.0.0.0:1978           0.0.0.0:0              LISTENING       2612
  TCP    0.0.0.0:1979           0.0.0.0:0              LISTENING       2612
  TCP    0.0.0.0:1980           0.0.0.0:0              LISTENING       2612
  TCP    0.0.0.0:3389           0.0.0.0:0              LISTENING       436
  TCP    0.0.0.0:5040           0.0.0.0:0              LISTENING       1140
  TCP    0.0.0.0:7680           0.0.0.0:0              LISTENING       4496
  TCP    0.0.0.0:49664          0.0.0.0:0              LISTENING       700
  TCP    0.0.0.0:49665          0.0.0.0:0              LISTENING       548
  TCP    0.0.0.0:49666          0.0.0.0:0              LISTENING       1084
  TCP    0.0.0.0:49667          0.0.0.0:0              LISTENING       448
  TCP    0.0.0.0:49668          0.0.0.0:0              LISTENING       1940
  TCP    0.0.0.0:49669          0.0.0.0:0              LISTENING       656
  TCP    192.168.195.199:139    0.0.0.0:0              LISTENING       4
  TCP    192.168.195.199:55698  192.168.49.195:443     ESTABLISHED     6100
  TCP    192.168.195.199:63575  192.168.49.195:443     ESTABLISHED     7968
  TCP    192.168.195.199:64878  52.226.139.180:443     ESTABLISHED     448
  TCP    192.168.195.199:65212  208.111.183.1:80       SYN_SENT        1348
  TCP    [::]:135               [::]:0                 LISTENING       904
  TCP    [::]:445               [::]:0                 LISTENING       4
  TCP    [::]:3389              [::]:0                 LISTENING       436
  TCP    [::]:7680              [::]:0                 LISTENING       4496
  TCP    [::]:49664             [::]:0                 LISTENING       700
  TCP    [::]:49665             [::]:0                 LISTENING       548
  TCP    [::]:49666             [::]:0                 LISTENING       1084
  TCP    [::]:49667             [::]:0                 LISTENING       448
  TCP    [::]:49668             [::]:0                 LISTENING       1940
  TCP    [::]:49669             [::]:0                 LISTENING       656
  UDP    0.0.0.0:123            *:*                                    5208
  UDP    0.0.0.0:1978           *:*                                    2612
  UDP    0.0.0.0:3389           *:*                                    436
  UDP    0.0.0.0:5353           *:*                                    788
  UDP    0.0.0.0:5353           *:*                                    1348
  UDP    0.0.0.0:5355           *:*                                    1348
  UDP    127.0.0.1:1900         *:*                                    4652
  UDP    127.0.0.1:11978        *:*                                    2604
  UDP    127.0.0.1:62117        *:*                                    448
  UDP    127.0.0.1:64294        *:*                                    4652
  UDP    192.168.195.199:137    *:*                                    4
  UDP    192.168.195.199:138    *:*                                    4
  UDP    192.168.195.199:1900   *:*                                    4652
  UDP    192.168.195.199:64293  *:*                                    4652
  UDP    [::]:123               *:*                                    5208
  UDP    [::]:3389              *:*                                    436
  UDP    [::1]:1900             *:*                                    4652
  UDP    [::1]:64292            *:*                                    4652
SHELL> systeminfo
                                                                                                                    
Host Name:                 REMOTE-PC                                                                                
OS Name:                   Microsoft Windows 10 Pro                                                                 
OS Version:                10.0.19042 N/A Build 19042                                                               
OS Manufacturer:           Microsoft Corporation                                                                    
OS Configuration:          Standalone Workstation                                                                   
OS Build Type:             Multiprocessor Free                                                                      
Registered Owner:          admin                                                                                    
Registered Organization:                                                                                            
Product ID:                00330-80000-00000-AA764                                                                  
Original Install Date:     6/18/2021, 4:06:17 AM                                                                    
System Boot Time:          5/17/2022, 5:09:21 PM                                                                    
System Manufacturer:       VMware, Inc.                                                                             
System Model:              VMware7,1                                                                                
System Type:               x64-based PC                                                                             
Processor(s):              1 Processor(s) Installed.                                                                
                           [01]: AMD64 Family 23 Model 1 Stepping 2 AuthenticAMD ~3094 Mhz                          
BIOS Version:              VMware, Inc. VMW71.00V.18227214.B64.2106252220, 6/25/2021
Windows Directory:         C:\WINDOWS
System Directory:          C:\WINDOWS\system32
Boot Device:               \Device\HarddiskVolume2
System Locale:             en-us;English (United States)
Input Locale:              en-us;English (United States)
Time Zone:                 (UTC-08:00) Pacific Time (US & Canada)
Total Physical Memory:     2,047 MB
Available Physical Memory: 375 MB
Virtual Memory: Max Size:  3,199 MB
Virtual Memory: Available: 902 MB
Virtual Memory: In Use:    2,297 MB
Page File Location(s):     C:\pagefile.sys
Domain:                    WORKGROUP
Logon Server:              \\REMOTE-PC
Hotfix(s):                 6 Hotfix(s) Installed.
                           [01]: KB5007289
                           [02]: KB4562830
                           [03]: KB4580325
                           [04]: KB5007186
                           [05]: KB5006753
                           [06]: KB5005699
Network Card(s):           1 NIC(s) Installed.
                           [01]: vmxnet3 Ethernet Adapter
                                 Connection Name: Ethernet0 2
                                 DHCP Enabled:    No
                                 IP address(es)
                                 [01]: 192.168.195.199
Hyper-V Requirements:      A hypervisor has been detected. Features required for Hyper-V will not be displayed.
SHELL> whoami
remote-pc\divine
SHELL> net users

User accounts for \\REMOTE-PC

-------------------------------------------------------------------------------
Administrator            DefaultAccount           divine                   
Guest                    WDAGUtilityAccount       
The command completed successfully.

SHELL> net localgroups
SHELL> net user divine
User name                    divine
Full Name                    
Comment                      
User's comment               
Country/region code          000 (System Default)
Account active               Yes
Account expires              Never

Password last set            4/20/2022 2:07:39 AM
Password expires             Never
Password changeable          4/20/2022 2:07:39 AM
Password required            Yes
User may change password     Yes

Workstations allowed         All
Logon script                 
User profile                 
Home directory               
Last logon                   5/17/2022 5:09:39 PM

Logon hours allowed          All

Local Group Memberships      *Remote Desktop Users *Users                
Global Group memberships     *None                 
The command completed successfully.

SHELL> ipconfig /all

Windows IP Configuration

   Host Name . . . . . . . . . . . . : Remote-PC
   Primary Dns Suffix  . . . . . . . : 
   Node Type . . . . . . . . . . . . : Hybrid
   IP Routing Enabled. . . . . . . . : No
   WINS Proxy Enabled. . . . . . . . : No

Ethernet adapter Ethernet0 2:

   Connection-specific DNS Suffix  . : 
   Description . . . . . . . . . . . : vmxnet3 Ethernet Adapter
   Physical Address. . . . . . . . . : 00-50-56-BF-40-F0
   DHCP Enabled. . . . . . . . . . . : No
   Autoconfiguration Enabled . . . . : Yes
   IPv4 Address. . . . . . . . . . . : 192.168.195.199(Preferred) 
   Subnet Mask . . . . . . . . . . . : 255.255.255.0
   Default Gateway . . . . . . . . . : 192.168.195.254
   DNS Servers . . . . . . . . . . . : 192.168.195.254
   NetBIOS over Tcpip. . . . . . . . : Enabled
SHELL> wmic qfe get Caption,Description,HotFixID,InstalledOn
Caption                                     Description      HotFixID   InstalledOn  

http://support.microsoft.com/?kbid=5007289  Update           KB5007289  12/7/2021    

https://support.microsoft.com/help/4562830  Update           KB4562830  6/18/2021    

https://support.microsoft.com/help/4580325  Security Update  KB4580325  6/21/2021    

https://support.microsoft.com/help/5007186  Security Update  KB5007186  12/7/2021    

                                            Update           KB5006753  12/7/2021    

                                            Security Update  KB5005699  10/19/2021  
```

Filezilla gave away this one. Inside Appdata/roaming/Filezilla there is a recentservers.xml file. Inside this there is a password saved in base64. decoded it gives you ControlFreak11.

https://www.exploit-db.com/exploits/50047
Used this to get access to run as system.