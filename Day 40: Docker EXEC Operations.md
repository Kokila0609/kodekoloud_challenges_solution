# Apache2 Installation and Port Configuration Guide

Follow these steps to install and configure Apache2 to run on port `8087` inside your Ubuntu container.

Configure Apache to listen on port 8087 instead of default http port. Do not bind it to listen on specific IP or hostname only, i.e it should listen on localhost, 127.0.0.1, container ip, etc

### 1. Get the Container ID and Name
Run this command on your host machine to locate your target container:
```bash
docker ps
```

### 2. Access the Container Shell
Jump inside the running container's terminal:
```bash
docker exec -it <container_name_or_id> bash
```

### 3. Update the Package Lists
Synchronize your container's package index with the remote repositories:
```bash
apt update
```

### 4. Install Apache2
Install the Apache web server package automatically without prompts:
```bash
apt install -y apache2
```

### 5. Modify the Ports and Configuration Files
Update the network bindings and server name declarations to switch from the default port `80` to `8087`:
```bash
# Change the global listening port
sed -i 's/Listen 80/Listen 8087/' /etc/apache2/ports.conf

# Update the default VirtualHost port
sed -i 's/<VirtualHost \*:80>/<VirtualHost \*:8087>/' /etc/apache2/sites-available/000-default.conf

# Define the local ServerName in the site configuration
sed -i 's/#ServerName www.example.com/ServerName  127.0.0.1/' /etc/apache2/sites-available/000-default.conf

# Add a global ServerName to suppress FQDN warnings
echo "ServerName localhost" >> /etc/apache2/apache2.conf
```

### 6. Restart the Apache Service
Apply all configuration changes by restarting the web server:
```bash
service apache2 restart
```
