Nmap:
```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 cc:f1:63:46:e6:7a:0a:b8:ac:83:be:29:0f:d6:3f:09 (RSA)
|   256 2c:99:b4:b1:97:7a:8b:86:6d:37:c9:13:61:9f:bc:ff (ECDSA)
|_  256 e6:ff:77:94:12:40:7b:06:a2:97:7a:de:14:94:5b:ae (ED25519)
80/tcp open  http    nginx 1.18.0 (Ubuntu)
|_http-server-header: nginx/1.18.0 (Ubuntu)
|_http-title: Did not follow redirect to http://download.htb
```
![[walkthroughs/HACKTHEBOX/Download/IMG/Pasted image 20230810010841.png]]
we can upload the files like this:
![[walkthroughs/HACKTHEBOX/Download/IMG/Pasted image 20230810011109.png]]
