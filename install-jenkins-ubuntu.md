Install Jenkins on Ubuntu
===
Prerequisites
---
- Internet Access
- System that run ubuntu derivatives (recommend : based on Ubuntu 18.04)
- Basic knowledge of Linux Command Line

Installing Java
---
```sh
$ sudo apt update
$ sudo apt install default-jre default-jdk
```
Installing Jenkins
---
```sh
$ wget -q -O - http://pkg.jenkins-ci.org/debian/jenkins-ci.org.key | sudo apt-key add -
$ sudo sh -c 'echo deb http://pkg.jenkins-ci.org/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
$ sudo apt update
$ sudo apt install jenkins
```

Start Jenkins and Enable Jenkins on Boot
---
```sh
$ sudo systemctl enable jenkins
$ sudo service jenkins start
```

Optional : Open the Firewall
---
Jenkins run HTTP on port `8080`, so open it.
```sh 
$ sudo ufw allow 8080
```

Initialize Jenkins
---
You now can access Jenkins via web browser. Go to `http://your_jenkins_ip:8080`, it will ask your initial password that you can get from `/var/lib/jenkins/secrets/initialAdminPassword`
Paste the password to your browser. Then after this you need to continue

- Install suggested plugin
- Create First Admin User
- Start Using Jenkins

Now, your Jenkins is ready, you can continue to create some job!
