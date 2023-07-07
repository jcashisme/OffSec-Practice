---
description: Pg Practice Payday writeup
---

# Payday

![](<../../../.gitbook/assets/image (701).png>)

## Nmap

```
sudo nmap 192.168.189.39 -p- -sS        

PORT    STATE SERVICE
22/tcp  open  ssh
80/tcp  open  http
110/tcp open  pop3
139/tcp open  netbios-ssn
143/tcp open  imap
445/tcp open  microsoft-ds
993/tcp open  imaps
995/tcp open  pop3s
```

## HTTP

Default root page on port 80 takes us to a store page for CS-cart.

![](<../../../.gitbook/assets/image (702).png>)

We can see from this page that this is a demo page that is currently running. With that we can try some common login credentials.

I tried admin:admin and was able to login to the web application. After logging in I could not see much had changed and was unable to find anything interesting. I quick Google search produces a potential authenticated RCE exploit.

{% embed url="https://www.exploit-db.com/exploits/48891" %}

![](<../../../.gitbook/assets/image (703).png>)

I could not find anywhere to upload to the file manager and after a short while decided to not proceed with this exploit for the time being.

## LFI

After further research on Google we also have a potential for a LFI exploit on CS-cart.

{% embed url="https://www.exploit-db.com/exploits/48890" %}

![](<../../../.gitbook/assets/image (704) (1).png>)

Lets test one of these and see if we can read /etc/passwd.

```
http://192.168.189.39/classes/phpmailer/class.cs_phpmailer.php?classes_dir=../../../../../../../../../../../etc/passwd%00
```

![](<../../../.gitbook/assets/image (705) (1).png>)

We have confirmed LFI and have read the passwd file. We can take note of the user 'patrick' which is the last entry on the file.

I tried reading other common LFI paths and could not find further information. The users patrick did not appear to have any SSH keys in /home/patrick/.ssh/

## SSH Bruteforce

With a known users on the system we can try bruteforcing pop3 / imap over telnet and we can try `SSH`. I first set up `crackmapexec` to brute force SSH.

```
crackmapexec ssh 192.168.189.39 -u patrick -p /usr/share/wordlists/rockyou.txt
```

We soon find a valid set of credentials. `patrick:patrick.`

![](<../../../.gitbook/assets/image (706).png>)

## Low Privilege Access

We now attempt to login with `SSH`.

```
ssh patrick@192.168.189.39
```

![](<../../../.gitbook/assets/image (707).png>)

## Privilege Escalation

Privilege escalation proved to be exceptionally easy on this machine. Before uploading any enumeration scripts I always perform a quick check on what we can run as sudo with `sudo -l`.

![](<../../../.gitbook/assets/image (708) (1).png>)

Here we can see patrick can run sudo as any user on any command. This is a quick win as we can run sudo as root on /bin/bash to spawn a root shell.

![](<../../../.gitbook/assets/image (709).png>)
