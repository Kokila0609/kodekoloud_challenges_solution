# AWS EC2 Nginx Web Server Setup Guide for Nautilus DevOps Team

This guide outlines the steps to deploy a public Ubuntu EC2 instance (`devops-ec2`) pre-configured with Nginx using a User Data automation script.

---

## 1. Create the Security Group (Open Port 80)

Before creating the instance, we need a security group that opens the firewall for web traffic.

1. Log in to the [AWS Management Console](https://get.ec2).
2. Navigate to the **EC2 Dashboard**.
3. In the left menu under **Network & Security**, click **Security Groups**.
4. Click **Create security group**.
5. Configure the following details:
   * **Security group name**: `devops-nginx-sg`
   * **Description**: `Allow HTTP web traffic from the internet`
   * **VPC**: Select your default VPC.
6. Under **Inbound rules**, click **Add rule**:
   * **Type**: **HTTP**
   * **Source**: **Anywhere-IPv4** (`0.0.0.0/0`)
7. Click **Create security group** at the bottom.

---

## 2. Launch the EC2 Instance

1. Go back to the **EC2 Dashboard** and click **Instances** in the left menu.
2. Click the **Launch instances** button.
3. Under **Name and tags**:
   * **Name**: `devops-ec2`
4. Under **Application and OS Images (Amazon Machine Image)**:
   * Select **Ubuntu**.
   * Choose any available **Ubuntu Server (AMI)** from the dropdown (e.g., Ubuntu 24.04 LTS or 22.04 LTS - Free Tier Eligible).
5. Under **Instance type**:
   * Choose `t2.micro` (or your preferred instance size).
6. Under **Key pair (login)**:
   * Select an existing key pair or click *Create new key pair* if you need SSH terminal access later.

---

## 3. Configure Network and Firewall

1. Under **Network settings**, click **Edit** (top right of the network box).
2. Ensure **Auto-assign public IP** is set to **Enable**.
3. Under **Firewall (security groups)**, choose **Select existing security group**.
4. Check the box for the **`devops-nginx-sg`** group created in Step 1.

---

## 4. Add the User Data Script for Nginx Automation

1. Scroll down to the very bottom and expand the **Advanced details** section.
2. Scroll to the last text box labeled **User data**.
3. Copy and paste the following bash script directly into the box:

```bash
#!/bin/bash
# Update the package index
apt-get update -y

# Install Nginx
apt-get install nginx -y

# Start and enable Nginx service so it runs automatically
systemctl start nginx
systemctl enable nginx
```

---

## 5. Review and Launch

1. Review your settings in the **Summary** panel on the right side.
2. Click **Launch instance**.
3. Click **View all instances** to track its deployment status.

---

## 6. Verify the Web Server

1. Once the instance state changes to **Running**, click on `devops-ec2`.
2. Copy its **Public IPv4 address** from the details tab.
3. Open your computer's terminal and test the web server using `curl`:
   ```bash
   curl http://<YOUR_PUBLIC_IP> -v
   ```
4. You should instantly receive a **200 OK** response and see the HTML source code for the default Nginx greeting page.
