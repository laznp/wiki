Deploy Static Website with Docker
===
Prerequisites
---
- Docker
- Text Editor
- Basic knowledge of Linux Command Line

If you don't have docker installed on your system, you can follow [this tutorial](install-docker-ubuntu.md) to install it.

Preparation
---
Before we can start to deploy, we need to make directory.
```sh
$ mkdir -p ~/learning/html
```
Dockerfile
---
So we have to create `Dockerfile` in order to make docker build our image. And note the capital 'D' in `Dockerfile` filename.
```sh
$ cd ~/learning/
$ vim Dockerfile
```
then copy code below into Dockerfile
```docker
FROM nginx:alpine
RUN mkdir -p /app/web
COPY nginx_default.conf /etc/nginx/conf.d/default.conf
COPY html/* /app/web/
CMD ["nginx", "-g", "daemon off;"]
```
HTML Files
---
After you create the Dockerfile, we have to create the html files that will become our page. In this case we will try to make 2 html files. 1 index files and 1 another page that will linked from index.
```sh
$ cd ~/learning/html
$ touch index.html another.html
```
Then paste this line into `index.html` file
```html
<h1>Hello from Index Page</h1>
<a href="another.html">Next Page</a>
```

And paste this line into `another.html` file
```html
<span>Hello from Another Page</span>
<a href="index.html">Previous Page</a>
```

NGINX Configuration File
---
Because we will use custom directory of our website, so we have to customize it.
```sh
 $ cd ~/learning/
 $ vim nginx_default.conf
```

Paste nginx configuration below into `nginx_default.conf` file
```nginx
server {
    listen       80;
    server_name  localhost;
    location / {
        root   /app/web;
        index  index.html index.htm;
    }
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /app/web;
    }
}
```

Build and Run Docker
---
OK from here you have already created this following files

- `~/learning/nginx_default.conf`
- `~/learning/Dockerfile`
- `~/learning/html/index.html`
- `~/learning/html/another.html`

So, lets build and tag our image with this following command.
Change `<your_name>` to your name. And note the `.` behind the `docker build` command
```sh
$ cd ~/learning/
$ docker build -t <your_name>/html:latest .
$ docker run -d --name "learn_docker" -p 8000:80 <your_name>/html:latest
```

That command will build and run our docker container with name `learn_docker` and map the port `80` from container to port `8000` on your computer.
You can check if your container is running or not with this command
```sh
 $ docker ps | grep "learn_docker"
```
If everything seems good, you can check it through your browser by accessing `http://localhost:8000`.
You will found that your website is up!

Stop the running container
---
For stopping the container and delete the running container, you can use this command
```sh
$ docker stop learn_docker
$ docker rm -f learn_docker
```
From now, you can create website and deploy it into docker container and stopping the running container.
For more documentation about docker, you can go to [ Official Docker Documentation](https://docs.docker.com/)
