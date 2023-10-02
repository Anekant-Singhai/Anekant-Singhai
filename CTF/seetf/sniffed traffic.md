this had a wireshark file in it , we use it to see that what kind of data was transferred,

so we first look at the protocol hierarchy and look at the protocols being used,

the juicy prt is from http where we see that a GET request is made to get a certain file and look at the file content , the file content has PZ in start, means it's a zip file

we look the file in the raw format and cpy it's content, to a new file and name it with .zip extension

unzip it, you'll find a stuff file that is there that requires a password but how do we crack it?

I used tools , didn't work,

then I realised there's a field in the protocol hierarchy for data looking into that we get to know that there's a convo going on on some kind of passwd

boom! password was there

after unzipping we found the file that was gibbersh , using the strings command , got to know that it was containing a word called flag

command was:

"strings stuff"

then used a forensic tool callled:

foremost that is used to output the bin file into zipped whole complte file, after that to unzip it still required a password

this time while using brute forcer , the passwd was john, got the flag