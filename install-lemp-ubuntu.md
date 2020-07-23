Install LEMP on Ubuntu
===

Prerequisites
---
- Server with Ubuntu OS (recommend: Ubuntu 16.04)
- Internet connection
- Basic knowlegde about linux command line

NGINX Installation
---
```sh
$ sudo apt update
$ sudo apt install nginx
```

MySQL Installation
---
```sh
$ sudo apt install mysql-server
```

PHP Installation
---
In this steps I will try to install PHP v7.2. If you want to use another version, just replace 7.2 to your needs.
```sh
$ sudo add-apt-repository ppa:ondrej/php
$ sudo apt update
$ sudo apt install php7.2 php7.2-mysql php7.2-fpm php7.2-gd
$ sudo service nginx reload
```

OK, from now you already successfully install LEMP on your server.
You can try to access your Public IP through your browser and it will look like this 

Configure NGINX
---
Delete `/etc/nginx/sites-enabled/default` and `/etc/nginx/sites-available/default`
```sh
$ sudo rm /etc/nginx/sites-enabled/default /etc/nginx/sites-available/default
```
Create new sites files with this configuration
```sh
$ sudo vim /etc/nginx/sites-available/default
```
Paste below script
```nginx
 server {
     listen 80;
     root /opt/www; # This is the root directory of our server, you can change it.
     server_name _;
     location / {
         try_files $uri $uri/ =404;
     }
     location ~ \.php$ {
         include snippets/fastcgi-php.conf;
         fastcgi_pass unix:/run/php/php7.2-fpm.sock;
         # Change to below line if you serve FPM via TCP
         #fastcgi_pass 127.0.0.1:7200;
     }
     location ~ /\.ht {
         deny all;
     }
 }
```

Then check your script and reload the NGINX
```sh
$ sudo nginx -t
$ sudo service nginx reload
```
Optional : Configure PHP-FPM to serve via TCP
---
Edit the PHP-FPM configuration file
```sh
$ sudo vim /etc/php/7.2/fpm/pool.d/www.conf
```
And change below line
```sh
listen = /var/run/php7.2-fpm.sock
```
to

```sh
listen = 127.0.0.1:7200
```
Then reload the PHP-FPM service
```sh
$ sudo service php7.2-fpm restart
```

Testing the Webserver
---
Create folder to become your root directory and check if your service running well
```sh
sudo mkdir /opt/www
sudo vim /opt/www/info.php
```
Paste below code to your `info.php`
```php
 <?php
     phpinfo();
 ?>
```

Go to your web browser and go to `http://your_server_ip/info.php`
If you can see the generated page from PHP, it means your service is running well and you can continue to explore more.
