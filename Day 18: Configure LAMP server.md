# Application & Database Server Setup

## Task:1 Install Apache (`httpd`), PHP, and Dependencies on All App Hosts

Run the following commands on **each application host** (CentOS 8):

### Update system and install Apache:

```bash
sudo yum update -y
sudo yum install httpd -y
sudo systemctl start httpd
sudo systemctl enable httpd
```

## Install PHP 7.4 via Remi repository

```bash
sudo dnf install epel-release -y
sudo dnf install dnf-utils -y
sudo dnf install http://rpms.remirepo.net/enterprise/remi-release-8.rpm -y
sudo dnf module reset php -y
sudo dnf module enable php:remi-7.4 -y
sudo dnf install php php-cli php-common php-mysqlnd php-gd php-xml php-mbstring -y
```
## Task:2 Apache should serve on port 8084 within the apps.

Open the Apache config file:

```bash
sudo vi /etc/httpd/conf/httpd.conf
```

Locate line 100 (or search for Listen 80) and change it to:

```bash
Listen 8084
```

Restart Apache:

```bash
sudo systemctl restart httpd
```

Verify Apache is listening on port 8084:

```bash
ss -tunlp | grep :8084
```

## Task:3 Install/Configure MariaDB server on DB Server

```bash

      sudo yum update
      sudo yum -y install mariadb-server
      sudo systemctl start mariadb
      sudo systemctl status mariadb
      mariadb -u root -p
```

## Task:4 Create a database named kodekloud_db10 and create a database user named kodekloud_rin identified as password BruCStnMT5. Further make sure this newly created user is able to perform all operation on the database you created.


Log in to MariaDB as root (or a privileged user):

```bash
sudo mysql -u root -p
```


Enter your root password when prompted.

Create the database kodekloud_db10:

```bash
CREATE DATABASE kodekloud_db10;
```

Create the user kodekloud_rin with password BruCStnMT5:

```bash
CREATE USER 'kodekloud_rin'@'%' IDENTIFIED BY 'BruCStnMT5';
```

"'%' means the user can connect from any host. If you want to restrict to localhost only, use 'localhost'."

Grant all privileges on the database to the user:

```bash
GRANT ALL PRIVILEGES ON kodekloud_db10.* TO 'kodekloud_rin'@'%';
```

Flush privileges to apply changes:

```bash
FLUSH PRIVILEGES;
```

Exit MariaDB:

```bash
EXIT;
```
