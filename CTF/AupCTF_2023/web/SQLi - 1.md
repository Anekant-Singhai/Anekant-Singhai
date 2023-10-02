just a basic sqli auth bypass :
![[Pasted image 20230624210955.png]]

Background:
In this example we used ' or 1=1 -- .

This causes the application to perform the query:

`SELECT * FROM users WHERE username = '' OR 1=1-- ' AND password = 'foo'`

Because the comment sequence (--) causes the remainder of the query to be ignored, this is equivalent to:

`SELECT * FROM users WHERE username = ' ' OR 1=1`

In this example the SQL injection attack has resulted in a bypass of the login, and we are now authenticated as "admin".

You can learn more about this type of detection in our article; [Using Burp to Detect Blind SQL Injection Bugs](https://portswigger.net/support/using-burp-to-detect-blind-sql-injection-bugs).
``

![[Pasted image 20230624210915.png]]