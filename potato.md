# Potato

## Nmap

```
sudo nmap 192.168.211.101 -p- -sS -sV

PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.1 (Ubuntu Linux; protocol 2.0)
80/tcp   open  http    Apache httpd 2.4.41 ((Ubuntu))
2112/tcp open  ftp     ProFTPD
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Going for the low hanging fruit I will dive straight into FTP on port 2112. On connection with anonymous login we see two files. I grab the index.php.bak and welcome.msg. I also tried file upload and was unsuccessful.

![](<../../../.gitbook/assets/image (1396).png>)

The contents of welcome.msg is shown below:

```
Welcome, archive user %U@%R !

The local time is: %T
```

Not too sure what to do with this just yet but looks like some variables to me. Index.php.bak is more interesting however.

![](<../../../.gitbook/assets/image (1397).png>)

Reading through this the string 'strcmp' was interesting and it was a string I was not familier with or recognized as seeing before. After Googling the string for exploits I came across an interesting post relating to a CTF challenge where someone came across the same string.

{% embed url="https://www.doyler.net/security-not-included/bypassing-php-strcmp-abctf2016" %}

Take mind of the following snippet from the blog post.

![https://www.doyler.net/security-not-included/bypassing-php-strcmp-abctf2016](<../../../.gitbook/assets/image (1398).png>)

Running `dirsearch.py` against the target on port 80 reveals the /admin/ directory.

![](<../../../.gitbook/assets/image (1399).png>)

Checking out the page source we see some familiarity with the form action and the index.php.bak we came across earlier.

![](<../../../.gitbook/assets/image (1400).png>)

Looking back at just the PHP code from the index.php.bak we can see where the _strcmp_ string comes into play.

![](<../../../.gitbook/assets/image (1401).png>)

We know that from the code the admin name exists and that the password is changed regularly base don the comment. Going by this we can try the _strcmp_ bypass on the password value.

I caught a login request with Burp and set the password value to `[]==""`

![](<../../../.gitbook/assets/image (1404).png>)

After sending the request we get confirmation of login.

Once logged in we can follow the link on the dashboard to the admin area.

![](<../../../.gitbook/assets/image (1405).png>)

Heading over to logs we are given the option to pick from a subset of lots to retrieve from the webserver.

![](<../../../.gitbook/assets/image (1406).png>)

Retrieving a log and catching the request in `Burpsuite` shows an interesting parameter.

![](<../../../.gitbook/assets/image (1407).png>)

Changing the file request to match `../../../../../../etc/passwd` allows for us to perform LFI and read the contents of `/etc/passwd`.

![](<../../../.gitbook/assets/image (1409).png>)

![](<../../../.gitbook/assets/image (1408).png>)

Looking at the results we can see a hash for the user webadmin. Checking this hash against the `hashcat` [examples page](https://hashcat.net/wiki/doku.php?id=example\_hashes) shows this is a MD5crypt hash. I was then able to crack the hash on my Windows machine with `hashcat`.

![](<../../../.gitbook/assets/image (1410).png>)

We now have the credentials `webadmin:dragon`. I was then able to use these to login to SSH with.

```
ssh webadmin@192.168.211.101
```

![](<../../../.gitbook/assets/image (1411) (1).png>)

Checking `sudo -l` shows we can run the binary `/bin/nice` on any file inside `/notes/*` due to the wildcard.

![](<../../../.gitbook/assets/image (1412).png>)

Inside the notes directory we have two files. One that clears the screen and the other runs the `id` command. With no writeable permissions on this folder we are unable to edit or replace the files and unable to use them for privilege escalation.

As the sudoers directory. has a wildcard in /notes/\* we can abuse this to run a file with sudo permissions outside of the notes directory.

See below for more information:

{% embed url="https://blog.compass-security.com/2012/10/dangerous-sudoers-entries-part-4-wildcards/" %}

We can create a shell.sh file in our home directory and execute with sudo.

```
touch shell.sh
echo '/bin/sh' > shell.sh
chmod 777 shell.sh
```

Once completed then run the following command to gain a root shell.

```
sudo /bin/nice /notes/../../../../home/webadmin/shell.sh
```

![](<../../../.gitbook/assets/image (1413).png>)
