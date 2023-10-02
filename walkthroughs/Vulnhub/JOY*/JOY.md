# Nmap
```
PORT    STATE SERVICE     VERSION
21/tcp  open  ftp         ProFTPD
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| drwxrwxr-x   2 ftp      ftp          4096 Jan  6  2019 download
|_drwxrwxr-x   2 ftp      ftp          4096 Jan 10  2019 upload
22/tcp  open  ssh         Dropbear sshd 0.34 (protocol 2.0)
25/tcp  open  smtp        Postfix smtpd
|_smtp-commands: JOY.localdomain, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, ENHANCEDSTATUSCODES, 8BITMIME, DSN, SMTPUTF8
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=JOY
| Subject Alternative Name: DNS:JOY
| Not valid before: 2018-12-23T14:29:24
|_Not valid after:  2028-12-20T14:29:24
80/tcp  open  http        Apache httpd 2.4.25
|_http-title: Index of /
| http-ls: Volume /
| SIZE  TIME              FILENAME
| -     2016-07-19 20:03  ossec/
|_
|_http-server-header: Apache/2.4.25 (Debian)
110/tcp open  pop3        Dovecot pop3d
| ssl-cert: Subject: commonName=JOY/organizationName=Good Tech Pte. Ltd/stateOrProvinceName=Singapore/countryName=SG
| Not valid before: 2019-01-27T17:23:23
|_Not valid after:  2032-10-05T17:23:23
|_ssl-date: TLS randomness does not represent time
|_pop3-capabilities: TOP CAPA SASL STLS UIDL RESP-CODES PIPELINING AUTH-RESP-CODE
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
143/tcp open  imap        Dovecot imapd
|_imap-capabilities: more STARTTLS LITERAL+ have post-login SASL-IR ID OK LOGIN-REFERRALS capabilities ENABLE Pre-login LOGINDISABLEDA0001 IMAP4rev1 listed IDLE
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=JOY/organizationName=Good Tech Pte. Ltd/stateOrProvinceName=Singapore/countryName=SG
| Not valid before: 2019-01-27T17:23:23
|_Not valid after:  2032-10-05T17:23:23
445/tcp open              Samba smbd 4.5.12-Debian (workgroup: WORKGROUP)
465/tcp open  smtp        Postfix smtpd
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=JOY
| Subject Alternative Name: DNS:JOY
| Not valid before: 2018-12-23T14:29:24
|_Not valid after:  2028-12-20T14:29:24
|_smtp-commands: JOY.localdomain, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, ENHANCEDSTATUSCODES, 8BITMIME, DSN, SMTPUTF8
587/tcp open  smtp        Postfix smtpd
|_ssl-date: TLS randomness does not represent time
|_smtp-commands: JOY.localdomain, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, ENHANCEDSTATUSCODES, 8BITMIME, DSN, SMTPUTF8
| ssl-cert: Subject: commonName=JOY
| Subject Alternative Name: DNS:JOY
| Not valid before: 2018-12-23T14:29:24
|_Not valid after:  2028-12-20T14:29:24
993/tcp open  ssl/imap    Dovecot imapd
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=JOY/organizationName=Good Tech Pte. Ltd/stateOrProvinceName=Singapore/countryName=SG
| Not valid before: 2019-01-27T17:23:23
|_Not valid after:  2032-10-05T17:23:23
|_imap-capabilities: AUTH=PLAINA0001 LITERAL+ more have SASL-IR post-login OK LOGIN-REFERRALS ID ENABLE capabilities Pre-login IMAP4rev1 listed IDLE
995/tcp open  ssl/pop3    Dovecot pop3d
|_pop3-capabilities: TOP CAPA SASL(PLAIN) USER UIDL RESP-CODES PIPELINING AUTH-RESP-CODE
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=JOY/organizationName=Good Tech Pte. Ltd/stateOrProvinceName=Singapore/countryName=SG
| Not valid before: 2019-01-27T17:23:23
|_Not valid after:  2032-10-05T17:23:23
MAC Address: 08:00:27:CB:66:BD (Oracle VirtualBox virtual NIC)
Device type: general purpose
Running: Linux 3.X|4.X
OS CPE: cpe:/o:linux:linux_kernel:3 cpe:/o:linux:linux_kernel:4
OS details: Linux 3.2 - 4.9
Network Distance: 1 hop
Service Info: Hosts: The,  JOY.localdomain, 127.0.1.1, JOY; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
|_nbstat: NetBIOS name: JOY, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| smb2-time: 
|   date: 2023-09-16T13:34:17
|_  start_date: N/A
|_clock-skew: mean: -2h40m00s, deviation: 4h37m07s, median: -1s
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.5.12-Debian)
|   Computer name: joy
|   NetBIOS computer name: JOY\x00
|   Domain name: \x00
|   FQDN: joy
|_  System time: 2023-09-16T21:34:17+08:00
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required

TRACEROUTE
HOP RTT     ADDRESS
1   0.20 ms 192.168.211.29
```
# PORT 21
![[walkthroughs/Vulnhub/JOY*/IMG/Pasted image 20230912021741.png]]
while these were the contents:
![[walkthroughs/Vulnhub/JOY*/IMG/Pasted image 20230916191035.png]]

# version_control
in the http port while bruteforcing directories we found a file called .hgtags , inside that file was the version names , also in the ftp folder we found the patrick's home directory where we found out the version_control file , so with the help of telnet we try to copy that file to ftp user so that we can get it:

![[walkthroughs/Vulnhub/JOY*/IMG/Pasted image 20230916201119.png]]
copy from:
```
site cpfr /home/patrick/version_control 
```
copy to:
```
site cpto /home/ftp/upload/version_control
```
it will be inside the upload folder in ftp . file had:
![[walkthroughs/Vulnhub/JOY*/IMG/Pasted image 20230916201536.png]]
