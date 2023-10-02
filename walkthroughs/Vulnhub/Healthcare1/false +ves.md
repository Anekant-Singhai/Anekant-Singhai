the email parameter taking the email looks like this:
![[Pasted image 20230623105046.png]]
maybe it's vulnerable:
yep as we thought , a bit of manual trial and error we get this blind sqli:
![[Pasted image 20230623105204.png]]

as when we pass the quote only it breaks the query and resets the timer while when we join the query we see the timer is intact and won't reset
but after using sqlmap it says not vulnerable