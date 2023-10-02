starting the server sets up the website with only username panel and after putting it in , says "only admin can see the flag"
classic broken authentication challenge 
The basic approach is to look for the ways it can be used to authenticate the admin 
like 
* Passwords
* 2FA
* cookies
* tokens
* session id etc...
since password and 2FA are out of picture due to the nature of application set , so there must be some cookie

and we were right:
![[Pasted image 20230619141825.png]]
a JWT token 
usually they are encoded and all , look for that ,maybe we can find a way to forge a cookie with admin's signature on it
basic encoding is always base64:
`eyJhbGciOiJNRDVfSE1BQyJ9.eyJ1c2VybmFtZSI6ImJhYmUifQ.MGkBTIskLxGyXZ9RbduW-A`

It has 3 parts:
* eyJhbGciOiJNRDVfSE1BQyJ9
* eyJ1c2VybmFtZSI6ImJhYmUifQ
* MGkBTIskLxGyXZ9RbduW-A
![[Pasted image 20230619143938.png]]

Look here  on jwt.io saying it has this MD5 encryption also this blue string right here is different everytime we create a user so even if we change the middle part to admin and encode it we won't be able to predict this string so we need to do something else

