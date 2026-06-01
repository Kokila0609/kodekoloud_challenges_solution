

### 1. Update System and Install Apache
```bash
sudo yum update -y
sudo yum install httpd -y
sudo systemctl start httpd
sudo systemctl enable httpd
```

### 2. Configure Apache Listening Port
Open the Apache configuration file:
```bash
sudo vi /etc/httpd/conf/httpd.conf
```
Locate line 100 (or search for `Listen 80`) and change it to:
```apache
Listen 8084  
```
Restart Apache to apply the changes:
```bash
sudo systemctl restart httpd
```

### 3. Install and Verify `ss` Utility
Check if `ss` is already installed:
```bash
ss -V
```
If it is not installed, install it using `iproute`:
```bash
sudo yum install iproute
```
Verify that Apache is successfully listening on port 8084:
```bash
ss -tunlp | grep :8084                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          
```

### 4. Copy Backups from Jump Host
Run this command from the `jump_host` (as user `thor`) to securely copy both folders into the temporary directory of your application server (`stapp03`):
```bash
scp -r /home/thor/blog /home/thor/demo banner@stapp03:/tmp/
```

### 5. Move Folders to Apache Web Directory
Log back into your application server `stapp03` as `root` and move the transferred directories into `/var/www/html/` so that Apache can manage them natively:
```bash
mv /tmp/blog /var/www/html/
mv /tmp/demo /var/www/html/
```

### 6. Update Apache Routing Configurations
Open the main Apache configuration file:
```bash
vi /etc/httpd/conf/httpd.conf
```
Scroll down to the bottom of the file and paste the following configuration block to define your custom mappings and access rules:
```apache
# Ensure Apache is listening on the expected port
Listen 6300

# Configuration for the Blog website
Alias /blog/ "/var/www/html/blog/"
<Directory "/var/www/html/blog">
    Options Indexes FollowSymLinks
    AllowOverride None
    Require all granted
</Directory>

# Configuration for the Demo website
Alias /demo/ "/var/www/html/demo/"
<Directory "/var/www/html/demo">
    Options Indexes FollowSymLinks
    AllowOverride None
    Require all granted
</Directory>
```

### 7. Restart and Verify Apache
Test your syntax adjustments for hidden typos before restarting the daemon:
```bash
httpd -t
```
If it reports `Syntax OK`, restart the service to apply the configuration:
```bash
systemctl restart httpd  
```
Verify that both sites are online and returning content locally by querying them via `curl`:
```bash
curl -I http://localhost:6300/blog/
curl -I http://localhost:6300/demo/
```
