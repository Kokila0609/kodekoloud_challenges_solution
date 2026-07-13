# Nautilus DevOps Team Task: Fix Dockerfile Build Failures

The Nautilus DevOps team is working to create new images per requirements shared by the development team. One of the team members is working to create a Dockerfile on **App Server 2** in **Stratos DC**. While working on it, she ran into issues where the `docker build` command is failing and displaying errors. Look into the issue and fix it to build an image as per the details mentioned below:

## Requirements

* **File Location**: The Dockerfile is placed on **App Server 2** under the `/opt/docker` directory.
* **Objective**: Fix the issues with this file and make sure it is able to build the image.
* **Constraints**: Do not change the base image, any other valid configuration within the Dockerfile, or any of the data being used (for example, `index.html`).

---

## Original Broken Dockerfile

```bash
[steve@stapp02 ~]\$ cat /opt/docker/Dockerfile 
```

```dockerfile
IMAGE httpd:2.4.43

ADD sed -i "s/Listen 80/Listen 8080/g" /usr/local/apache2/conf/httpd.conf

ADD sed -i '/LoadModule\ ssl_module modules\/mod_ssl.so/s/^#//g' conf/httpd.conf

ADD sed -i '/LoadModule\ socache_shmcb_module modules\/mod_socache_shmcb.so/s/^#//g' conf/httpd.conf

ADD sed -i '/Include\ conf\/extra\/httpd-ssl.conf/s/^#//g' conf/httpd.conf

COPY certs/server.crt /usr/local/apache2/conf/server.crt

COPY certs/server.key /usr/local/apache2/conf/server.key

COPY html/index.html /usr/local/apache2/htdocs/
```

---

## Updated Dockerfile

> **Note**: Added `FROM` to the image line and replaced `ADD` with `RUN` to resolve execution errors.

```bash
[steve@stapp02 docker]\$ cat /opt/docker/Dockerfile 
```

```dockerfile
FROM httpd:2.4.43

RUN sed -i "s/Listen 80/Listen 8080/g" /usr/local/apache2/conf/httpd.conf

RUN sed -i '/LoadModule\ ssl_module modules\/mod_ssl.so/s/^#//g' conf/httpd.conf

RUN sed -i '/LoadModule\ socache_shmcb_module modules\/mod_socache_shmcb.so/s/^#//g' conf/httpd.conf

RUN sed -i '/Include\ conf\/extra\/httpd-ssl.conf/s/^#//g' conf/httpd.conf

COPY certs/server.crt /usr/local/apache2/conf/server.crt

COPY certs/server.key /usr/local/apache2/conf/server.key

COPY html/index.html /usr/local/apache2/htdocs/
```
