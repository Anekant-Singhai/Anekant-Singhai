We need to try every parameter , so I tried categories , no luck , the lab said about the cookie , and cookie is only made when session is created , so we go to the login functionality and open our burp there:
![[Pasted image 20230626161929.png]]

we see that there is this parameter vulnerable
when we do the order by clause we see that the column 1 is only there , how do we get to this opinion? :
there in question it said that if the query was right we'll get that string "welcome back" and in order by 2 we don't:
![[Pasted image 20230626162144.png]]

so this param in vuln to sql

since it is blind and we only are dependent on welcome back , we will have to predict the name if it is admin or administrator:

```
' and select username from users where user LIKE '%a'  --
```

and this gives : 
![[Pasted image 20230626162654.png]]

so we are sure we have a user starting with 'a'
then we make the query like this:
`GFp67KmDlsKnePgU' and select username from users where username like 'adminis%' --`
![[Pasted image 20230627165751.png]]

so we get to know that the value is administrator , so we just need to get the password:
