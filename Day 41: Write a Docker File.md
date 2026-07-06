# Apache Docker Deployment Guide

## Requirements
* **a.** Use `ubuntu:24.04` as the base image.
* **b.** Install `apache2` and configure it to work on port `5002` (do not update any other Apache configuration settings like document root etc).

---

## Deployment Steps

### 1. Create the Dockerfile
Create the `Dockerfile` at the path `/opt/docker/Dockerfile` with the following content:

```dockerfile
FROM ubuntu:24.04

RUN apt update && apt install -y apache2

RUN sed -i 's/Listen 80/Listen 5002/' /etc/apache2/ports.conf

RUN sed -i 's/<VirtualHost \*:80>/<VirtualHost \*:5002>/' /etc/apache2/sites-available/000-default.conf

RUN sed -i 's/#ServerName www.example.com/ServerName  127.0.0.1/' /etc/apache2/sites-available/000-default.conf

RUN echo "ServerName localhost" >> /etc/apache2/apache2.conf

EXPOSE 5002
```

### 2. Verify File Contents
Navigate to the directory and verify that the `Dockerfile` is saved correctly:

```bash
[banner@stapp03 ~]\$ cd /opt/docker/
[banner@stapp03 docker]\$ ls
Dockerfile
[banner@stapp03 docker]\$ cat Dockerfile 
FROM ubuntu:24.04

RUN apt update && apt install -y apache2

RUN sed -i 's/Listen 80/Listen 5002/' /etc/apache2/ports.conf

RUN sed -i 's/<VirtualHost \*:80>/<VirtualHost \*:5002>/' /etc/apache2/sites-available/000-default.conf

RUN sed -i 's/#ServerName www.example.com/ServerName  127.0.0.1/' /etc/apache2/sites-available/000-default.conf

RUN echo "ServerName localhost" >> /etc/apache2/apache2.conf

EXPOSE 5002
```

### 3. Build the Docker Image
Build the test image using the created `Dockerfile`:

```bash
[banner@stapp03 docker]\$ docker build . -t apache-test
[+] Building 38.3s (10/10) FINISHED               docker:default
 => [internal] load build definition from Dockerfile        0.1s
 => => transferring dockerfile: 455B                        0.0s
 => [internal] load metadata for docker.io/library/ubuntu:  0.0s
 => [internal] load .dockerignore                           0.1s
 => => transferring context: 2B                             0.0s
 => [1/6] FROM docker.io/library/ubuntu:24.04               0.0s
 => [2/6] RUN apt update && apt install -y apache2         33.8s
 => [3/6] RUN sed -i 's/Listen 80/Listen 5002/' /etc/apach  0.4s 
 => [4/6] RUN sed -i 's/<VirtualHost \*:80>/<VirtualHost \  0.4s 
 => [5/6] RUN sed -i 's/#ServerName www.example.com/Server  0.4s 
 => [6/6] RUN echo "ServerName localhost" >> /etc/apache2/  0.4s 
 => exporting to image                                      2.3s 
 => => exporting layers                                     2.2s 
 => => writing image sha256:ac3bba4e74f7b70e9a2deb615174c9  0.0s
 => => naming to docker.io/library/apache-test              0.0s
```

Check for any currently running processes:
```bash
[banner@stapp03 docker]\$ docker ps

CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

### 4. Verify the Created Image
Confirm that the new image exists in your local registry:

```bash
[banner@stapp03 docker]\$ docker images

REPOSITORY    TAG       IMAGE ID       CREATED          SIZE
apache-test   latest    ac3bba4e74f7   13 seconds ago   250MB
ubuntu        24.04     602eb6fb314b   15 months ago    78.1MB
```

### 5. Run the Docker Container
Launch the container in detached mode, exposing port `5002` and passing the foreground start command:

```bash
[banner@stapp03 docker]\$ docker run -d -p 5002:5002 --name my-apache-app apache-test apachectl -D FOREGROUND
676d68a7a6e5fb29684004593bf8841ed4e381cbeb8a4459cef1024882dba949
```

### 6. Verify the Running Container
Verify that the container is active and routing network traffic properly:

```bash
[banner@stapp03 docker]\$ docker ps

CONTAINER ID   IMAGE         COMMAND                  CREATED         STATUS         PORTS                                       NAMES
676d68a7a6e5   apache-test   "apachectl -D FOREGR…"   6 seconds ago   Up 5 seconds   0.0.0.0:5002->5002/tcp, :::5002->5002/tcp   my-apache-app
```

### 7. Test Apache Response
Send an HTTP request to ensure Apache is responding correctly on port `5002`:

```bash
[banner@stapp03 docker]\$ curl -I http://localhost:5002
HTTP/1.1 200 OK
Date: Mon, 06 Jul 2026 05:57:37 GMT
Server: Apache/2.4.58 (Ubuntu)
Last-Modified: Mon, 06 Jul 2026 05:54:33 GMT
ETag: "29af-655eae8a0f8a0"
Accept-Ranges: bytes
Content-Length: 10671
Vary: Accept-Encoding
Content-Type: text/html
```
