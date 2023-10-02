[https://github.com/piyagehi/CTF-Writeups/blob/main/2022-Unlock-The-City/technical-debt.md](https://github.com/piyagehi/CTF-Writeups/blob/main/2022-Unlock-The-City/technical-debt.md)

look at the AFP protocol at the protocol hierarchy,

digging a bit into this protocol we get to know that the AFP is used by the apple devices , developed by apple to share the files btw the devices in the network , now succeeded by SMB in os X 11 and macos 11

AFP currently supports [Unicode](https://en.wikipedia.org/wiki/Unicode) file names, [POSIX](https://en.wikipedia.org/wiki/POSIX) and [access control list](https://en.wikipedia.org/wiki/Access_control_list) permissions, [resource forks](https://en.wikipedia.org/wiki/Resource_fork), named extended attributes, and advanced [file locking](https://en.wikipedia.org/wiki/File_locking).

filter it selected , look at the "name" in the info section of the file , we see a name=image1 , that tells us that the image is being transferred

what is interesting here is now cancel the filter , and apply new filter for AppleTransferProtocol

see the responses for the file:

A section that stood out was the “FPRead” response packets. .. now we see that there is a fork request for the file , actually there are two forks involved in the ATP protocol

a data fork and a resource fork. The bytes in a file fork are sequentially numbered starting with 0. The data fork is an unstructured finite sequence of bytes. The resource fork is used to hold Mac OS resources, such as icons and drivers, and a data structure for mapping them within the fork. AFP is designed to consider both forks as finite-length byte sequences; however, AFP contains no rules relating to the structure of the resource fork.

SO IN THIS CASE:

In that case, the subsequent `RESPonse transaction` packets contain the bytes of the image, split between the different packets. This means we can export the bytes and form the image! The image is 7527 bytes bytes in size, so the `Size=7680` should get us all the bytes of this image.

But wait, right after these set of packets, there's another FPRead request of the same fork. There's even an end of file error for this one.

The difference is in the offset and size fields of both the request packets. While the first packet requested 7680 bytes, but received only 4608 bytes for some reason.

The difference is in the offset and size fields of both the request packets. While the first packet requested 7680 bytes, but received only 4608 bytes for some reason.

So the second request starts from byte 4608 and requests 3072 bytes more, making the total 7680 bytes. It receives an end of file error because the image file is only 7527 bytes in size.

Combining the bytes from both these requests should result in the final image! So I exported the data from the two FPRead reply packets, (they contained all the fragments together) and combined the exported files as one file