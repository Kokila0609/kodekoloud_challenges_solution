# Azure VM Integration: PHP Application to MySQL Database

## Overview & Requirements
The Nautilus DevOps team is integrating a PHP web application hosted on an Azure VM with a remote MySQL database hosted on a separate Azure VM to validate cloud-based database connectivity.

### Infrastructure Baseline
*   **Database Server:** `datacenter-mysql-vm` (To be created in `Central US`)
*   **Application Server:** `datacenter-php-vm` (Pre-existing in `East US`)

---

## Step 1: Provision the MySQL Database VM
Deploy a new virtual machine via the Azure Portal or CLI using the following specifications:

*   **VM Name:** `datacenter-mysql-vm`
*   **Image:** Percona Server for MySQL (Published by Jetware from Azure Marketplace)
*   **Region:** Central US
*   **Size:** Standard B1s
*   **OS Disk Type:** Standard HDD
*   **Authentication Type:** Password
    *   **Username:** `datacenter_admin`
    *   **Password:** `Namin@123456`
*   **Networking (NSG Rules):**
    *   Allow public inbound port `22` (SSH) during creation.
    *   **Post-creation action:** Add an inbound security rule to the Network Security Group (NSG) to open port `3306` (MySQL) to allow remote access.

---

## Step 2: Configure the MySQL Database & Users

1. SSH into the newly created `datacenter-mysql-vm`.
2. Access the isolated Percona MySQL shell:
   ```bash
   sudo /jet/enter mysql
   ```
3. Execute the database initialization script:
   ```sql
   -- Create the application database
   CREATE DATABASE datacenter_db;

   -- Verify database creation
   SHOW DATABASES;

   -- Create the remote application user
   CREATE USER 'datacenter_user'@'%' IDENTIFIED BY 'password123';

   -- Grant privileges on all tables within the target database
   GRANT ALL PRIVILEGES ON datacenter_db.* TO 'datacenter_user'@'%';

   -- Apply changes instantly (Ensure correct spelling)
   FLUSH PRIVILEGES;
   ```

---

## Step 3: Configure the PHP Application Server

1. Retrieve the **Public IP Address** of the `datacenter-mysql-vm`.
2. From your lab host, log into the application server using the pre-configured passwordless SSH setup:
   ```bash
   ssh azureuser@<php-vm-public-ip>
   ```
3. Open the database connection script using a text editor:
   ```bash
   sudo vi /var/www/html/db_test.php
   ```
4. Update the connection variables to target your database deployment. Ensure `$port` is provided as the fifth argument in the `mysqli` instantiation:
   ```php
   <?php
       $servername = "<mysql-vm-public-ip>";
       $username = "datacenter_user";
       $password = "password123";
       $dbname = "datacenter_db";
       $port = 3306;

       // Create connection utilizing the explicit port variable
       $conn = new mysqli($servername, $username, $password, $dbname, $port);

       // Check connection status
       if ($conn->connect_error) {
           die("Connection failed: " . $conn->connect_error);
       }
       echo "Connected successfully";
   ?>
   ```

---

## Step 4: Verification and Quality Assurance

### A. Run Local PHP Code Linting
Before opening the web route, verify that the application script contains no code format or syntax errors:
```bash
php -l /var/www/html/db_test.php
```
*Expected Output:* `No syntax errors detected in /var/www/html/db_test.php`

### B. Test the Network Endpoint
Simulate an HTTP browser request locally to verify connectivity over the network bridge:
```bash
curl http://localhost/db_test.php
```
*Expected Output:* `Connected successfully`

### C. Final Browser Validation
Open your web browser and navigate to the application endpoint to confirm successful integration:
```text
http://<PHP_VM_PUBLIC_IP>/db_test.php
```
Verify that the screen successfully prints: **Connected successfully**.
