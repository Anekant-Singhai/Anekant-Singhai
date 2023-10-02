#usb-protocol
The USB data was being captured by the wireshark , so we need to analyze the data
![[CTF/Seaside_wctf/IMG/Pasted image 20230820224037.png]]
we filter the packets : `usb.transfer_type == 0x01 && usbhid.data` , we export the specified packets from 
we make the hex data dump from the packets via a command:
```bash
tshark -r ./keystrokes.pcapng -Y 'usb.capdata && usb.data_len == 8' -T fields -e usb.capdata | sed 's/../:&/g2' > usb_hex.txt
```
![[CTF/Seaside_wctf/IMG/Pasted image 20230820224216.png]]
then we use the dictionary for keyboard mapping 
while this is the automated tool: `https://github.com/carlospolop-forks/ctf-usb-keyboard-parser`
![[CTF/Seaside_wctf/IMG/Pasted image 20230820224157.png]]
