the pdf is encrypted , we get it and then we crack it with john:

steps:
convert the file to hash via:

`pdf2john math.pdf > math_hash.txt`

remove the values and crack the hash:
`john math_hash.txt| sed "s/::.*$//" | sed "s/^.*://" >  math_hash_cracked.txt`

password: `naruto`

ctrl+a for :
![[Pasted image 20230625004419.png]]






