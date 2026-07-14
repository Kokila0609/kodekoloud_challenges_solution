# Requirement
Requirement:


On App Server 3 in Stratos Datacenter create a docker compose file /opt/itadmin/docker-compose.yml (should be named exactly).


The compose should deploy two services (web and DB), and each service should deploy a container as per details below:


For web service:


a. Container name must be php_web.


b. Use image php with any apache tag. Check here for more details.


c. Map php_web container's port 80 with host port 8089


d. Map php_web container's /var/www/html volume with host volume /var/www/html.


For DB service:


a. Container name must be mysql_web.


b. Use image mariadb with any tag (preferably latest). Check here for more details.


c. Map mysql_web container's port 3306 with host port 3306


d. Map mysql_web container's /var/lib/mysql volume with host volume /var/lib/mysql.


e. Set MYSQL_DATABASE=database_web and use any custom user ( except root ) with some complex password for DB connections.


After running docker-compose up you can access the app with curl command curl <server-ip or hostname>:8089/



# Docker Compose Deployment Steps

Follow these steps to configure and launch the multi-container application on the server.

### Step 1: Create and Edit the Configuration File
Open the Docker Compose file inside the designated administration directory using the `vi` editor:

```bash
sudo vi /opt/itadmin/docker-compose.yml
```

Paste the following service configuration inside the file, then save and exit:

```yaml
services:
  web:
    container_name: php_web
    image: php:apache
    ports:
      - "8089:80"
    volumes:
      - /var/www/html:/var/www/html

  DB:
    container_name: mysql_web
    image: mariadb:latest
    ports:
      - "3306:3306"
    volumes:
      - /var/lib/mysql:/var/lib/mysql
    environment:
      - MYSQL_ROOT_PASSWORD=RootComplexPassword123!
      - MYSQL_DATABASE=database_web
      - MYSQL_USER=dbuser
      - MYSQL_PASSWORD=test@123
```

### Step 2: Launch the Services
Start the application containers by executing the compose file directly with the following command:

```bash
docker compose -f /opt/itadmin/docker-compose.yml up
```
