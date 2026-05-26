# MariaDB Installation and Configuration Guide

This guide covers the step-by-step process to install, secure, and configure MariaDB on RHEL-based systems.

## Prerequisites

Enable the core plugins and Code Ready Builder (CRB) repository:

```bash
sudo dnf install -y dnf-plugins-core
sudo dnf config-manager --set-enabled crb || true
```

## Step 1: Add Official MariaDB Repository

Run the following command to add the official MariaDB 10.11 repository to your system:

```bash
sudo tee /etc/yum.repos.d/MariaDB.repo >/dev/null <<'EOF'
# MariaDB 10.11 (example)
[mariadb]
name = MariaDB
baseurl = https://mirror.mariadb.org/yum/10.11/rhel/$releasever/$basearch
gpgkey=https://mariadb.org/mariadb_release_signing_key.asc
gpgcheck=1
enabled=1
EOF
```

## Step 2: Install MariaDB

Install both the server and client packages:

```bash
sudo dnf -y install MariaDB-server MariaDB-client
```

## Step 3: Start and Enable the Service

Configure MariaDB to start automatically at system boot and start the service immediately:

```bash
sudo systemctl enable mariadb
sudo systemctl start mariadb
```

## Step 4: Verify the Installation

Check the installed version and ensure the service is running without opening a pager:

```bash
mariadb --version
sudo systemctl status mariadb --no-pager
```

## Step 5: Secure MariaDB

Run the security script to remove anonymous users, disallow remote root logins, and remove the test database:

```bash
sudo mysql_secure_installation
```

---

## Step 6: Database and User Setup

### 1. Log in to MariaDB
Log in as the root user using socket authentication:

```bash
sudo mariadb
```

### 2. Create the Database
Create a new database named `kodekloud_db9`:

```sql
CREATE DATABASE kodekloud_db9;
```

### 3. Create the Database User
Create the user `kodekloud_tim` (replace the placeholder text with your actual secure password):

```sql
CREATE USER 'kodekloud_tim'@'localhost' IDENTIFIED BY 'REPLACE_WITH_LONG_RANDOM_PASSWORD';
```

### 4. Grant Privileges
Grant the user full permissions on all tables within the newly created database:

```sql
GRANT ALL PRIVILEGES ON kodekloud_db9.* TO 'kodekloud_tim'@'localhost';
```

### 5. Verify Permissions
Confirm that the privileges were applied correctly to the user:

```sql
SHOW GRANTS FOR 'kodekloud_tim'@'localhost';
```

---

## Useful Links
* [How to Configure MariaDB on Linux](https://www.youstable.com/blog/how-to-configure-mariadb-on-linux/)
