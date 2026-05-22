# 🚀 NGINX Load Balancer Setup Guide (CentOS/RHEL-based)

This guide walks you through installing **NGINX** on a CentOS/RHEL-based system and configuring it as a **reverse proxy load balancer** for multiple Apache backend servers running on port `3003`.

---

## 🧰 Prerequisites

- A CentOS or RHEL-based server
- Root or `sudo` privileges
- IP addresses of backend servers
- Apache HTTPD installed on backend servers (`stapp01`, `stapp02`, `stapp03`)
- Backend servers running HTTPD on **port 3003**

---

## 📦 Step 1: Install NGINX

```bash
sudo yum install epel-release -y
sudo yum install nginx -y
```

---

## ⚙️ Step 2: Configure NGINX

Edit the NGINX configuration file:

```bash
vi /etc/nginx/nginx.conf
```

Update the `http {}` block as shown below:

```nginx
http {
    upstream backend_servers {
        server 172.16.238.10:3003;
        server 172.16.238.11:3003;
        server 172.16.238.12:3003;
        # Optional: Add weights for weighted round-robin
        # server 192.168.1.100:3003 weight=3;
        # server 192.168.1.101:3003 weight=1;
    }

    server {
        listen       80;
        listen       [::]:80;
        server_name  172.16.238.14;
        root         /usr/share/nginx/html;

        location / {
            proxy_pass http://backend_servers;
        }

        error_page 404 /404.html;
        location = /404.html { }

        error_page 500 502 503 504 /50x.html;
        location = /50x.html { }

        include /etc/nginx/default.d/*.conf;
    }
}
```

---

## 🔁 Step 3: Enable and Start NGINX

```bash
sudo systemctl enable nginx
sudo systemctl start nginx
sudo systemctl status nginx
```

---

## 🧪 Step 4: Validate NGINX Configuration

Test the configuration syntax:

```bash
sudo nginx -t
```

If you encounter errors like:

- Invalid number of arguments in `server` directive ➜ Check `upstream` block.
- Directive not allowed ➜ Ensure everything is inside `http {}`.

Fix and recheck:

```bash
vi /etc/nginx/nginx.conf
sudo nginx -t
sudo systemctl restart nginx
```

---

## 🛠️ Step 5: Configure Backend Apache Servers

Repeat on each backend server (`stapp01`, `stapp02`, `stapp03`):

### Edit Apache config:

```bash
vi /etc/httpd/conf/httpd.conf
```

Set the `ServerName`:

```apache
ServerName stapp0X  # Replace X with 1, 2, or 3 accordingly
```

### Restart HTTPD:

```bash
sudo httpd -t
sudo systemctl restart httpd
sudo systemctl status httpd
```

---

## 🧪 Step 6: Verify Apache Port and Connectivity

Ensure Apache is running on **port 3003**:

```bash
sudo ss -tuln | grep :3003
```

Then from the load balancer (`stlb01`):

```bash
curl -I http://172.16.238.14
```

Expected output:

```http
HTTP/1.1 200 OK
```

---

## ✅ Summary

| Component  | Hostname  | IP Address     | Role             | Port |
|------------|-----------|----------------|------------------|------|
| Load Balancer | stlb01    | 172.16.238.14  | NGINX Proxy      | 80   |
| App Server    | stapp01   | 172.16.238.10  | Apache Backend   | 3003 |
| App Server    | stapp02   | 172.16.238.11  | Apache Backend   | 3003 |
| App Server    | stapp03   | 172.16.238.12  | Apache Backend   | 3003 |

---

## 📌 Notes

- Ensure backend servers are reachable from the NGINX machine.
- If HTTPD is not running on port 80, update the `upstream` block accordingly (e.g., `:3003`).
- Check `journalctl -xeu nginx.service` for troubleshooting NGINX startup issues.


# Troubleshooting Nginx Load Balancer (LBR) Issues

This document details the troubleshooting steps used to resolve an issue where the Nginx Load Balancer server (`stlb01`) was not properly distributing traffic to all backend application servers (`stapp01`, `stapp02`, `stapp03`).

---

## 1. Initial Verification (The False Positive)
Running a single configuration check using `curl -I` returned a `200 OK` status, but the header revealed that Nginx itself was responding locally, rather than proxying requests:

```bash
curl -I http://stlb01:80
```
* **Symptom:** The `Server` header returned `nginx/1.20.1` instead of the expected backend application server header (`Apache`).

---

## 2. Diagnosing Backend Application Ports
To find out why Nginx could not communicate with the backend, we inspected the active network sockets on the application servers (`stapp01`) using the socket statistics tool:

```bash
ss -tunlp | grep :80
```

* **Discovery:** The command showed that the Apache service (`httpd`) was actually listening on custom port **`8088`**, not the standard port `80`:
  ```text
  tcp   LISTEN 0      511   *:8088   *:*   users:(("httpd",pid=18482,fd=4)...)
  ```
* **Root Cause 1:** The Nginx upstream block was missing port declarations, causing it to default to port `80` where no application was listening.

---

## 3. Fixing the Nginx Configuration
To correct this, the Nginx configuration file on the LBR server (`/etc/nginx/nginx.conf`) was updated to route traffic to port `8088` and to include the missing reverse proxy logic.

### Broken Configuration (Default Static Server)
The `root` directive was hijacking traffic globally, and the `location /` reverse proxy block was entirely missing:
```nginx
upstream backend_servers {
    server stapp01:8088;
    server stapp02:8088;
    server stapp03:8088;
}
server {
    listen       80;
    server_name  stlb01;
    root         /usr/share/nginx/html; # Overrode proxy rules
}
```

### Fixed Configuration (Load Balancer)
Removed the global `root` directive and added the `location /` reverse proxy container block:
```nginx
upstream backend_servers {
    server stapp01:8088;
    server stapp02:8088;
    server stapp03:8088;
}

server {
    listen       80;
    listen       [::]:80;
    server_name  stlb01;

    location / {
        proxy_pass http://backend_servers;
        proxy_pass_header Server; # Passes backend "Apache" header to client
    }

    # System default configurations
    include /etc/nginx/default.d/*.conf;
    error_page 404 /404.html;
    location = /404.html {}
}
```

### Applying the Changes
Validated configuration file syntax and gracefully reloaded the Nginx daemon:
```bash
sudo nginx -t
sudo systemctl reload nginx
```

---

## 4. Final Validation Loop
To confirm that traffic was actively splitting across all three application servers, a continuous validation loop was run from the LBR server to fetch header data:

```bash
for i in {1..6}; do curl -I -s http://stlb01:80 | grep -E "HTTP/|Server:|ETag:"; echo "---"; done
```

### Output Analysis
```text
HTTP/1.1 200 OK
Server: Apache/2.4.62 (CentOS Stream)
ETag: "22-6526242c6d6e5"
---
HTTP/1.1 200 OK
Server: Apache/2.4.62 (CentOS Stream)
ETag: "22-6526242ca061b"
---
HTTP/1.1 200 OK
Server: Apache/2.4.62 (CentOS Stream)
ETag: "22-6526242cd5f46"
---
```

* **Verification 1:** The `Server: Apache/2.4.62` line proves that Nginx is successfully proxying requests backward to the application layer.
* **Verification 2:** The `ETag` string values strictly alternate across 3 unique variants in a continuous sequence, validating that the Round-Robin algorithm is actively balancing traffic across all three backend nodes.

