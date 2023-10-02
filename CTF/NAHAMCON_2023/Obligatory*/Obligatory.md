***
Every Capture the Flag competition has to have an obligatory to-do list application, right???
***
I signed up and login successfully 
when I create my first task , and tick mark it , it says success 
![[Pasted image 20230617155245.png]]
meaning a request might have gone , when I unchecked it , the request was gone again , so when I checked on burp it said:
![[Pasted image 20230617155448.png]]

For the first task , why does it say id:2 , interesting
when I changed the number from 2 to 3:
![[Pasted image 20230617155626.png]]
I tried same with -1  , same response  , so It checks for request geniunely , I tried 1 also , out of curiosity , same response , but when I tried 0....:
it said `missing prameters`
![[Pasted image 20230617155815.png]]

so what parameters does it needs? admin?: nope since the name says "obligatory" try that , nope , tried active:true , delete:false
but no success