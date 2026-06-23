# Azure VM Connectivity Troubleshooting Guide: Package Installation Failure

This documentation outlines the investigation steps and resolution process for restoring outbound internet connectivity on the Azure VM `nautilus-vm`.

---

## 📋 Problem Description

The Nautilus DevOps team encountered an issue where `nautilus-vm` could not install or update packages using `apt`. The command hung indefinitely at 0% when attempting to reach the Ubuntu archive repositories.

### Environment Details:
* **Target VM**: `nautilus-vm`
* **Authentication**: SSH private key located at `/root/.ssh/id_rsa` on the `azure-client` host.
* **User**: `azureuser`

---

## 🛠️ Step-by-Step Investigation & Root Cause Analysis

### 1. Access the Virtual Machine
Establish an SSH connection to the target VM using the pre-configured private key from the client machine:
```bash
ssh azureuser@<vm-public-ip>
```

### 2. Verify the Connectivity Failure
Attempt to update the local package index to replicate the reported issue:
```bash
sudo apt update
```
*Observation: The process hangs at the following state, indicating a network timeout:*
```text
0% [Connecting to azure.archive.ubuntu.com (52.147.219.192)]
```

### 3. Test General Internet Routing
Verify if raw IP packets can reach external networks:
```bash
ping -c 3 8.8.8.8
```
*Observation: The ping test fails with 100% packet loss, confirming zero outbound routing.*

### 4. Audit Local Operating System Firewall
Check if the internal Ubuntu firewall (UFW) is actively dropping packets:
```bash
sudo ufw status
```
*Output:*
```text
Status: inactive
```
*Conclusion: The internal OS firewall is turned off. The traffic block is originating at the Azure cloud infrastructure layer.*

---

## 🔍 Infrastructure Verification Checklist

When local operating system firewalls are inactive, check the following Azure network components:

1. **Missing Public IP Address**: Verify under *Azure Portal -> Virtual Machines -> nautilus-vm -> Networking* that a valid Public IP is bound to the Network Interface (NIC).
2. **Load Balancer Restrictions**: If the VM sits behind a Standard Load Balancer (`xfusion-lb`) without an explicit instance-level public IP, ensure an *Outbound Rule* or an *Azure NAT Gateway* is configured to allow internet access.
3. **Network Security Group (NSG) Rules**: Inspect the **Outbound security rules** tab of the NSG attached to the VM or its subnet. Look for custom rules taking precedence over default system rules.

---

## 🎯 Root Cause & Resolution

### Root Cause
An audit of the Network Security Group revealed a custom outbound rule named **`Block-All-Outbound`** which was explicitly dropping all traffic destined for the internet, effectively blocking ports `80` (HTTP), `443` (HTTPS), and `53` (DNS) required by `apt`.

### Resolution Steps
1. Log in to the [Azure Portal](https://azure.com).
2. Navigate to **Network security groups** and click on the NSG assigned to `nautilus-vm`.
3. Select **Outbound security rules** from the settings panel on the left.
4. Locate the **`Block-All-Outbound`** rule.
5. Click the context menu (three dots `...`) on the right side of the rule and select **Delete**.
6. Wait a few seconds for the security group changes to propagate across the network interface.

---

## ✅ Verification of Success

Return to the terminal of `nautilus-vm` and run the update script again:
```bash
sudo apt update
```


