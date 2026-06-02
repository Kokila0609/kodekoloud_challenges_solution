Here is your cleaned, structured, and properly formatted Markdown (.md) documentation for the task:
## Nginx and PHP-FPM 8.2 Setup Guide on App Server 1
This guide covers the installation, configuration, and troubleshooting steps required to host a dynamic PHP application using Nginx and a custom UNIX socket file stream.
------------------------------
## 1. Initial Server Connection## SSH to the Designated App Server
Connect to your specific assignment target (example uses App Server 1):

ssh tony@stapp01

## Elevate Privileges to Root
Switch to the root user to execute global administrative tasks without prefixing sudo:

sudo -i

------------------------------
## 2. Package Installation & PHP 8.2 Stream Activation## Install Nginx Web Server

dnf install -y nginx

## Reset and Enable the PHP 8.2 Channel
Clear old DNF module history and activate the 8.2 stream repository:

dnf module reset php -y


## Download PHP 

dnf module -y install php:8.2/common

## Verify the Active PHP-FPM Version
Ensure your system registers version 8.2 exactly:

php-fpm -v

------------------------------
## 3. UNIX Socket & Process Manager Setup## Create the Socket Runtime Parent Directory

mkdir -p /var/run/php-fpm

## Edit the Default Pool Configuration File
Open the system pool configuration file:

nano /etc/php-fpm.d/www.conf

Locate the listen = rule inside the file and update it to use your designated socket path:

listen = /var/run/php-fpm/default.sock

Locate or add security permission settings right beneath the socket path definition to ensure Nginx access:

listen.owner = nginx
listen.group = nginx
listen.mode = 0660

Save and close the editor using Ctrl + O and Ctrl + X.
## Start and Enable the PHP-FPM Daemon

systemctl enable --now php-fpm

## Double-Check Socket Generation and Permissions
Confirm that the socket file is alive and correctly assigned to the nginx group:

ls -l /var/run/php-fpm/default.sock

Expected Output Format:
srw-rw----. 1 nginx nginx 0 Jun 2 17:39 /var/run/php-fpm/default.sock
If ownership falls back to root or apache, repair it immediately:

chown nginx:nginx /var/run/php-fpm/default.sock

------------------------------
## 4. Nginx Server Block Configuration## Open the Nginx Core Configuration File

nano /etc/nginx/nginx.conf

(Note: If your environment manages specific ports inside discrete domain files, edit the corresponding module in /etc/nginx/conf.d/ instead).
## Edit the Server Block Parameters
Update your target block (example utilizes port 8093) to append the index.php preference and routing proxy block:

server {
    listen       8093;
    listen       [::]:8093;
    server_name  stapp01;
    root         /var/www/html;

    # 1. Add index.php to the standard root indexing list
    index        index.php index.html index.htm;

    # Load configuration files for the default server block.
    include /etc/nginx/default.d/*.conf;

    # 2. Map dynamic .php paths straight to your custom UNIX socket
    location ~ \.php$ {
        include        fastcgi_params;
        fastcgi_pass   unix:/var/run/php-fpm/default.sock;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }

    # Retain the standard error tracking configurations
    error_page 404 /404.html;
    location = /404.html {
    }

    error_page 500 502 503 504 /50x.html;
    location = /50x.html {
    }
}

------------------------------
## 5. Web Directory & Permissions Calibration## Assign Proper Web Root Permissions
Ensure the file manager engine permissions do not deny access to the execution files:

chown -R nginx:nginx /var/www/html
chmod -R 755 /var/www/html

------------------------------
## 6. Troubleshooting: Resolving 404/502 Error Drops## Search Configuration Directives for Conflicting Paths
If you experience a 404 message while files exist in your root directory, run an inspection query:

grep -R "root" /etc/nginx/

## The Conflict Fix (Removing Conflicting Modular Presets)
When PHP-FPM installs on standard RHEL configurations, an included configuration file (/etc/nginx/default.d/php.conf) can hijack local routing logic. Move it out of the path chain to clear conflicts:

# Rename the blocking preset profile out of the way
mv /etc/nginx/default.d/php.conf /etc/nginx/default.d/php.conf.bak
# Validate configuration formatting syntax
nginx -t
# Clean out the application engine caches
systemctl restart nginx

------------------------------
## 7. System Connection Verification
Run a final curl execution check against the designated web directory to verify full functionality:

curl http://stapp01:8099/index.php

## Successful Output:

Welcome to xFusionCorp Industries

------------------------------
