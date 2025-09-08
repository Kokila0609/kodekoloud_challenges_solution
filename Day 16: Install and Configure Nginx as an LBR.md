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

