
`http://mercury.picoctf.net:15931/`

the site looks like this:
![[Pasted image 20230613220704.png]]
* Source-code: nothing of any interest
* Network-tab: index.php file had an entry seemed suspicious:
![[Pasted image 20230613220938.png]]
why one method is GET while another POST? -> 
WHENEVER WE SENT THE POST REQUEST IT WOULD TURN BLUE
WHILE WHENEVER THE GET REQUEST WAS SEN IT TURNED RED 

WHY?!!
Let's check what all methods are available for it:
 When we go to see the methods available for sending via 
 OPTIONS method we see that all the headers are gone , that is suspicious ,
 ![[Pasted image 20230613222552.png]]
 
  what if we used HEAD method to see what is hidden in those response headers , because server should have responded to the OPTIONS header:
![[Pasted image 20230613223040.png]]

andd... we get the flag
flag: picoCTF{r3j3ct_th3_du4l1ty_82880908}
