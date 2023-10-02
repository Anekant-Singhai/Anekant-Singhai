ip: `192.168.211.178`
# SCan:
scan said ports opened were: 80 , 22

# PORT 80:

There was a pic of poseidon the GOD of water , and nothing else , also when used the big.txt wordlist from seclists didn't give much except the `manual` endpoint which had an apache server running of version : 2.4 which on searching had a vulnerability of RCE via LFI , but didn't seem to work , so I brtfrced the dir. again using the bigger wordlist: `directory-list-2.3-big.txt` from seclist and found another endpoint:
![[walkthroughs/Vulnhub/Symfonos4/IMG/Pasted image 20231001203852.png]]
had:
![[walkthroughs/Vulnhub/Symfonos4/IMG/Pasted image 20231001204221.png]]
### Hades log:
![[walkthroughs/Vulnhub/Symfonos4/IMG/Pasted image 20231001204256.png]]
### Zeus log:
![[walkthroughs/Vulnhub/Symfonos4/IMG/Pasted image 20231001204323.png]]
### poseidon log
![[walkthroughs/Vulnhub/Symfonos4/IMG/Pasted image 20231001204352.png]]