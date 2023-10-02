# Natas 0
```
http://natas0.natas.labs.overthewire.org
```

was in source code - g9D9cREhslqBKtcA2uocGHPfMZVzeFK6
as the hint suggested

# Natas 1
again in the src code , via ctrl+U:
h4ubbcXrWqsTo7GGnnUMLppXbOogfBZ7

# Natas 2
in the src code there's this :
```
<img src="[files/pixel.png](http://natas2.natas.labs.overthewire.org/files/pixel.png)">
```
so let's look at this dir , dir listing is enabled :
```
# username:password
alice:BYNdCesZqW
bob:jw2ueICLvT
charlie:G5vCxkVV3m
natas3:G6ctbMJ5Nb4cbFwhpMPSvxGHhQ7I6W8Q
eve:zo4mJWyNj2
mallory:9urtcpzBmH
```

# Natas 3
in the src code:
`<!-- No more information leaks!! Not even Google will find it this time... -->`

so there's this file called robots.txt that lists all the files that google's spider won't crawl
we get this: `/s3cr3t/`
`natas4:tKOcJIbzM4lTs8hbCmzn5Zr4434fGZQm`

# Natas 4
just use the referer header , this header tells that where the request is coming from :
`Z0NsrtIkJoKALBCLi5eqFfcRN82Au2oD`
first refresh the page via refresh button then copy the link above as it includes index.php also to execute logic
use it via `curl -H 'referer: <natas5>' <natas4>`

# Natas 5
think how they login :
* either as login page 
* as cookie
and other things ...
so here it was the cookie just set the login cookie to 1 from devtools via f12 and refresh:
`fOIvE0MDtPTgRhqmmvvAOt2EfXR6uQgR`

# Natas 6
go to source code :
there it says include-source.html , go to it and it says a code that include `includes/secret.inc` visit it and below that code says if that file's content is equal to what we sent via POST request give flag
`jmxSiH3SP6Sonf8dv66ng8v1cIEdjXWr`

# Natas 7
view code: they gave us the file which contains the password `/etc/natas_webpass/natas8`
so classic LFI or path-traversal vuln {both are different in LFI files execute while in path-traversal we can only read} if don't know google them
the payload in the link goes:
`http://natas7.natas.labs.overthewire.org/index.php?page=../../../../etc/natas_webpass/natas8`
`a6bZCNYwdKqN5cGP11ZdtPg0iImQQhAB`

# Natas 8
the password is: `3d3d516343746d4d6d6c315669563362`
so we need to look at the src code: it is using 
* base64
* strrev
* bintohex
so -> decode hex -> reverse -> base64 decode
decode from hex:
==QcCtmMml1ViV3b

after reversing and decoding from base64:
oubWYf2kBq
now we decode 
`Sda6t0vkOPkM8YeOZkAGVhFoaplvlJFd`

# Natas 9
; cat /etc/natas_webpass/natas10