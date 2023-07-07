# Photographer

## Nmap

```
sudo nmap 192.168.121.76 -p- -sS -sV           

PORT     STATE SERVICE     VERSION
22/tcp   open  ssh         OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
80/tcp   open  http        Apache httpd 2.4.18 ((Ubuntu))
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
8000/tcp open  http        Apache httpd 2.4.18 ((Ubuntu))
Service Info: Host: PHOTOGRAPHER; OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

I started this box with checking SMB. First I ran the following command with `smbclient` to list shares.

```
smbclient -L "//192.168.121.76/" -U "guest"%
```

![](<../../../.gitbook/assets/image (1501).png>)

Taking note of the non default share 'sambashare' I connected with `smbclient` again, this time specifying that share that has been identified. After connecting I tried to upload with the `put` command and was given 'access denied. I grabbed the two files on the share and then disconnected.

```
smbclient "//192.168.121.76/sambashare" -U "guest"%
```

![](<../../../.gitbook/assets/image (1502).png>)

Checking the contents of mailsent.txt reveals a user 'daisa'. We also have the sender who is called 'agi' who may be a potential user. The comment regarding the secret is interesting and something we may be able to use later.

![](<../../../.gitbook/assets/image (1503).png>)

I checked the contents of wordpress.bkg.zip and was unable to identify any interesting information. Unfortunately the archive file did not contain a wp-config.php file which normally contains database information.

Checking port 80 we come to a website called 'photographer'. I ran directory enumeration on this page and enumerated manually and was unable to discover anything of interest.

![](<../../../.gitbook/assets/image (1504) (1).png>)

Moving over to port 8000 we land on what is assumed to be the web page which was mention in Agi's email to Daisa. Noting from the footer the website is built with Koken.

![](<../../../.gitbook/assets/image (1505).png>)

I checked manually for common directories and was presented with a login page under the directory /admin.

![](<../../../.gitbook/assets/image (1507).png>)

Knowing Daisa likely has account on this as per the email we grabbed from SMB I entered the email address as 'daisa@photographer.com'. For the password I tried 'babygirl' which was hinted from the email as well. Fortunately this gave us access.

![](<../../../.gitbook/assets/image (1508).png>)

Using the settings tab we take note we are run on version 0.22.24.

![](<../../../.gitbook/assets/image (1509).png>)

A simple search with searchsploit reveals 'Arbitrary File Upload (Authenticated)' for this particular version.

![](<../../../.gitbook/assets/image (1510).png>)

The exploit for this has been linked below.

{% embed url="https://www.exploit-db.com/exploits/48706" %}

As per the exploit instructions I went to 'Import Content' in the bottom right of the page.

![](<../../../.gitbook/assets/image (1511).png>)

I then selected a PHP shell which I had renamed to phpshell.php.jpg in order to bypass the upload filter. After selecting and uploading I caught the upload request with `Burpsuite`.

![](<../../../.gitbook/assets/image (1512).png>)

On the highlighted line above I removed '.jpg' from the end of the file name. After forwarding the request go back to Koken and look under the 'Date Published' section and find the uploaded file. Highlight the file and select 'Download File' on the right.

![](<../../../.gitbook/assets/image (1513).png>)

This will execute the PHP reverse shell and we get a shell on our `netcat` listener.

![](<../../../.gitbook/assets/image (1514).png>)

I then used the following command to make the shell more usable.

```
python -c 'import pty; pty.spawn("/bin/bash")'
```

After this I uploaded `linpeas` to the target machine and executed. Linpeas soon picking up the binary `php7.2` has the SUID bit set.

![](<../../../.gitbook/assets/image (1516).png>)

Checking [GTFOBins](https://gtfobins.github.io/gtfobins/php/) for SUID exploits we see the following:

![](<../../../.gitbook/assets/image (1515).png>)

Using this information I ran the following command to gain shell as root.

```
/usr/bin/php7.2 -r "pcntl_exec('/bin/sh', ['-p']);"
```

![](<../../../.gitbook/assets/image (1517).png>)

Keywords

#### \<binary:php>
