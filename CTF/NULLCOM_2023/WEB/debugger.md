
![[CTF/NULLCOM_2023/WEB/IMG/Pasted image 20230819233532.png]]
![[CTF/NULLCOM_2023/WEB/IMG/Pasted image 20230819233412.png]]
```php
<html>
    <head>
        <title>Debugger</title>
    </head>
    <body>
        <h1>F-Debugger</h1>
        <p>

        </p>
        <?php
        // This line defines a constant named LOADFLAG and sets its value to true.
            define("LOADFLAG", true);

            //This line turns off error reporting, which means that any error messages or warnings won't be displayed on the page. This is a common practice to prevent sensitive information from being exposed to potential attacker

            error_reporting(0);

            // This function retrieves debug information using phpinfo() and filters out specific lines based on the provided filters. The function captures the output of phpinfo(), processes it line by line, and adds lines that match the provided filters to an array.

            function get_debug_info($filters) {
                ob_start(); phpinfo(); $pi = ob_get_contents(); ob_end_clean() ;
                $debug = array();
                foreach(explode(PHP_EOL, $pi) as $line) {
                    if(strstr($line, $filters)) {
                        array_push($debug, $line);
                    }
                }
                return $debug;
            }

            //: This block of code is executed if the action parameter in the URL is set to "debug." It checks if the client's IP address is "127.0.0.0" (which is likely a typo and should be "127.0.0.1") and determines whether the client is an admin. If the client is an admin, it retrieves debug information using the get_debug_info function and echoes it on the page. Otherwise, it displays a message stating that only local admins are allowed to debug.
            /*
            1. action == debug
            2. admin or not
            3. addr = 127.0.0.1
            */ 

            if(isset($_GET['action']) && $_GET['action']=="debug") {
                $is_admin = $_SERVER['REMOTE_ADDR'] == "127.0.0.0" ? 1 : 0;
                $debug_info = get_debug_info(extract($_GET['filters']));
                if($is_admin) {
                    echo implode($debug_info, '\n');
                } else {
                    echo("Only local admins are allowed to debug!");
                }
                include_once "flag.php";
            }
            if(isset($_GET['action']) && $_GET['action']=="src") {
                highlight_file(__FILE__);
            }
            // With <3 from @gehaxelt.
        ?>
    </body>
    <!-- view source: POST ?action=src -->
    <span>With <3 from @gehaxelt</span>
</html> With <3 from @gehaxelt
```

when we did: action = debug and  
![[CTF/NULLCOM_2023/WEB/IMG/Pasted image 20230819234457.png]]
```
X-Forwarded-For: 127.0.0.1
X-Forwarded-For: 192.168.96.2
```

There was this condition : 
`$is_admin = $_SERVER['REMOTE_ADDR'] == "127.0.0.0" ? 1 : 0;`
either 1 or 0. Now look at this line:
```php
$debug_info = get_debug_info(extract($_GET['filters']));
                if($is_admin) {
                    echo implode($debug_info, '\n');
                } else {
                    echo("Only local admins are allowed to debug!");
```
Get the filters parameter and check if is_admin = 1 then give the flag else: nope!
so we give the filter's array that element's value as 1 and we get the flag

![[CTF/NULLCOM_2023/WEB/IMG/Pasted image 20230828154837.png]]

