#sql-injection #internal-scanning #ssh-pivoting
#proxy-ssh #pivoting 
# nmap
```
PORT     STATE    SERVICE    VERSION
> 22/tcp   filtered ssh

> 80/tcp   open     http       Apache httpd 2.2.22 ((Debian))
|_http-server-header: Apache/2.2.22 (Debian)
|_http-title: Site doesn't have a title (text/html).

> 3128/tcp open     http-proxy Squid http proxy 3.1.20
|_http-server-header: squid/3.1.20
|_http-title: ERROR: The requested URL could not be retrieved
MAC Address: 08:00:27:54:4A:37 (Oracle VirtualBox virtual NIC)
Device type: general purpose
Running: Linux 3.X
OS CPE: cpe:/o:linux:linux_kernel:3
OS details: Linux 3.2 - 3.10, Linux 3.2 - 3.16
Network Distance: 1 hop
```

# PORT 80
![[walkthroughs/Vulnhub/SKYtower/IMG/Pasted image 20230905123305.png]]
giving creds gives:
![[walkthroughs/Vulnhub/SKYtower/IMG/Pasted image 20230905123403.png]]
A single quote gives:
![[walkthroughs/Vulnhub/SKYtower/IMG/Pasted image 20230905123632.png]]
we can break and join the query background by `#`
![[walkthroughs/Vulnhub/SKYtower/IMG/Pasted image 20230905124157.png]]
we can bypass this sql injection restrictions via:
![[walkthroughs/Vulnhub/SKYtower/IMG/Pasted image 20230905153616.png]]
and we are in
![[walkthroughs/Vulnhub/SKYtower/IMG/Pasted image 20230905153715.png]]
`john` , `hereisjohn`
but the ssh port is filtered we need to bypass ssh proxy
we can connect it via:
```
ssh -o ProxyCommand='nc --proxy-type socks4 --proxy 127.0.0.1:9050 %h %p' user@host
```

or add this to a file : `.ssh/config`
```
ProxyCommand /usr/local/bin/corkscrew proxy.example.com 8080 %h %p
```

like:
```
ssh -o ProxyCommand='corkscrew 192.168.90.4 3128 192.168.90.4 22' john@192.168.90.4
```

we can see that:
![[walkthroughs/Vulnhub/SKYtower/IMG/Pasted image 20230905164705.png]]

we can also scan all the ports from the machine behind the proxy
![[walkthroughs/Vulnhub/SKYtower/IMG/Pasted image 20230905163458.png]]
tool: `https://github.com/aancw/spose/tree/master`

![[walkthroughs/Vulnhub/SKYtower/IMG/Pasted image 20230905165537.png]]
it's giving no output:
![[walkthroughs/Vulnhub/SKYtower/IMG/Pasted image 20230905165638.png]]
It's executing in bg:
`bash -c`

![[walkthroughs/Vulnhub/SKYtower/IMG/Pasted image 20230905170101.png]]

we get the shell

# shell via proxy tunnels
```
proxytunnel -p 192.168.90.4:3128 -d 127.0.0.1:22 -a 1234
```

we tunneled a way to that port and forwarded it to our port 1234 on our ssh
it was behaving the same , closing it as soon as we enter , so I deleted the bashrc file

this was the bashrc:
![[walkthroughs/Vulnhub/SKYtower/IMG/Pasted image 20230905174228.png]]


# port 3306:
![[walkthroughs/Vulnhub/SKYtower/IMG/Pasted image 20230905171512.png]]

we go into sara's account
![[walkthroughs/Vulnhub/SKYtower/IMG/Pasted image 20230905172414.png]]
![[walkthroughs/Vulnhub/SKYtower/IMG/Pasted image 20230905173018.png]]
