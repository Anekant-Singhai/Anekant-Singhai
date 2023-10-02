* First we authenticate with our user and get ahead:

* then we observe how it is fetching the user details
![[walkthroughs/Webgoat_2023/Broken_access_control/IMG/Pasted image 20230824162736.png]]
like this:
![[walkthroughs/Webgoat_2023/Broken_access_control/IMG/Pasted image 20230824162822.png]]
can we try this for change and chech the user profile:
![[walkthroughs/Webgoat_2023/Broken_access_control/IMG/Pasted image 20230824162859.png]]
`WebGoat/?userId=2342384`
nope:
![[walkthroughs/Webgoat_2023/Broken_access_control/IMG/Pasted image 20230824162947.png]]
so instead of profile can we give id directly:
![[walkthroughs/Webgoat_2023/Broken_access_control/IMG/Pasted image 20230824163023.png]]
nope:
![[walkthroughs/Webgoat_2023/Broken_access_control/IMG/Pasted image 20230824163050.png]]
parameter:
`WebGoat/IDOR/profile?userId=2342384` NOPE
but what if we directly give the id?:
`WebGoat/IDOR/profile/2342384` -> and it works!
![[walkthroughs/Webgoat_2023/Broken_access_control/IMG/Pasted image 20230930130521.png]]
