Here's a clean and well-formatted **Markdown (`.md`) file** based on your provided Nginx installation and configuration steps. You can copy this content into a file named something like `nginx-setup.md` for your GitHub repo.

---

### 📄 `nginx-setup.md`

````markdown
# 🚀 Nginx Installation and SSL Configuration Guide (CentOS/RHEL-based)

This guide walks you through installing **Nginx** on a CentOS/RHEL-based system and configuring it to use an **SSL certificate**.

---

## 🧰 Prerequisites

- A CentOS or RHEL-based server
- Root or `sudo` privileges
- SSL certificate and key files (`nautilus.crt` and `nautilus.key`)

---

## 🔧 Step-by-Step Instructions

### 1. Install EPEL Repository
```bash
sudo yum install epel-release
````

### 2. Install Nginx

```bash
sudo yum install nginx
```

### 3. Test Nginx Configuration

```bash
sudo nginx -t
```

> ✅ You should see a message saying `syntax is ok` and `test is successful`.

### 4. Start Nginx and Visit the Default Page

```bash
sudo systemctl start nginx
```

Open your browser and visit:

```
http://your_server_ip/
```

You should see the Nginx welcome page.

---

## 🔐 SSL Configuration

### 5. Create SSL Directory

```bash
sudo mkdir -p /etc/nginx/ssl
```

### 6. Move SSL Certificate and Key

```bash
sudo mv /tmp/nautilus.crt /etc/nginx/ssl/
sudo mv /tmp/nautilus.key /etc/nginx/ssl/
```

---

## 📝 7. Configure SSL Server Block

Edit your Nginx configuration file (e.g., `/etc/nginx/nginx.conf` or create a new file under `/etc/nginx/conf.d/ssl.conf`).

```nginx
server {
    listen 443 ssl;
    server_name your_domain.com;  # Replace with your domain or server IP

    ssl_certificate /etc/nginx/ssl/nautilus.crt;
    ssl_certificate_key /etc/nginx/ssl/nautilus.key;

    root /var/www/html;  # Adjust path as needed
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

### 8. Restart Nginx to Apply Changes

```bash
sudo systemctl restart nginx
```

---

## ✅ Verification

Visit your site using `https`:

```
https://your_domain.com
```

> You should see the secure version of your site now!

---

## 📌 Notes

* Make sure ports **80** (HTTP) and **443** (HTTPS) are open in your firewall.
* To enable Nginx to start on boot:

  ```bash
  sudo systemctl enable nginx
  ```

---

## 📂 File Structure Summary

```
/etc/nginx/ssl/
├── nautilus.crt
└── nautilus.key
```

---

## 🧼 Optional: Redirect HTTP to HTTPS

To force HTTPS, you can add this server block:

```nginx
server {
    listen 80;
    server_name your_domain.com;

    return 301 https://$host$request_uri;
}
```

---

## 🛠 Troubleshooting

* Check Nginx logs:

  ```bash
  sudo tail -f /var/log/nginx/error.log
  ```

* If you see permission errors, ensure certificate files are readable by Nginx.

---

## 👨‍💻 Author

Created by: *\[kokila]*
Last updated: `September 5, 2025`

```


