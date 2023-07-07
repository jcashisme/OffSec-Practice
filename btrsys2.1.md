# BTRSys2.1

## Nmap

```
sudo nmap 192.168.120.50 -p- -sS -sV

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.1 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```

I was able to login with FTP on anonymous login. However, no files are listed and was unable to upload anything. Moving onto port 80 the default page take us to a GIF:

![](<../../../.gitbook/assets/image (1100).png>)

Running `dirsearch.py` against the the target machine reveals robots.txt

```
python3 dirsearch.py -u http://192.168.120.50/ -w /usr/share/seclists/Discovery/Web-Content/raft-large-files.txt -r -t 60 --full-url 
```

![](<../../../.gitbook/assets/image (1101).png>)

![](<../../../.gitbook/assets/image (1102).png>)

Which advises on the Wordpress directory as being allowed.

![](<../../../.gitbook/assets/image (1103).png>)

Running `WPScan` against the target soon reveals two users of which one is admin.

```
 wpscan --url http://192.168.120.50/wordpress/ -t 40 -e u1-1000 --passwords /usr/share/wordlists/rockyou.txt --force 
```

![](<../../../.gitbook/assets/image (1104).png>)

`WPScan` reports the credentials `admin:admin` are valid. Heaving over to /wordpress/wp-admin we can sign in with these credentials to access the Wordpress dashboard.

![](<../../../.gitbook/assets/image (1105).png>)

We now head over to Appearance > Editor and select the index.php. From here we can remove the PHP contents and replace it with a [PHP reverse shell](https://github.com/pentestmonkey/php-reverse-shell).

![](<../../../.gitbook/assets/image (1106).png>)

Once updated we can set a `netcat` listener and then reload the main page on:[ http://192.168.120.50/wordpress/index.php](http://192.168.120.50/wordpress/). This should hang the page and we should then receive a reverse shell.

![](<../../../.gitbook/assets/image (1107) (1).png>)

I then transferred over `linpeas` to the target machine and soon after running linpeas identifies database credentials in /var/www/html/wordpress/wp-config.php.

![](<../../../.gitbook/assets/image (1108).png>)

From here we can connect to MYSQL and then enter the Wordpress database. We can then extract users information from the table wp\_users as shown below.

```bash
# Connect to mysql
mysql -u root -p
rootpassword!

# Extract user information from Wordpress
show databases;
select wordpress;
select * from wp_users;
```

![](<../../../.gitbook/assets/image (1109).png>)

I was unable to crack the root MD5 hash so run I it against some online databases and got a hit on [crackstation.net](https://crackstation.net).

![](<../../../.gitbook/assets/image (1110).png>)

We now have the credentials `root:roottoor`. We can `su` to the root user for a root shell.

![](<../../../.gitbook/assets/image (1111).png>)
