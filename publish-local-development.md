Publish Local Development via SSH Tunnel
=== 
Prerequisites
---
- Internet Access
- Private server that run ubuntu derivatives (recommend : based on Ubuntu 18.04)
- Basic knowledge of Linux Command Line

Config SSH on Remote Server
---
```sh
$ ssh <user>@<remote_ip>
$ sudo echo "GatewayPorts yes" >> /etc/ssh/sshd_config
$ sudo service ssh restart
```
Open Port on Remote Server
---
```sh
 $ sudo ufw allow 9090
```
Configure Nginx to Use Proxy Pass
---
```sh
$ sudo vim /etc/nginx/sites-enabled/your_domain
```
Copy this below code
```nginx
server {
    server_name your_domain;
    listen 80;
    location / {
        proxy_pass http://127.0.0.1:9090;
    }
}
```
Then restart the nginx
```sh
$ sudo service nginx restart
```

Start Tunnel on your Local
---
Copy this command and dont close the terminal
```sh
$ ssh -R 9090:localhost:<your_dev_port> <user>@<remote_ip>
```
Test it!
---
Check your published local with this command
```sh
 $ curl <your_domain>
```

