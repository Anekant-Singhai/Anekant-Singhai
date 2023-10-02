nmap:
```
> 22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 35:39:d4:39:40:4b:1f:61:86:dd:7c:37:bb:4b:98:9e (ECDSA)
|_  256 1a:e9:72:be:8b:b1:05:d5:ef:fe:dd:80:d8:ef:c0:66 (ED25519)
> 80/tcp open  http    nginx 1.18.0 (Ubuntu)
|_http-server-header: nginx/1.18.0 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
```

When we visit the site we can see that they give us the link and redirect us to:
![[walkthroughs/HACKTHEBOX/Keeper/IMG/Pasted image 20230819162137.png]]
default creds:
`root:password`
![[walkthroughs/HACKTHEBOX/Keeper/IMG/Pasted image 20230819170338.png]]
![[walkthroughs/HACKTHEBOX/Keeper/IMG/Pasted image 20230819170513.png]]
when I tried the password : got the shell:
![[walkthroughs/HACKTHEBOX/Keeper/IMG/Pasted image 20230819170617.png]]
Two files were there: `.dmp` and `.kdbx`  got them via:
![[walkthroughs/HACKTHEBOX/Keeper/IMG/Pasted image 20230819170729.png]]
via: `python3 -m http.server -b 10.10.11.227`
![[walkthroughs/HACKTHEBOX/Keeper/IMG/Pasted image 20230819173039.png]]
OR 
![[walkthroughs/HACKTHEBOX/Keeper/IMG/Pasted image 20230819172025.png]]
Opened the file via tool on github
This file had a cve and this was the POC of the file 
via a tool: `https://github.com/CMEPW/keepass-dump-masterkey`
![[walkthroughs/HACKTHEBOX/Keeper/IMG/Pasted image 20230819180030.png]]
When searched this gibberish
It was a name of some dessert:
![[walkthroughs/HACKTHEBOX/Keeper/IMG/Pasted image 20230819180452.png]]
`rødgrød med fløde` -> this is the password for the kepass file
open in `kepassxc` tool in kali to open the `.kdbx` file
when we go in network tab of it we see an ssh key:
![[walkthroughs/HACKTHEBOX/Keeper/IMG/Pasted image 20230819180725.png]]

copy the putty key into a file and run this command:
`puttygen kepass.txt -O private-openssh -o id_rsa`
and BOOM! we get the root