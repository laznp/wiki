Simplify OAuth Login With Flask Dance
=== 

Prerequisites
---
- Text Editor
- Python 3.6
- Basic knowlegde of Python Programming

Setup Directory and Virtual Environment
---
```sh
$ pip3 install virtualenv
$ mkdir /home/$USER/learn-flask-dance
$ cd /home/$USER/learn-flask-dance
$ touch app.py
$ python3 -m venv venv
```

Install Required Dependencies
---
```sh
$ cd /home/$USER/learn-flask-dance
$ source venv/bin/activate
$ pip3 install Flask-Dance Flask
```

Create Client ID and Client Secret
---
#### Github

- Head to `https://github.com/settings/developers`
- Register new application, if you haven't create any.
- Fill the form like: 
 - Application Name : `LazAuth` < Change `Laz` to your name
 - Homepage URL : `http://127.0.0.1:5000`
 - Application description : `Learning How To Login with Github`
 - Authorization callback URL :  `http://127.0.0.1:5000/github/authorized`
- Register Application
- Copy `Client ID` and `Client Secret` to this below code

#### Google

- Head to `https://console.developers.google.com/`
- On the sidebar choose `Credentials`
- On the top of `Credentials` panel you'll see `+ CREATE CREDENTIALS`, choose `OAuth Client ID`
- Set Application Type to `Web Application`
- Set Name like : `LazAuth` < Change `Laz` to your name
- On the bottom you'll see `Authorised redirect URIs`, fill it with `http://127.0.0.1:5000/login/google/authorized`
- Click `CREATE`
- Wait until you see modal `OAuth client created`, copy `Your Client ID` and `Your Client Secret` to this below code

Note! Copy the Client ID and Client Secret to somewhere safe! If you lose it, you have to start over again!

Code
---
Open text editor paste this code to `/home/$USER/learn-flask-dance/app.py`
```py
from flask import Flask, redirect, url_for, session
from flask_dance.contrib.github import make_github_blueprint, github
from flask_dance.contrib.google import make_google_blueprint, google
import os 
os.environ['OAUTHLIB_INSECURE_TRANSPORT'] = '1'
os.environ['OAUTHLIB_RELAX_TOKEN_SCOPE'] = '1'

app = Flask(__name__)
app.secret_key = "SecretKey!"  # Replace this with your own secret!
github_blueprint = make_github_blueprint(
	client_id="<client_id>", # Replace this with your github client id
	client_secret="<client_id>", # Replace this with your github client secret
)
google_blueprint = make_google_blueprint(
    client_id="<client_id>", # Replace this with your google client id
    client_secret="<client_id>", # Replace this with your google client secret
	scope=[
		"https://www.googleapis.com/auth/plus.me",
		"https://www.googleapis.com/auth/userinfo.email",
	]
)
app.register_blueprint(github_blueprint, url_prefix="/github/authorized")
app.register_blueprint(google_blueprint, url_prefix="/login")

@app.route("/github")
def github_login():
	if not github.authorized:
		return redirect(url_for("github.login"))
	resp = github.get("/user")
	return resp.json()

@app.route("/google")
def google_login():
    if not google.authorized:
        return redirect(url_for("google.login"))
    resp = google.get("/oauth2/v2/userinfo")
    return resp.json()

if __name__ == '__main__':
	app.run(debug=True)
```

Start Your Apps!
---
```sh
$ cd /home/$USER/learn-flask-dance
$ python3 app.py
```
Open web browser and head to this following URL

- http://127.0.0.1:5000/github for Login with Github
- http://127.0.0.1:5000/google for Login with Google

If you can your profile with JSON formatted it means you are already success authenticate with Github/Google!

##### You can explore more about Python Flask Dance Library from this [Official Flask Dance Documentation](https://flask-dance.readthedocs.io/en/v1.2.0/index.html)


