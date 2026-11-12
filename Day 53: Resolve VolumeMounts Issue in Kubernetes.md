# Problem

We encountered an issue with our Nginx and PHP-FPM setup on the Kubernetes cluster this morning, which halted its functionality. Investigate and rectify the issue:
 
 
 
The pod name is nginx-phpfpm and configmap name is nginx-config. Identify and fix the problem.
 
 
Once resolved, copy /home/thor/index.php file from the jump host to the nginx-container within the nginx document root. After this, you should be able to access the website using Website button on the top bar.
 
 
Note: The kubectl utility on the jump-host has been configured to work with the Kubernetes cluster.

# Troubleshooting Steps
## 1. Check Existing Files
```bash
ls
```
Review the ConfigMap configuration:
```bash
cat nginx-config.yaml
```
Review the Pod configuration:
```bash
cat nginx-phpfm.yaml
```
---
## 2. Edit the Nginx ConfigMap
Open the ConfigMap YAML file:
```bash
vi nginx-config.yaml
```
Make the required corrections to the Nginx configuration.
---
## 3. Edit the Pod YAML
Open the Pod YAML:
```bash
vi nginx-phpfm.yaml
```
Make any required corrections to the Pod configuration.
---
## 4. Check the Current Kubernetes Resources
Check the Pod:
```bash
kubectl get pods
```
Or using the `k` alias:
```bash
k get po
```
Check the ConfigMap:
```bash
k get cm
```
---
## 5. Recreate the ConfigMap
Delete the existing ConfigMap:
```bash
k delete cm nginx-config
```
Verify that it has been removed:
```bash
k get cm
```
Create the corrected ConfigMap:
```bash
k apply -f nginx-config.yaml
```
Verify:
```bash
k get cm
```
---
## 6. Recreate the Pod
Check the current Pod:
```bash
k get po
```
Delete the existing Pod:
```bash
k delete po nginx-phpfpm
```
Recreate it using the corrected YAML:
```bash
k apply -f nginx-phpfm.yaml
```
Verify that the Pod is running:
```bash
k get po
```
For additional details if the Pod is not running:
```bash
k describe po nginx-phpfpm
```
---
## 7. Copy `index.php` into the Nginx Container
Copy the local `index.php` file into the Nginx document root:
```bash
k cp ./index.php nginx-phpfpm:/var/www/html/index.php -c nginx-container
```
Alternatively, using the absolute path:
```bash
kubectl cp /home/thor/index.php nginx-phpfpm:/var/www/html/index.php -c nginx-container
```
### Command Breakdown
```bash
k cp ./index.php nginx-phpfpm:/var/www/html/index.php -c nginx-container
```
| Component | Description |
|---|---|
| `k cp` | Copy files between the local machine and a Kubernetes Pod |
| `./index.php` | Source file on the jump host |
| `nginx-phpfpm` | Pod name |
| `/var/www/html/index.php` | Destination inside the container |
| `-c nginx-container` | Specifies the target container |
---
## 8. Verify the File
Check whether `index.php` exists inside the Nginx container:
```bash
k exec nginx-phpfpm -c nginx-container -- ls -l /var/www/html
```
You should see:
```text
index.php
```
You can also inspect the file:
```bash
k exec nginx-phpfpm -c nginx-container -- cat /var/www/html/index.php
```
---
## 9. Verify the Pod
Check the Pod status:
```bash
k get po
```
For more information:
```bash
k get po nginx-phpfpm -o wide
```
Expected state:
```text
STATUS: Running
```
If the Pod contains both Nginx and PHP-FPM containers, ensure that all containers are ready.
For example:
```text
READY   STATUS
2/2     Running
```
---
## 10. Test the Website
Once the Pod is running and `index.php` exists under:
```text
/var/www/html/index.php
```
click the **Website** button on the top bar.
The PHP application should now be accessible.
---
# Important Commands
### Check Pods
```bash
k get po
```
### Check ConfigMaps
```bash
k get cm
```
### Apply ConfigMap
```bash
k apply -f nginx-config.yaml
```
### Apply Pod
```bash
k apply -f nginx-phpfm.yaml
```
### Copy `index.php`
```bash
k cp ./index.php nginx-phpfpm:/var/www/html/index.php -c nginx-container
```
### Verify Document Root
```bash
k exec nginx-phpfpm -c nginx-container -- ls -l /var/www/html
```
### Check Nginx Logs
```bash
k logs nginx-phpfpm -c nginx-container
```
### Check PHP-FPM Logs
```bash
k logs nginx-phpfpm -c php-fpm-container
```
> Replace `php-fpm-container` with the actual PHP-FPM container name if it is different.
---
# Final Verification Checklist
- [ ] `nginx-config` ConfigMap is corrected.
- [ ] ConfigMap has been recreated successfully.
- [ ] `nginx-phpfpm` Pod has been recreated successfully.
- [ ] All containers in the Pod are running.
- [ ] `index.php` has been copied to `/var/www/html/index.php`.
- [ ] `index.php` is visible inside `nginx-container`.
- [ ] Nginx can communicate with PHP-FPM.
- [ ] Website is accessible through the **Website** button.
