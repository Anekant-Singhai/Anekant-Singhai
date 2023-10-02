steps compiler performs:

<ol>
<li> it checked whether the code was written right {complier}</li>
<li> it then checks whether the code has proper dictionary to translate{linker}</li>
<li>It then checks the functions are all right and in the dictionary and loads the code to the machine to be converted to machine code {loader}</li>
</ol>
![[Pasted image 20230803220519.png]]

# How to exploit:
## Scenario 1:
When you can just call out the file inside the server that you know is there 
  

> You may be wondering “I get your point but brother how do I execute _my_ code on server ? what’s the use of this code execution in LFI if I can’t place my file on server”


There is a library called pandas from which you can read csv datax