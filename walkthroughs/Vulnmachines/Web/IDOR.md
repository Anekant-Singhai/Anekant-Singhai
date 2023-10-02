The site has two options:
![[walkthroughs/Vulnmachines/Web/IMG/Pasted image 20230801153247.png]]
and the catch lies in the second one , to read pdf 
we can read only from 1 to 1500 id's as the error said , when tried a higher number , so I intercept the request and go to burp to brtfrc some and got this:

![[walkthroughs/Vulnmachines/Web/IMG/Pasted image 20230801153123.png]]
When we try both 386 and 15 we get success on 15:
![[walkthroughs/Vulnmachines/Web/IMG/Pasted image 20230801153517.png]]
`vnm{"Congratulations!! Y0U H4VE FOUND ID0R"}`
