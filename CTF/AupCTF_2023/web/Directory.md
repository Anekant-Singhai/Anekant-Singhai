This challenge contains the directory from 1 - 1000 listed and when we visit we see "no flag for you" so I guess one of them has this flag:
![[Pasted image 20230624222644.png]]
![[Pasted image 20230624222715.png]]

![[Pasted image 20230624222726.png]]

so we need to visit every end point and look for flag , pretty annoying right?

what if we automate it and look for unique response , we can do this via tool called burpsuite , actually this is a really important tool in any pentester and bug-hunter's arsenal , we'll use it's intruder function

we set up the position:
![[Pasted image 20230624223139.png]]
then we define the payload to bruteforce:
![[Pasted image 20230624223335.png]]

we get the flag:
![[Pasted image 20230625000549.png]]
aupCTF{d1r3ct0r13s-tr1v14l-fl4g}