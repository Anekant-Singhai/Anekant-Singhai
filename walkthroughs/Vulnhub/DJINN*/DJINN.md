#exploit-development #port-knocking #python-vuln 
# Nmap
```
PORT     STATE    SERVICE VERSION
21/tcp   open     ftp     vsftpd 3.0.3
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:192.168.211.130
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| -rw-r--r--    1 0        0              11 Oct 20  2019 creds.txt
| -rw-r--r--    1 0        0             128 Oct 21  2019 game.txt
|_-rw-r--r--    1 0        0             113 Oct 21  2019 message.txt
22/tcp   filtered ssh
1337/tcp open     waste?
| fingerprint-strings: 
|   NULL: 
|     ____ _____ _ 
|     ___| __ _ _ __ ___ ___ |_ _(_)_ __ ___ ___ 
|     \x20/ _ \x20 | | | | '_ ` _ \x20/ _ \n| |_| | (_| | | | | | | __/ | | | | | | | | | __/
|     ____|__,_|_| |_| |_|___| |_| |_|_| |_| |_|___|
|     Let's see how good you are with simple maths
|     Answer my questions 1000 times and I'll give you your gift.
|     '*', 1)
|   RPCCheck: 
|     ____ _____ _ 
|     ___| __ _ _ __ ___ ___ |_ _(_)_ __ ___ ___ 
|     \x20/ _ \x20 | | | | '_ ` _ \x20/ _ \n| |_| | (_| | | | | | | __/ | | | | | | | | | __/
|     ____|__,_|_| |_| |_|___| |_| |_|_| |_| |_|___|
|     Let's see how good you are with simple maths
|     Answer my questions 1000 times and I'll give you your gift.
|_    '+', 4)
7331/tcp open     http    Werkzeug httpd 0.16.0 (Python 2.7.15+)
|_http-title: Lost in space
```

# PORT 21:
anonymous login was on , so got these files via `mget`
![[walkthroughs/Vulnhub/DJINN*/IMG/Pasted image 20230919161647.png]]

# Port 1337
It was a game :
![[walkthroughs/Vulnhub/DJINN*/IMG/Pasted image 20230919171408.png]]
1000 times huh?
wrote a python script to automate it:
```python
from pwn import *

import operator

def receive_3_lines():

lines = []

for _ in range(3):

line = r.readline().decode('utf-8').strip()

lines.append(line)

print("Received:", line)

if "ERROR" in line:

print("Error occurred, breaking the loop")

break

return lines

r = remote('192.168.211.194',1337)

a = r.readline_startswith('(').decode('utf-8')

print("This is the array: ",a)

first = int(a[1])

oper = a[5]

second = int(a[9])

print(f"The first number: {first} a[1] and the second: {a[9]} operation to perform: {a[5]}")

# we create a dictionary that has the meanings of the every operator

ops = {"+": operator.add, "-": operator.sub,

'*': operator.mul, '/': operator.truediv}

data = ops[oper](first,second)

print(f"This is what ops does: {ops[oper](first,second)}")

r.sendline(str(data))

for i in range(0,1000):

# read the lines that starts with the > symbol

a=r.readline_startswith('>').decode("utf-8")

# then read the symbols from the array that we get and then we calculate them

first = int(a[3])

oper = a[7]

second = int(a[11])

ops = {"+": operator.add, "-": operator.sub,

'*': operator.mul, '/': operator.truediv}

data = ops[oper](first,second)

print("The array now:",a)

r.sendline(str(data))

data = receive_3_lines()
```
port knocking ig:![[walkthroughs/Vulnhub/DJINN*/IMG/Pasted image 20230919171922.png]]
`1356, 6784, 3409`.The filtered ssh port is now open!, but the creds that we found are not correct so what to do?
# Port 7331
This is the http port with a flask server running saying , let's see how good you are....
![[walkthroughs/Vulnhub/DJINN*/IMG/Pasted image 20230919174814.png]]
doing a little bit of fuzzing gives us the dirs:
![[walkthroughs/Vulnhub/DJINN*/IMG/Pasted image 20230919174749.png]]
though we have the server name and version `Werkzeug httpd 0.16.0` the exploits available doesn't seem to work , will look harder into them if nothing works.....
### genie endpoint:
![[walkthroughs/Vulnhub/DJINN*/IMG/Pasted image 20230919174948.png]]
### wish endpoint:
![[walkthroughs/Vulnhub/DJINN*/IMG/Pasted image 20230919175007.png]]
seems command execution to me , when prompted we get this:
![[walkthroughs/Vulnhub/DJINN*/IMG/Pasted image 20230919175057.png]]