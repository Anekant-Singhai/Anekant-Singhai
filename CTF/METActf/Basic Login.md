![[CTF/METActf/IMG/Pasted image 20230908200357.png]]

![[CTF/METActf/IMG/Pasted image 20230908200247.png]]

![[CTF/METActf/IMG/Pasted image 20230908200259.png]]
code:
```python
from scapy.all import *

packets = rdpcap('basicLogin.pcapng')

http_data = b''  # Initialize an empty byte string

# Iterate over all the packets
for packet in packets:
    if IP in packet and packet[IP].src == '127.0.0.1' and TCP in packet:
        http_layer = packet[TCP].payload
        if Raw in http_layer:
            http_data += bytes(http_layer[Raw])

with open('http.txt', 'wb') as http_file:
    http_file.write(http_data)
```

MetaCTF{bas364_is_t00_p00r_to_op3n_the_door}

# your password my password
![[CTF/METActf/IMG/Pasted image 20230908201225.png]]

# Admin portal
![[CTF/METActf/IMG/Pasted image 20230908201317.png]]

# Bucket works
Inside the source code:
![[CTF/METActf/IMG/Pasted image 20230908203555.png]]

this bucket was exposed to the internet:
![[CTF/METActf/IMG/Pasted image 20230908203525.png]]
in one of the image was: 
![[CTF/METActf/IMG/Pasted image 20230908203445.png]]
`MetaCTF{Open_S3_Bucket}`
# string and things
strings command
![[CTF/METActf/IMG/Pasted image 20230908214234.png]]
`MetaCTF{a16935925d41ad88a928e94b35416797}`

# Middleman
![[CTF/METActf/IMG/Pasted image 20230909031339.png]]

# Juggling club
refer:
```
https://www.youtube.com/watch?v=HOQzu0SQFWA&t=899s
```
we juggle !!!!
![[CTF/METActf/IMG/Pasted image 20230909194205.png]]
![[CTF/METActf/IMG/Pasted image 20230909194248.png]]
`MetaCTF{account-paramount-tying-bottling-560874135}`
# Prince of lies
![[CTF/METActf/IMG/Pasted image 20230909200145.png]]
`MetaCTF{y0u_g0T_5CamM3d_8a68c3e6b}`

# corruption
![[CTF/METActf/IMG/Pasted image 20230909200343.png]]
![[CTF/METActf/IMG/Pasted image 20230909200446.png]]
`MetaCTF{0p3rating_$ystems_1ike_ext3nsions}`

# escalator 1
`find /home/hackerman -exec "whoami" \;`
find command was being executed as root , how do we know:
![[CTF/METActf/IMG/Pasted image 20230909225016.png]]
help from: 
`https://www.hackingarticles.in/linux-for-pentester-find-privilege-escalation/`

`MetaCTF{bc2536631c6d285111bf3e7ed5db2c31}`
![[CTF/METActf/IMG/Pasted image 20230909224900.png]]
command:
```
find ~ -name "*.txt" -exec ls -l {} \; -or -exec cat /flag.txt \;
```

# escalator 2
Hint said something about capabilities , s used this command:
```
getcap -r / 2>/dev/null
```

![[CTF/METActf/IMG/Pasted image 20230909225626.png]]
with the help of this article was able to understand what was to do:
`https://jai-the-seeker.github.io/musings/ctf/CTF-01/`

![[CTF/METActf/IMG/Pasted image 20230909231030.png]]
and boom!
![[CTF/METActf/IMG/Pasted image 20230909233034.png]]
### really useful resources:
* https://infosecwriteups.com/understand-privilege-escalation-by-abusing-linux-access-control-6cab107e7203
* https://steflan-security.com/linux-privilege-escalation-exploiting-capabilities/
* https://jai-the-seeker.github.io/musings/ctf/CTF-01/
* https://book.hacktricks.xyz/linux-hardening/privilege-escalation/linux-capabilities#cap_dac_read_search
# escalator 3
![[CTF/METActf/IMG/Pasted image 20230909235859.png]]
but.....
![[CTF/METActf/IMG/Pasted image 20230909235944.png]]
this article really helped .... really:
`https://www.baeldung.com/linux/solve-shared-object-error`
there are some variables where linux looks for libraries , like the `_LD_LIBRARY_PATH_` Variable
and to find the library where it is we use find command , unfortunately it didn't worked so :
I used this: `ldconfig -p` and grep the filename it was missing:
![[CTF/METActf/IMG/Pasted image 20230910001610.png]]

# clarity
look at the response header log4j
![[CTF/METActf/IMG/Pasted image 20230910014408.png]]
yep it's log4j when read about JNDI in the challenge
so we go to this 
`https://sysdig.com/blog/exploit-detect-mitigate-log4j-cve/`

# mathe magical
when I give a double quote after the equation , it breaks
![[CTF/METActf/IMG/Pasted image 20230910023741.png]]
and this is code:
![[CTF/METActf/IMG/Pasted image 20230910030131.png]]

