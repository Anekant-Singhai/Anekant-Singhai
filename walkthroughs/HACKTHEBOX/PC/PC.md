scan:
```
Open 10.10.11.214:22
Open 10.10.11.214:50051
```
There were two ports that were open.
when we connect to 50051 with nc we get garbled text
It's a grpc service running in the port , how do I know?
connect with nc and see this error:
![[walkthroughs/HACKTHEBOX/PC/IMG/Pasted image 20230826131824.png]]
we wait for a bit and we get this error
when searching on the port 50051 I stumbled upon this blog:
`https://www.huxxit.com/index.php/2023/05/25/notes-on-pentesting-ctf-hacking-a-grpc-application/`
so I did what this brother said: download the tool and run as he said
we first list all the things:
![[walkthroughs/HACKTHEBOX/PC/IMG/Pasted image 20230826132703.png]]
![[walkthroughs/HACKTHEBOX/PC/IMG/Pasted image 20230826132729.png]]
we use grpcui to get further:
![[walkthroughs/HACKTHEBOX/PC/IMG/Pasted image 20230826133025.png]]
when we invoke the request we see:
![[walkthroughs/HACKTHEBOX/PC/IMG/Pasted image 20230826133103.png]]
we try register a user:
![[walkthroughs/HACKTHEBOX/PC/IMG/Pasted image 20230826133715.png]]
then we login:
![[walkthroughs/HACKTHEBOX/PC/IMG/Pasted image 20230826133741.png]]
and the token at last:
```
b'eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VyX2lkIjoidGVzdCIsImV4cCI6MTY5MzA0NzM4MX0.QkbZj2kouXh-mx2uHP2ks55FjTOj88zgwt6MoN__pqU'
```
![[walkthroughs/HACKTHEBOX/PC/IMG/Pasted image 20230826134737.png]]
we intercept the request and send to repeater:
![[walkthroughs/HACKTHEBOX/PC/IMG/Pasted image 20230826134801.png]]
I had to register once more as id was deleted after some time:
![[walkthroughs/HACKTHEBOX/PC/IMG/Pasted image 20230826135418.png]]
what if make id=1:
![[walkthroughs/HACKTHEBOX/PC/IMG/Pasted image 20230826135508.png]]
and also single quote is breaking it:
![[walkthroughs/HACKTHEBOX/PC/IMG/Pasted image 20230826135622.png]]
we feed it to sqlmap via:
```
sqlmap -r <request.txt> -p id
```
we get the usernames and passwords:
`sau`-`HereIsYourPassWord1431`
`admin`-`admin`
so admin admin was of site , we could though login as sau
we are in baby!!!