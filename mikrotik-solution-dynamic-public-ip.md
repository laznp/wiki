Mikrotik Solution for Dynamic Public IP
===
Prerequisites
---
- Bridged modem
- Mikrotik Device, open these service
  - SSH on port 2200
  - HTTP on port 8000
- Basic skill of command line
- Ubuntu Server with Public IP (Optional)
- Internet Connection

I think you came here because you have problem regarding your Dynamic Public IP especially if you live in Indonesia and you use Indihome as your ISP.
I will not explain how to bridge your modem here. And I will not use Winbox for this tutorial because I believe that you can't always dependant on GUI.

Mikrotik Configuration
---
Assume your Mikrotik IP is `10.10.10.1`
```sh
 $ ssh user@10.10.10.1 -p 2200
```
Then check if the IP/Cloud feature is already enabled
```sh
    $ /ip cloud print
```
If you find line that show like this
```sh
...
ddns-enabled: no
...
```

It means that you haven't enable the IP Cloud feature. To enable it, use this command below
```sh
$ /ip cloud set ddns-enabled=yes
$ /ip cloud print
```
If you can see that your `ddns-enabled: yes`, copy the `dns-name` and paste into somewhere because we will need it for our next configuration

Optional : Create Domain for Mikrotik Hostname
---
All you need to do is install NGINX on your public server. If you haven't install it, use this [link](install-lemp-ubuntu.md) to help you install NGINX Web Server.
SSH to your server then create file for NGINX configuration
```sh
$ ssh user@<server_public_ip>
$ sudo vim /etc/nginx/sites-available/mikrotik
```
Paste this following code
```nginx
server {
    listen 80;
    server_name mikrotik.laznp.id;
    location / {
        proxy_pass http://<your_dns_name_from_mikrotik>:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
 }
```

After that, symlink the file and restart the nginx to make changes
```sh
$ sudo ln -s /etc/nginx/sites-available/mikrotik /etc/nginx/sites-enabled/mikrotik
$ sudo nginx -t
$ sudo service nginx restart
```
Now follow below steps to make your hostname become public

- Login to your DNS Management for your domain. Configuration below is depend on your domain provider
- Add subdomain
  - Type : `A`
  - Hostname : `mikrotik.laznp.id` <= Change this to your prefered subdomain
  - Value : `your_server_public_ip`
  - TTL : `3600`

Then try to access the new created subdomain `http://mikrotik.laznp.id` via web browser.
So now your home network is already public accessible, you can continue to explore more like:

- Create Public Web Server
- Create Public Media Server
