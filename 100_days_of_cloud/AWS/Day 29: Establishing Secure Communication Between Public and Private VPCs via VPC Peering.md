# VPC Peering and Network Connectivity Demonstration

## Overview & Requirements
The Nautilus DevOps team is demonstrating the use of VPC Peering to establish secure communication between a private VPC and the default public VPC.

### Pre-existing Infrastructure
*   **Public Environment (Default VPC):**
    *   **EC2 Instance Name:** `xfusion-public-ec2`
*   **Private Environment (`xfusion-private-vpc`):**
    *   **VPC CIDR:** `10.1.0.0/16`
    *   **Subnet Name:** `xfusion-private-subnet` (CIDR: `10.1.1.0/24`)
    *   **EC2 Instance Name:** `xfusion-private-ec2`

---

## Step 1: Establish VPC Peering
1. Create a VPC Peering Connection between the **Default VPC** and **`xfusion-private-vpc`**.
2. Name the connection: `xfusion-vpc-peering`.
3. **Important:** You must accept the peering connection request immediately. Failing to activate it will cause a `Blackhole` status when updating the route tables.

---

## Step 2: Configure Route Tables

To enable communication via `xfusion-vpc-peering`, you must update the route tables on both sides of the connection.

### A. Update the Public Subnet Route Table (Default VPC)
*This tells the public EC2 instance how to route traffic destined for the private subnet.*

1. Open the **Amazon VPC Console**.
2. Click **Route tables** in the left navigation pane.
3. Select the route table associated with your public subnet (where `xfusion-public-ec2` resides).
4. Click the **Routes** tab at the bottom, then click **Edit routes**.
5. Click **Add route** and configure the following:
    *   **Destination:** `10.1.1.0/24` (or `10.1.0.0/16` to target the entire private VPC)
    *   **Target:** Select **Peering Connection**, then choose **`xfusion-vpc-peering`**.
6. Click **Save changes**.

### B. Update the Private Subnet Route Table (xfusion-private-vpc)
*This tells the private EC2 instance how to route return traffic back to the public subnet.*

1. While still in **Route tables**, select the route table associated with **`xfusion-private-subnet`**.
2. Click the **Routes** tab at the bottom, then click **Edit routes**.
3. Click **Add route** and configure the following:
    *   **Destination:** Enter your Default VPC CIDR (e.g., `172.31.0.0/16`).
    *   **Target:** Select **Peering Connection**, then choose **`xfusion-vpc-peering`**.
4. Click **Save changes**.

---

## Step 3: Configure Security Groups (Firewalls)

Even with routes configured, AWS will drop network packets unless your security group rules explicitly permit the traffic.

### A. Verify Public Instance Security Group
Ensure the security group for `xfusion-public-ec2` allows inbound SSH (Port 22) traffic from your client IP or `0.0.0.0/0` so that you do not get connection timeouts when using EC2 Instance Connect.

### B. Configure Private Instance Security Group
1. Go to the **EC2 Console** and select **`xfusion-private-ec2`**.
2. Click the **Security** tab at the bottom and click on your **Security Group ID**.
3. Click **Edit inbound rules** and add the following two rules:
    *   **Rule 1 (SSH):** 
        *   **Type:** `SSH` (Port 22)
        *   **Source:** `172.31.0.0/16` (Default VPC CIDR) or the specific private IP of `xfusion-public-ec2`.
    *   **Rule 2 (Ping Test):** 
        *   **Type:** `All ICMP - IPv4`
        *   **Source:** `172.31.0.0/16` (Default VPC CIDR).
4. Click **Save rules**.

---

## Step 4: Configure Passwordless SSH Access

To enable secure, passwordless SSH connections from your local AWS client host into the deployment environment, configure the public key exchange.

### A. Copy the Public Key from the AWS Client Host
On your local AWS client terminal, display and copy your public key:
```bash
cat /root/.ssh/id_rsa.pub
```

### B. Connect to the Public EC2 Instance
Connect to **`xfusion-public-ec2`** using the AWS EC2 Instance Connect console or your existing SSH method.

### C. Append Key and Secure Permissions
Once inside the public EC2 instance terminal, configure the root user's SSH directory:

```bash
# Create the .ssh directory if it does not exist
sudo mkdir -p /root/.ssh

# Open the authorized_keys file and paste your copied public key
sudo vi /root/.ssh/authorized_keys

# Enforce secure ownership and file permissions
sudo chmod 700 /root/.ssh
sudo chmod 600 /root/.ssh/authorized_keys
sudo chown -R root:root /root/.ssh
```

---

## Step 5: Test Connectivity
1. From your local AWS client host, test the passwordless configuration by SSHing directly into the public instance.
2. Once inside `xfusion-public-ec2`, run a ping command to verify the peering connection to the private instance:
   ```bash
   ping <xfusion-private-ec2-private-ip>
   ```
3. Verify that the network packets transmit successfully with 0% packet loss.
