	# Hijack A session
***
## Concept
Application developers who develop their own session IDs frequently forget to incorporate the complexity and randomness necessary for security. If the user specific session ID is not complex and random, then the application is highly susceptible to session-based brute force attacks.
## Goals
Gain access to an authenticated session belonging to someone else.
***
![[walkthroughs/Webgoat_2023/Broken_access_control/IMG/Pasted image 20230824155427.png]]
This is the hijack_cookie:
![[walkthroughs/Webgoat_2023/Broken_access_control/IMG/Pasted image 20230824155633.png]]
These were the cookies that were being set on different creds:
`7439744012046070660-1692872729128`
`7439744012046070661-1692872886745`
`7439744012046070662-1692872948186`
`7439744012046070663-1692872971382`
authenticated user:
`7439744012046070658-1692872695023`

