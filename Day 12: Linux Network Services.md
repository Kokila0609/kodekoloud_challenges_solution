## Troubleshooting httpd Service on stapp01 Server ##

### 1. Login to the Target Server ###

``` bash
ssh tony@stapp01

#Switch to the root user
sudo su
```

### 2. Check httpd Service Status ###

``` bash
systemctl status httpd
```
#### If the service is inactive, check for configuration issues ####

``` bash
httpd -t
```

### 3. Edit the Apache Configuration ###

Open the httpd.conf file:

```bash
vi /etc/httpd/conf/httpd.conf
```
Uncomment and update the ServerName line (adjust the IP and port according to your task):

```bash
ServerName 172.16.238.10:8081
```
### 4. Check for Port Conflicts ###

#### Find if another process is using the required port (replace 8081 as needed): ####

``` bash
netstat -anp | grep LISTEN | grep ":8081"
```


#### Kill the conflicting process (replace 450 with the actual PID from the above command): ####

```
kill -9 450
```

### 5. Restart httpd Service ###

```
systemctl restart httpd
systemctl status httpd
```

### 6. Add Firewall Rule (Iptables) ###

#### Allow the required port through the firewall (replace 8081 with the correct port): ####

```
sudo iptables -I INPUT -p tcp -m tcp --dport 8081 -j ACCEPT
sudo iptables-save > /etc/sysconfig/iptables
cat /etc/sysconfig/iptables
```

### 7. Verify from Jump Server ###

Check if the service is now accessible from the jump server or required endpoint.








