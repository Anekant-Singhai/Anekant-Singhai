# NMAP
```
PORT      STATE SERVICE VERSION
9999/tcp  open  abyss?
| fingerprint-strings: 
|   NULL: 
|     _| _| 
|     _|_|_| _| _|_| _|_|_| _|_|_| _|_|_| _|_|_| _|_|_| 
|     _|_| _| _| _| _| _| _| _| _| _| _| _|
|     _|_|_| _| _|_|_| _| _| _| _|_|_| _|_|_| _| _|
|     [________________________ WELCOME TO BRAINPAN _________________________]
|_    ENTER THE PASSWORD
10000/tcp open  http    SimpleHTTPServer 0.6 (Python 2.7.3)
```

# port 80
![[walkthroughs/Vulnhub/BRAINPAN/img/Pasted image 20230926171209.png]]
there was a '/bin/' directory , there was a brainpan.exe file
strings the file and :
![[walkthroughs/Vulnhub/BRAINPAN/img/Pasted image 20230926171456.png]]
we see a potential candidate to password:
![[walkthroughs/Vulnhub/BRAINPAN/img/Pasted image 20230926171547.png]]
but it's closing immediately so we need to keep it open