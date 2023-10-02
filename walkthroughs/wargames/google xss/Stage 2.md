***
# [2/6]  Level 2: Persistence is key
## Mission Description

Web applications often keep user data in server-side and, increasingly, client-side databases and later display it to users. No matter where such user-controlled data comes from, it should be handled carefully.  
  
This level shows how easily XSS bugs can be introduced in complex apps.

## Mission Objective

Inject a script to pop up an `alert()` in the context of the application.  
  
**Note**: the application saves your posts so if you sneak in code to execute the alert, this level will be solved every time you reload it.
***

Level says "persistence" is the key , we have been give a webpage with comments that can be written on it , as we know that comments are stored and is visible to everyone , are we talking about the stored xss?
let's try:
we try the payload:
![[Pasted image 20230707182845.png]]
```
<img src="" onerror=alert(1)>
```

and we get the alert :)
