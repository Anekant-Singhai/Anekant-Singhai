This writeup is highly understood and taken from:
`https://medium.com/@rajpastagiya44/nullcon-hackim-ctf-web-challenges-walkthrough-86c62ecedec8`

```php
<!DOCTYPE html>  
<html>  
<head>  
    <meta charset="utf-8">  
    <meta name="viewport" content="width=device-width, initial-scale=1">  
    <title>IPfilter</title>  
</head>  
<body>  
    <h1>IPfilter</h1>  
    <?php  
        error_reporting(0);  
        function fetch_backend($ip) {  
            if(is_bad_ip($ip)) {  
                return "This IP is not allowed!";  
            }  
            return file_get_contents("http://". $ip . "/");  
        }  
        function is_bad_ip($ip) {  
            if(!preg_match('/^\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}$/', $ip)) {                // IP must be in X.Y.Z.Q format                
            return true;  
            }            $frontend = gethostbyname(gethostname());            $backend = gethostbyname("ipfilter_backend");            $subnet = long2ip(ip2long($frontend) & ip2long("255.255.255.0"));            $bcast = long2ip(ip2long($frontend) | ~ip2long("255.255.255.0"));  
  
            if(isset($_GET['debug_filter'])) {  
                echo "<pre>";  
                echo "IP: " . $ip . "<br>";  
                echo "Frontend: " . $frontend . "<br>";  
                echo "Backend: " . $backend . "<br>";  
                echo "Subnet:" . $subnet . "<br>";  
                echo "Broadcast:" . $bcast . "<br>";  
                echo  "</pre>";  
            }  
  
            if(inet_pton($ip) < (int) inet_pton($subnet)) {                // Do not go below the subnet!                
            return true;  
            }  
            if(! (inet_pton($ip) < inet_pton($bcast))) {                // Do not go above the subnet!                
            return true;  
            }  
            if($ip == $backend) {                // Do not allow the backend with our secrets ;-)                
            return true;  
            }  
            return false;  
        }  
        if(isset($_GET['fetch_backend']) ) {  
            echo fetch_backend($_GET['bip']);  
        }  
        if(isset($_GET['src'])) {            highlight_file(__FILE__);  
        }        // with <3 from @gehaxelt    ?>  
    <p>With <3 from @gehaxelt</p>  
</body>  
<!-- view source: ?src -->  
</html>
```
If we look at the page source of the website provided, we can see the comment with **“<! -- view source: ?src -->”**. Below is the PHP source code provided to us when we go to [**http://52.59.124.14:10019/?src**](http://52.59.124.14:10019/?src=).

Looking at the code, we find few interesting things:  
***
1). There is an IP-filtering function named as “is_bad_ip”, which checks the input against some regex, subnet, and the backend ip.  

2). There few GET parameters which are: **“fetch_backend”** which will help us get the backend of provided IP, **“bip”** where we need to send the IP for which we need backend data, **“debug_filter”** which specifically shows different IPs.
***

Our goal here is to first find the backend IP, and then bypass the IP-filter to get the backend code (where presumably the flag will be).
So when we pass the parameters as said in the code:
![[CTF/NULLCOM_2023/WEB/IMG/Pasted image 20230902114556.png]]
```
http://52.59.124.14:10019/?fetch_backend&&debug_filter&&bip=127.0.0.1
```
![[CTF/NULLCOM_2023/WEB/IMG/Pasted image 20230902114709.png]]
![[CTF/NULLCOM_2023/WEB/IMG/Pasted image 20230902114723.png]]
we see the backend ip:
192.168.1.3 but there's a regex filter that stops it to pass the ip , we can see that here also:
```php
function is_bad_ip($ip) {  
            if(!preg_match('/^\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}$/', $ip)) {                /* IP must be in X.Y.Z.Q format  */              return true;  
            }  
```
It is certain that we have to give the ip but how?
we can't give it in standard format , but it comes out that we can give ip in different formats
reference:
`https://www.hacksparrow.com/networking/many-faces-of-ip-address.html`

One of the ways as mentioned in the reference is mixed notation. Here since the regex allows at max `999.999.999.999` as IP to be passed, hexadecimal and binary notation is out of bounds since hexadecimal requires “0x” in front, and binary requires more than 3 digits to specify a single octet. So the best choice is to use combination of decimal and octal form.

For our backend IP the first 3 octets are fairly big, so there we can’t use octal nation as again octal notation needs “00” as prefix. But for last octet we can use octal notation which would look like “003”.

Keeping this theory in mind, the IP we should try would be “192.168.112.003” where “003” is in octet form, and since PHP won’t evaluate it as octet and directly checks as “192.168.112.003” == “192.168.112.3”, with regex and subnet checks, last backend IP check will be passed, and when the HTTP request is sent, the octet will be evaluated to get proper IP.

To see in action the URL would look like this: [**http://52.59.124.14:10019/?fetch_backend&debug_filter&bip=192.168.112.003**](http://52.59.124.14:10019/?fetch_backend=&debug_filter=&bip=192.168.112.003), and we see the flag too.

FLAG: _ENO{Another_Fl4G_something_IP_STuff!}_
![[CTF/NULLCOM_2023/WEB/IMG/Pasted image 20230902121614.png]]