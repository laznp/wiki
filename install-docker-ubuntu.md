Install Docker on Ubuntu
=== 
Prerequisites
---
- Internet Access
- System that run ubuntu derivatives (recommend : based on Ubuntu 18.04)
- Basic knowledge of Linux Command Line

Installing Dependencies
---
```sh
$ sudo apt update
$ sudo apt-get install apt-transport-https ca-certificates curl gnupg-agent software-properties-common
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```
Adding Docker APT Repository
---
```sh
$ sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```
If you run system that use ubuntu as its parent, you must know the parent of its, e.g : Linux Mint 18 "Sarah" is based on Ubuntu 16.04. So you have to know the codename of the Ubuntu 16.04
```sh
$ sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu <codename> stable"
```
Installing Docker Engine
---
```sh
 $ sudo apt update
 $ sudo apt install docker-ce docker-ce-cli containerd.io
 $ sudo systemctl enable docker
```

Testing Pull Image
```sh
$ sudo docker pull hello-world
$ sudo docker run hello-world
```
If you can see the messages from the container, it means you have successfully install docker

"Jump In" into running container
---
Lets say that you already running ubuntu image with name `ubuntu-train`
```sh
$ sudo docker exec -it ubuntu-train bash
```
Optional : Run Docker Command Without Sudo
---
For running it without `sudo`, you have to add your user to docker group with this command.
```sh
 $ sudo usermod -aG docker $(whoami)
```
Then restart your computer.
If you want to explore more about docker, you can read [this tutorial](https://wiki.laznp.id/Deploy_Static_Webpage_With_Docker) to make you practice more about docker.
