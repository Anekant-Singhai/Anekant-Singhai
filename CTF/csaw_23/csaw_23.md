
# INtro
## my first pwnie
![[CTF/csaw_23/IMG/Pasted image 20230916004250.png]]

![[CTF/csaw_23/IMG/Pasted image 20230916004330.png]]
## puffin
looking at the code says the buffer is of 44 bytes and after that it overflows and the local_c is overwritten by it:
![[CTF/csaw_23/IMG/Pasted image 20230916012507.png]]
![[CTF/csaw_23/IMG/Pasted image 20230916012353.png]]

# Web:
## smug-dino
when we go to the hint endpoint :
it says to give us the server version and name we'll get the hint:
![[CTF/csaw_23/IMG/Pasted image 20230916015119.png]]
we can see the hint , when we directly google the hint:
![[CTF/csaw_23/IMG/Pasted image 20230916015253.png]]
It's vulnerable to `http-request-smuggling`
so we try that:


### philantrophist
we can see that when we register , the info goes like this:
![[CTF/csaw_23/IMG/Pasted image 20230916022455.png]]
and loads like this:
![[CTF/csaw_23/IMG/Pasted image 20230916022527.png]]
though we can change these:
![[CTF/csaw_23/IMG/Pasted image 20230916022543.png]]
so when we do , the POST request is sent again , what if we do `member:true`?:
![[CTF/csaw_23/IMG/Pasted image 20230916022430.png]]
success!!!
we start seeing a new endpoint:
![[CTF/csaw_23/IMG/Pasted image 20230916022645.png]]
but....the jwt cookie:
![[CTF/csaw_23/IMG/Pasted image 20230916023112.png]]
