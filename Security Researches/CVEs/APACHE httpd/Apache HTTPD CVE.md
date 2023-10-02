	#### What Is Apache?

Apache is a free and open-source web server software. According to [Hostinger Tutorials](https://www.hostinger.com/tutorials/what-is-apache), the Apache Software Foundation first released this software in 1995--making it one of the oldest pieces of software used for website deployment.

#### What is the Apache HTTPD Vulnerability?

On October 4th, 2021, the Apache Software Foundation disclosed a new path traversal 0-day vulnerability that is now known as CVE-2021-41773. In just one day, proof-of-concept (PoC) exploits quickly surfaced online on GitHub and other writeups. Although the PoC exploit code appeared after the vulnerability disclosure, adversaries were exploiting the vulnerability in the wild before the CVE disclosure. Targeted vulnerable systems included servers running Apache 2.4.49 and Apache 2.4.50.

On October 6th, 2021, [reports](https://cloudsek.com/threatintelligence/cve-2021-41773-apache-http-server-exploited-actively-in-the-wild/) revealed the active exploitation of this vulnerability in even higher numbers. To help combat these exploitations, a patch was released on October 7th, 2021. But despite the release of this patch, on the [next day](https://www.sangfor.com/farsight-labs-threat-intelligence/cybersecurity/cve-2021-42013-apache-http-server-path-traversal-vulnerability), October 8th, [bypasses and POCs for the patch](https://blogs.juniper.net/en-us/threat-research/apache-http-server-cve-2021-42013-and-cve-2021-41773-exploited) were published and reported online--resulting in the CVE covered in this course: CVE-2021-42013.

#### A Deeper Look Into CVE-2021-42013

Resulting from an incomplete fix of another path traversal flaw called CVE-2021-41773, the CVE-2021-42013 vulnerability is both a directory traversal and an arbitrary code execution flaw (with the right conditions).

**Directory traversal** (MITRE technique [T1083](https://attack.mitre.org/techniques/T1083/)) permits adversaries to read arbitrary files on a server running an application. As noted on [GitHub](https://github.com/m96dg/CVE-2021-41773-exercise), "an attacker could use a path traversal attack to map URLs to files outside the directories configured by Alias-like directives." The readme also indicates that if files located outside of the directories lacked protection by the default configuration, "require all denied," then adversaries could bypass the URL validation by the encoding character '.' (period or decimal point punctuation). Examples of this encoding are **`/.%2e/.%2e/.%2e/`**, **`/.%2e%2f.%2e%2f`**, and **`/.%2e/%2e%2e/`**.

If **`mod_cgi`** or **`mod_cgid`** is enabled, adversaries can also leverage CVE-2021-42013 to perform **arbitrary code execution** (MITRE technique [T1203](https://attack.mitre.org/techniques/T1203/)). To accomplish this, adversaries could exploit web application vulnerabilities to execute code on a victim machine. When an application passes unsafe user-supplied data (i.e. forms, cookies, HTTP headers, etc.) to a system's shell, then arbitrary code execution is possible. We will explore this technique in the lab on the next page.

#### Vulnerability Identification

Follow these steps to verify if your machine is potentially susceptible to CVE-2021-42013.

1. Use a command-line tool called **`apachectl`** to check the version of Apache running on the host machine. It helps administrators control the functions of the Apache httpd daemon. The syntax for utilizing this tool is **`apachectl options`**, and you can use the **`-v`**  option to check the Apache version. If the returned versions are Apache 2.4.49 or Apache 2.4.50, then the system is potentially vulnerable.

![Apache HTTPD Image 1.1.1](https://images.ctfassets.net/kvf8rpi09wgk/2BI24r5kzqWIRbRQx02vuW/fd13b4a8bb4b63bb200e1dc3eddd5268/Apache_HTTPD_Image_1.1.1.png)

Image 1.1.1 - An example of using the -v option and seeing its output

2. Check whether or not the HTTP Server directory directive configuration for the entire server's file system is set as **Require all granted**. Or, identify if the directory directive is completely missing from the configuration file. To do this, view the contents of the **`httpd.conf`** file located typically at **`/usr/local/apache2/conf/`**. When looking through the config file, you want to find the following contents:

```
<Directory /> 
Require all granted 
</Directory>
```

![Require all Granted](https://images.ctfassets.net/kvf8rpi09wgk/4ZLfzrHc5ytuTV1ZPSfZC7/3faface53f50d5452019ddbcde678087/Apache_HTTPD_Image_1.1.2.png)

Image 1.1.2 - The config file on the host system contains the 'Require all granted' lines.

3. Check whether **`mods_cgi`** or **`mods_cgid`** is enabled by first viewing the contents of the **`httpd.conf`** file, like you did in the last step. Identify if the following line is present:

```
LoadModule cgid_module modules/mod_cgid.so
```

![Apache HTTPD Image 1.1.3](https://images.ctfassets.net/kvf8rpi09wgk/6to8ogQuy2Bz0WXYJc1BUM/8339c94ee954f65c18e4a28a76d276c9/Apache_HTTPD_Image_1.1.3.png)

Image 1.1.3 - The line above is present in the config file

The line, **`LoadModule cgid_module modules/mod_cgid.so`**, is only required for arbitrary command execution. If you do not see that line but do see the **Require all granted** line, then users can still perform directory traversal.