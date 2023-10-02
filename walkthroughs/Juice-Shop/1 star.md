# 3-Star FTP endpoint
check for robots.txt -> we see that one endpoint is not allowed: /ftp
when we visit it and see there's a folder called quarantine -> looking inside it we see malware files for different arch. and os
so not to meddle with it for now , we also check if we can send or put a file onto that endpoint via CURL it is actually a good practice to check:
![[Pasted image 20230715182741.png]]
we see PUT method is allowed , so shall we put a file?:
![[Pasted image 20230715182938.png]]
we can read the files with pdf and md extensions so there was one so we read it
![[Pasted image 20230715183043.png]]
we also see : legal.md and announcement_encrypted.md -> the enc. file looks juicy so we download it
On googling the first has value we get:
![[Pasted image 20230715183614.png]]

we also get this .kdbx file which on further analysis we get to know that it is a keypass database file , we can decrypt it via :
```
keepass2john incident-support.kdbx > keypass.txt
```
maybe we can find the key for that above hashes
we can decrypt this file via john with the wordlist rockyou
```
john --wordlist=/usr/share/wordlists/rockyou.txt keypass.txt
```
and we get the password: `..*7¡Vamos!`

# 1 star confidential document
we can see that in this endpoint we are allowed to read aquisitions.md which is a confidential document

# 1 star improper encoding
This picture is not being displayed , why?
![[walkthroughs/Juice-Shop/IMG/Pasted image 20230908160110.png]]
look at the name : it has `#` in the name , this should be url-encoded so that we get the image to displayed ,give `%23` instead of `#` , then it'll be a url
![[walkthroughs/Juice-Shop/IMG/Pasted image 20230908160217.png]]
![[walkthroughs/Juice-Shop/IMG/Pasted image 20230908160445.png]]


# Outdated Allowlist
Inside the main.js file , we can see that there are redirect urls:
![[walkthroughs/Juice-Shop/IMG/Pasted image 20230908170443.png]]
copy the bitcoin address `/redirect?to=https://blockchain.info/address/1AbKfgvw9psQ41NbLi8kufDQTezwG8DRZm`
and add to  the url and let it redirect , challenge is solved

# DRY principle
DRY means don't repeat yourself , meaning never repeat anything , but what are repeating:
![[walkthroughs/Juice-Shop/IMG/Pasted image 20230908173712.png]]
but we can't register without filling it, so we intercept the request and delete the repeat password.
![[walkthroughs/Juice-Shop/IMG/Pasted image 20230908174215.png]]
# data scraper:
go to prometheus's docs and there look for configuration docs , search for scrape and  we can see :
![[walkthroughs/Juice-Shop/IMG/Pasted image 20230908175239.png]]
