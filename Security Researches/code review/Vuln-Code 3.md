#django-vulnerability #os_path_join #python-vuln
***
![[Pasted image 20230709155639.png]]
***
The problem lies in the "os.path.join" function that joins the path for the image name that we provided or uploaded , with the malicious intent the attacker could use this function to read a file that is sensitive as this function `doesn't concatenate the "absolute paths" ` as per documentation so if a user would have supplied "../../../../etc/passwd" which is an abs. path it will result in reading that file
