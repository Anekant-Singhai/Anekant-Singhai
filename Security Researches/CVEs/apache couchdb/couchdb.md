## What is Apache CouchDB?

assalielmehdi's [GitHub](https://github.com/assalielmehdi/CVE-2017-12635) provides some need-to-know information about Apache CouchDB:

> CouchDB uses multiple formats and protocols to store, transfer, and process it's data, it uses JSON to store data, JavaScript as its query language using MapReduce, and HTTP for an API.

Damien Katz developed Apache CouchDB, which evolved into the Apache Software Foundation project in 2008. First released in 2005, CouchDB has been used by numerous companies worldwide.

## What is the CouchDB RCE Vulnerability?

The Apache CouchDB Remote Code Execution (RCE) vulnerability (CVE-2022-24706) is a critical flaw impacting Couch databases worldwide, and it has been known to be exploited in the wild. This vulnerability impacts all versions of CouchDB 3.2.1 and below.

[David Barroso of CounterCraft](https://www.countercraftsec.com/blog/active-exploitation-of-apache-couchdb-cve-2022-24706/) provides more information on the history of this vulnerability.

> On April 26, 2022, Apache officially issued a risk notice or Apache CouchDB remote code execution vulnerability, giving it the vulnerability number CVE-2022-24706 and a severity level of critical. Attackers have been actively exploiting this vulnerability since a public exploit was available (May 11th), installing the Kinsing malware family for cryptocurrency mining.

## A Deeper Look Into CVE-2022-24706

CouchDB has built-in support for distributed computing aka cluster nodes. [Konstantin Burov](https://medium.com/@_sadshade/couchdb-erlang-and-cookies-rce-on-default-settings-b1e9173a4bcd) shares more about these nodes, which "communicate using the Erlang/OTP Distribution Protocol" that allows someone to execute OS commands through a request from the OS module. To perform this OS execution, someone must know the cookie value when attempting the remote command.

Burov writes that the cookie value is stored in either the **`.erlang.cookie`** file or the **`vm.args`** file located in the directory with the program. In the case of CouchDB, the file containing the cookie value is **`/opt/couchdb/etc/vm.args`**. The CouchDB installer leaves the default cookie value even when installed in Standalone mode. If you were to view the contents of the **`vm.args`** file, you would find the default cookie value is set as **monster**.

1. At a high level, this is what happens on a victim system when the attack is executed.
2. CouchDB opens a random network port bound to all available interfaces in anticipation of clustered operations.
3. The **`epmd`** process advertises the random port to the network.
4. An attacker uses the cookie value to authenticate any communication between Erlang nodes.

For those that own and maintain systems running Apache CouchDB, this means that an attacker can access an improperly secured default installation without authenticating and gain administrative privileges.

## Vulnerability Identification

To identify if a system you own is vulnerable to this CVE, there are two things you must look for.

First, is your CouchDB port exposed to the world? To answer this, query the victim system's Erlang Port Mapper Daemon. Use the following command to identify the port that CouchDB is running on"

```
echo -n -e "\x00\x01\x6e" | nc -vn TargetIP 4369"
```

Second, verify what your secret cookie is set to. Run the following command to check the **`/opt/couchdb/etc/vm.args`** file:

```
grep '\-setcookie' /opt/couchdb/etc/vm.args
```

If the cookie value is set to **monster** and you can access the CouchDB port externally, then you are vulnerable.

## Exploitation Identification

Successful exploitation will generate a new process by user **couchdb**, whose parent process basename is **`erl_child_setup`**. By reviewing the running processes on the system, you can identify if this process exists.

# Lab Scenario

You are a Vulnerability Assessment Analyst evaluating your company's environment for the CouchDB RCE vulnerability. Test the impact of exploitation by executing an attack against a development server hosted by your company.

**Launch the VM. Please note that it may take 5 minutes to load the environment. While waiting for it to load, we recommend reviewing the challenge questions at the end of this page. You will need to answer them correctly based on the lab material in order to earn credit for this lesson.**

# Lab Instructions

1. After launching the lab, open a terminal window, which is the black square window in the upper left (next to the firefox icon!).

2. To identify if we are able to access the CouchDB server externally, let's run an nmap scan looking for open erlang ports:

First, lets identify what our IP is in the range and use that to look around our network

`ip addr`
Next, let's scan the network for open `erlang` port mapper daemon services (port 4369). We will take our IP range from the previous command for this next command.

`nmap -p 4369 (IP range)`

We see one port is open on the network, lets target that part further and see if it advertises services  
  
```
echo -n -e "\x00\x01\x6e" | nc (our target machine IP) 4369  
```

![[Security Researches/CVEs/apache couchdb/Pasted image 20230913031303.png]]

3. Now that you have identified that the system has a public-facing port, run the Python script. This is rather simple and abuses the default erlang cookie associated with the daemon service:
`cd Documents/ `
`cd CVE-2022-24706-CouchDB-Exploit/`
4. Run the exploit script:

`python3 CVE-2022-24706-Exploit.py`
  ![[Security Researches/CVEs/apache couchdb/Pasted image 20230913031215.png]]
When prompted, enter the IP of our victim host and our erlang port (4369)  
![[Security Researches/CVEs/apache couchdb/Pasted image 20230913031553.png]]
After gaining access to the system, view the contents of the **`/etc/passwd`** file.

## Mitigation Overview

To mitigate this vulnerability permanently, you will need to upgrade to a newer version of Apache CouchDB. You should upgrade all installations 3.2.1 and below as soon as possible. If you cannot upgrade these installations, there is also a temporary fix. We will discuss both mitigation options below.

## Install CouchDB

### Debian/Ubuntu

- **Install**:
    
    - **`sudo apt-get install -y couchdb`**
- **Start Service**:
    
    - **`systemctl start couchdb`**
    - **`systemctl enable couchdb`**

### Red Hat, CentOS, RHEL, and Fedora

- **Install**:
    
    - **`sudo yum install couchdb`**
- **Configure the 'ini' file as needed**:
    
    - **`vim /opt/couchdb/etc/local.ini`**
- **Start the service**:
    
    - **`systemctl enable couchdb.service`**
    - **`systemctl start couchdb.service`**

## Temporary Fix

1. Run the exploit script to gain access again:

```
python3 exploit.py www.target.co 4369
```

2. Identify the current cookie:

```
cat /opt/couchdb/etc/vm.args | grep -setcookie
```

![CouchDB Image 1.3.1](https://images.ctfassets.net/kvf8rpi09wgk/7uWJVYy6auvSF0x9Vnnyft/c958e3cd4562153f946b19b87955c01d/CouchDB_Image_1.3.1.png)

Image 1.3.1 - Found cookie

3. Run the following command to update the cookie file:

```
sed -i "s/-setcookie\ monster/-setcookie\ newtoken/g"\ /opt/couchdb/etc/vm.args
```

![CouchDB Image 1.3.2](https://images.ctfassets.net/kvf8rpi09wgk/5723his0qm4r3l9X35FJ6z/0c53eed911ce78acd172eb22336f1fe6/CouchDB_Image_1.3.2.png)

Image 1.3.2 - Updating the cookie file

4. Verify whether the cookie has been updated:

```
cat /opt/couchdb/etc/vm.args | grep -setcookie
```

![CouchDB Image 1.3.3](https://images.ctfassets.net/kvf8rpi09wgk/38R0wwO4zLWOrXXbdVbAyI/4e8eeae3be070953c40a1bb184646576/CouchDB_Image_1.3.3.png)

Image 1.3.3 - Checking to see if the cookie is updated

5. Stop the CouchDB service:

```
systemctl stop couchdb.service
```

6. Restart the CouchDB service:

```
systemctl start couchdb.service
```