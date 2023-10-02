
### Nmap 
```
nmap -n -sV -Pn --script "*telnet* and safe" -p 23 <IP>
```

### Config file
```
/etc/inetd.conf
/etc/xinetd.d/telnet
/etc/xinetd.d/stelnet
```

The script `telnet-ntlm-info.nse` will obtain NTLM info (Windows versions).
