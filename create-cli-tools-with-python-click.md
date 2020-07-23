Create CLI Tools with Python Click
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
$ mkdir ~/learning
$ cd ~/learning
$ touch laznp.py # Change to <yourname>.py
$ touch setup.py
$ python3 -m venv venv
```
 
Begin Writing the Tool
---
So here we will create 2 functions :

- For Get the Public IP
- For Check Port on Host

Open `<yourname>.py` with text editor and paste the code
```py
import requests as curl
import socket
import click

@click.group()
def main():
    pass

@click.command()
def myip():
    """Command to check current Public IP"""
    print(curl.get("https://ifconfig.me").text)

@click.command()
@click.option("--host", default="127.0.0.1", help="Host")
@click.option("--port", default=80, help="Port")
def knock(host,port):
    """Command to check if the port on host is open"""
    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    s.settimeout(2)
    try:
        s.connect((host, int(port)))
        print("Connected to %s on port %s" %(host, port))
    except:
        print("Can't connect to %s on port %s" %(host, port))

main.add_command(myip)
main.add_command(knock)

if __name__ == "__main__":
    main()
```

As you can see, there is 3 function. The `main` function is used for `add` more subcommand.
The `myip` function is used to get the Public IP.
and `knock` is function that we will use for check port on any host.
Okay, now save it and open the `setup.py` then paste this code
```py
from setuptools import setup

 # Change all 'laznp' to yourname
setup(
    name='laznp',
    version='0.1',
    py_modules=['laznp'],
    install_requires=[
        'Click',
        'requests',
    ],
    entry_points='''
        [console_scripts]
        laznp=laznp:main
    ''',
)
```
Then save it.

Build and install the tools
---
Now you already have 2 files(`yourname.py` and `setup.py`) and 1 directory (`venv`). Now we can start to build our tools.
```sh
$ cd ~/learning
$ source venv/bin/activate
$ pip3 install --editable . # mind the '.' on the end of line
```

Run it!
---
After you successfully install and build your tools. Just run the help first
```sh
 $ laznp --help
```

You can see this following lines
```sh
Usage: laznp [OPTIONS] COMMAND [ARGS]...
 
Options:
  --help  Show this message and exit.
  
Commands:
  knock  Command to check if the port on host is open
  myip   Command to check current Public IP
```

Test our first command to get our Public IP
```sh
$ laznp myip
```
Then test our second command to check the opened port
```sh
$ laznp knock --host "localhost" --port 7400
```
Is it closed? Then try this command
```sh
$ laznp knock --host "laznp.id" --port 443
```

So now you can create more tools to make your life get easier.
You can explore more about Python Click Library from this [Official Click Documentation](https://click.palletsprojects.com/en/7.x/)
