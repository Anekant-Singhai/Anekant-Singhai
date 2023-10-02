# Scanning:
It had every port open , EVERY
# PORT 80
starting with a really funny note of the "internal" work on this task....
![[walkthroughs/Vulnhub/Breach_1/IMG/Pasted image 20230925204323.png]]
source reveals a base64 code:
![[walkthroughs/Vulnhub/Breach_1/IMG/Pasted image 20230925204517.png]]
turns out to be creds:
![[walkthroughs/Vulnhub/Breach_1/IMG/Pasted image 20230925204650.png]]
`pgibbons:damnitfeel$goodtobeagang$ta`
dirsearch revealed an endpoint:
![[walkthroughs/Vulnhub/Breach_1/IMG/Pasted image 20230925204857.png]]
These pics after downloading:
### bill.png:
![[walkthroughs/Vulnhub/Breach_1/IMG/Pasted image 20230925205213.png]]
and.....
![[walkthroughs/Vulnhub/Breach_1/IMG/Pasted image 20230925205153.png]]
![[walkthroughs/Vulnhub/Breach_1/IMG/Pasted image 20230925205442.png]]
it also had a `.gitignore`: 
![[walkthroughs/Vulnhub/Breach_1/IMG/Pasted image 20230925210150.png]]
series of banner grabbing:
![[walkthroughs/Vulnhub/Breach_1/IMG/Pasted image 20230925211612.png]]
they looked like ports:
![[walkthroughs/Vulnhub/Breach_1/IMG/Pasted image 20230925212550.png]]

12345:
![[walkthroughs/Vulnhub/Breach_1/IMG/Pasted image 20230925213052.png]]

nmap said :
![[walkthroughs/Vulnhub/Breach_1/IMG/Pasted image 20230925214556.png]]
![[walkthroughs/Vulnhub/Breach_1/IMG/Pasted image 20230925214539.png]]
![[walkthroughs/Vulnhub/Breach_1/IMG/Pasted image 20230925214633.png]]

# ALL RABBIT HOLES....
look at this:
![[walkthroughs/Vulnhub/Breach_1/IMG/Pasted image 20230925233141.png]]
![[walkthroughs/Vulnhub/Breach_1/IMG/Pasted image 20230925233218.png]]
on clicking the initech image:
![[walkthroughs/Vulnhub/Breach_1/IMG/Pasted image 20230925233306.png]]
all other were images while company portal said this:
![[walkthroughs/Vulnhub/Breach_1/IMG/Pasted image 20230925233331.png]]
above creds work for pgibbons:
![[walkthroughs/Vulnhub/Breach_1/IMG/Pasted image 20230925233533.png]]
ps: remove user.php from url if not loads
### email 1:
![[walkthroughs/Vulnhub/Breach_1/IMG/Pasted image 20230925233624.png]]
### email 2:
![[walkthroughs/Vulnhub/Breach_1/IMG/Pasted image 20230925233642.png]]
### email 3:
![[walkthroughs/Vulnhub/Breach_1/IMG/Pasted image 20230925233709.png]]

`admin:coffeestains` is the creds for the admin account
![[walkthroughs/Vulnhub/Breach_1/IMG/Pasted image 20230925234630.png]]
# Admin panel:
![[walkthroughs/Vulnhub/Breach_1/IMG/Pasted image 20230925234934.png]]
![[walkthroughs/Vulnhub/Breach_1/IMG/Pasted image 20230925234945.png]]

we have 3 users:
![[walkthroughs/Vulnhub/Breach_1/IMG/Pasted image 20230926004841.png]]
![[walkthroughs/Vulnhub/Breach_1/IMG/Pasted image 20230926004903.png]]
also when we go to: `modules -> protector -> prefix manager` we can see some backups , just click on backup and repost it , then the page will look like this:
![[walkthroughs/Vulnhub/Breach_1/IMG/Pasted image 20230926005753.png]]
we got this:
![[walkthroughs/Vulnhub/Breach_1/IMG/Pasted image 20230926005822.png]]
we have their hash too in the file

### content:
remember the key we got from the above mail in peter gibbons ? :
well inside the modules -> content -> contents 
![[walkthroughs/Vulnhub/Breach_1/IMG/Pasted image 20230926010030.png]]
![[walkthroughs/Vulnhub/Breach_1/IMG/Pasted image 20230926010110.png]]
tried to use that key directly , but it didn't work -> so looked up and found this blog:
`https://programmers-dump.blogspot.com/2011/04/exporting-private-key-from-java.html`
it requires a password -> tomcat maybe
strings keystore:
![[walkthroughs/Vulnhub/Breach_1/IMG/Pasted image 20230926011415.png]]

we use keystore to check what type of key is this:
`keytool -list -v -keystore key` password: `tomcat`
![[walkthroughs/Vulnhub/Breach_1/IMG/Pasted image 20230926011544.png]]
`It must be changed to PKCS12 format` says the last line via:
```
keytool -importkeystore -srckeystore key -destkeystore key.p12 -deststoretype pkcs12
```
we import this to wireshark
also we would need port for it :
we can do this via: statistics -> protocol hierarchy -> transport layer == apply as filter:
![[walkthroughs/Vulnhub/Breach_1/IMG/Pasted image 20230926012713.png]]
then add the rsa key in it to decrypt it:
in the: `edit -> preferences -> protocols -> TLS`
![[walkthroughs/Vulnhub/Breach_1/IMG/Pasted image 20230926013535.png]]
![[walkthroughs/Vulnhub/Breach_1/IMG/Pasted image 20230926013500.png]]
look at the traffic:
![[walkthroughs/Vulnhub/Breach_1/IMG/Pasted image 20230926014243.png]]
`tomcat` and `s3cret`
also look at this: cmd execution!!!
![[walkthroughs/Vulnhub/Breach_1/IMG/Pasted image 20230926014411.png]]