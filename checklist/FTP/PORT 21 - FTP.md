# FTP Enumeration Guide

## **Introduction**

FTP is a network protocol used to transfer files from a server to a client over a network. FTP servers can be accessed either via the ftp command-line tool or via third-party applications such as [FileZilla](https://filezilla-project.org/). This service runs on port 21 by default.

This guide will cover the main methods to enumerate an FTP server in order to find potential vulnerabilities or misconfigurations.

## **Identifying an FTP Server**

Port scanning tools such as Nmap can be used to identify whether an FTP server is running on the target host:

```
nmap -p 21 X.X.X.X
```

![](https://i0.wp.com/steflan-security.com/wp-content/uploads/2021/02/image-327.png?resize=769%2C165&ssl=1)

The scan has identified that the remote server is running FTP on port 21.

## **Banner Grabbing**

Services often have a banner that is displayed when establishing a connection, Banner Grabbing is a technique used to gain information about the services and its version. Netcat can be used for this task:

```
nc -nv X.X.X.X 21
```

![](https://i0.wp.com/steflan-security.com/wp-content/uploads/2021/02/image-322.png?resize=644%2C96&ssl=1)

The banner is disclosing the application used on port 21 as well as the version (vsFTPd 3.0.3)

If the port is forcing SSL connections like the one in [[walkthroughs/Vulnhub/Vulnix/vulnix|vulnix]]
```bash
openssl s_client -connect crossfit.htb:21 -starttls ftp #Get certificate if any
```
OR:
![[checklist/FTP/IMG/Pasted image 20230913001939.png]]

Once the FTP service and version running on the server have been identified, common exploit databases such as Exploit DB can be used to identify any potential vulnerabilities:

![](https://i0.wp.com/steflan-security.com/wp-content/uploads/2021/02/image-323.png?resize=810%2C73&ssl=1)

In case of vsFTPd 2.3.2, for example, the only available exploit on Exploit DB was a denial of service, but unpatched FTP applications can often lead to vulnerabilities such as arbitrary file write/read, remote command execution and more.

Banner grabbing can also be performed using the -sV Nmap flag or through the auxiliary/scanner/ftp/ftp_version Metasploit module.

## **Anonymous Authentication**

FTP has a way to allow remote users to authenticate without having the need to identify themselves to the server. If this feature is enabled on the FTP server, users will be able to authenticate using anonymous as the username and any password

Anonymous FTP is a common way to get access to a server in order to view or download files that are publicly available, although it can pose a security risk if the FTP server is exposing sensitive files or folders. The FTP command can be used to perform an authentication as follows:

```
ftp X.X.X.X
#provide anonymous as username
#provide any passowrd
```

![](https://i0.wp.com/steflan-security.com/wp-content/uploads/2021/02/image-324.png?resize=756%2C162&ssl=1)

Thrugh anonymous authentication, a connection was established to the remote server without the need of any credentials.

Anonymous authentications can also be performed using the Nmap ftp-anon script, the Metasploit auxiliary/scanner/ftp/anonymous module or through graphical user interfaces such as [FileZilla](https://filezilla-project.org/).

## **Common Credentials**

A few common passwords or usernames (if unknown) such as admin, administrator, root, ftpuser, test etc. should be tried if anonymous authentication is disabled on the remote FTP server. This is safer than bruteforcing and it should always be tried when possible.

![](https://i0.wp.com/steflan-security.com/wp-content/uploads/2021/02/image-325.png?resize=690%2C165&ssl=1)

In the example above, a connection was established by using a password of “test”.

An FTP authentication can also be performed using the auxiliary/scanner/ftp/ftp_login Metasploit module, graphical user interfaces such as [FileZilla](https://filezilla-project.org/) or by simply typing ftp://X.X.X.X/ in the URL bar of a browser.

# Getting the files from FTP:
Note: when connected to ftp always check in which mode the files are being transferred, commands:

>binary #Set transmission to binary instead of ascii
>ascii #Set transmission to ascii instead of binary

### via wget:
```
wget -m ftp://anonymous:anonymous@10.10.10.98 #Donwload all
wget -m --no-passive ftp://anonymous:anonymous@10.10.10.98 #Download all
```

## via telnet
we can copy various files like shell , ssh keys and all
telnet 192.168.1.104 21 referece: [[walkthroughs/Vulnhub/JOY*/JOY|JOY]] vulnhub
copy from:
```
site cpfr /home/patrick/version_control 
```
copy to:
```
site cpto /home/ftp/upload/version_control
```
## **Bruteforcing Credentials**

A brute-force attack consists of an attacker submitting a number of passwords or usernames with the purpose of identifying the correct combination to access a given system.

Network cracking tools such as [Hydra](https://github.com/vanhauser-thc/thc-hydra) can be used to perform bruteforce attacks against online services such as FTP, HTTP, SMB etc. In this specific case it will be a dictionary attack, meaning hydra will use a list of usernames and passwords from a text file to perform the authentication attempts.

The following command can be used in Hydra to bruteforce FTP credentials:

```
hydra [-L users.txt or -l user_name] [-P pass.txt or -p password] -f [-S port] ftp://X.X.X.X
```

![](https://i0.wp.com/steflan-security.com/wp-content/uploads/2021/02/image-332.png?resize=810%2C165&ssl=1)

The command above has identified the password for the “ftpuser” user, by providing a list of usernames and passwords.

FTP servers credentials can also be bruteforced by using the Nmap ftp-brute script or the Metasploit auxiliary/scanner/ftp/ftp_version module.

## **Packet Sniffing**

Packet sniffing is the practice of gathering, collecting, and logging some or all packets that pass through a computer network, regardless of how the packet is addressed.

Because data transmitted over FTP on port 21 is unencrypted, an attacker could intercept traffic on the network and identify the credentials being used to perform the authentication.

[Wireshark](https://www.wireshark.org/) is a widely popular network sniffing and packet analyzing tool that can be used to do exactly that. The main screen displays all of the traffic within the network (in this case a filter for port 21 has been applied):

![](https://i0.wp.com/steflan-security.com/wp-content/uploads/2021/02/image-330.png?w=800&ssl=1)

The Follow→TCP Stream functionality can be used to inspect the actual traffic:

![](https://i0.wp.com/steflan-security.com/wp-content/uploads/2021/02/image-331.png?w=800&ssl=1)

![](https://i0.wp.com/steflan-security.com/wp-content/uploads/2021/02/image-328.png?resize=810%2C203&ssl=1)

In this case Wireshark was able to capture the connection made on port 21 and display the clear-text credentials used to authenticate.

The dig command line tool which comes with Kali Linux can also be used to analyze network traffic.

## **Enumerating Files & Folders**

Once an authentication has been performed successfully, steps should be performed in order to identify the following:

- Current working directory
- Whether FTP is granting access to directories being used by other services
- Files and folders the current user has access to read/write to

These enumeration steps are key in order to further exploit FTP. For example, if the FTP server grants access to a folder used by a web server, a malicious script can be uploaded and executed from a browser. Alternatively, if the current FTP user can access or modify certain files such as .bash_history, SSH keys, passwd/shadow etc. it could potentially grant remote access to the system.

## **Nmap Scripts**

The Nmap Scripting Engine (NSE) allows users to write (and share) simple scripts (using the [Lua programming language](http://lua.org/) ) to automate a wide variety of networking tasks.

Nmap comes with several FTP-related scripts such as:

- ftp-anon – Checks if an FTP server allows anonymous logins.
- ftp-brute – Performs brute-force password auditing against FTP servers.
- ftp-bounce – Checks to see if an FTP server allows port scanning using the FTP bounce method.

When using Nmap, scripts can be specified using the –script flag as follows:

```
nmap -p 21 --script [script name] X.X.X.X
```

![](https://i0.wp.com/steflan-security.com/wp-content/uploads/2021/02/image-326.png?resize=692%2C180&ssl=1)

Nmap scripts can be very powerful and can help greatly in speeding up reconassaince. The -sC flag will perform all scripts deemed as safe by Nmap when doing a port scan.

## **Common FTP Commands**

Below are some of the most common commands that can be used when communicating with an FTP server:

|   |   |
|---|---|
|**Command**|**Description**|
|_?_/help|print local help information|
|_append_|Append to a file|
|_ascii_|set ascii transfer type|
|_binary_|Set Binary transfer type|
|_bye/_exit__/quit|Terminate ftp session and exit|
|_cd_|Change remote working directory|
|_chmod_|Change file permissions of remote file|
|_close_/disconnect|Terminate FTP session|
|_debug_|toggle/set debugging mode|
|_delete_/mdelete (multiple)|delete remote file|
|_dir_/ls|list contents of remote directory|
|_get_/recv/mget (multiple)|receive file|
|_mkdir_|make directory on remote machine|
|_passive_|enter passive transfer mode|
|_put_/_mput_ (multiple)|send one file|
|_pwd_|print working directory on remote machine|
|_rename_|rename file|
|_rmdir_|remove directory on remote machine|
|_size_|show size of remote file|
|_type_|set file transfer type|
|_verbose_|toggle verbose mode|

When an FTP connection can be established, either through anonymous login or via an authenticated user, the above commands can be useful for downloading, uploading, modifying and removing files.

## Config files
ftpusers
ftp.conf
proftpd.conf
vsftpd.conf

## Post exploitation
The default configuration of vsFTPd can be found in `/etc/vsftpd.conf`. In here, you could find some dangerous settings:

- `anonymous_enable=YES`
- `anon_upload_enable=YES`
- `anon_mkdir_write_enable=YES`
- `anon_root=/home/username/ftp` - Directory for anonymous.
- `chown_uploads=YES` - Change ownership of anonymously uploaded files
- `chown_username=username` - User who is given ownership of anonymously uploaded files
- `local_enable=YES` - Enable local users to login 
- `no_anon_password=YES` - Do not ask anonymous for password
- `write_enable=YES` - Allow commands: STOR, DELE, RNFR, RNTO, MKD, RMD, APPE, and SITE

## Shodan
- ftp
- port:21