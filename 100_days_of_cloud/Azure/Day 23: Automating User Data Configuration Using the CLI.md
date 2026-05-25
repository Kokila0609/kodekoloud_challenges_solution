# Azure VM Deployment & Troubleshooting Guide

A collection of useful commands and configuration scripts for deploying compliant Linux virtual machines in Azure sandbox lab environments.

## 1. Initial Verification

Verify your active subscription profile:
```bash
az account show --output table
```

List pre-existing resource groups in the environment:
```bash
az group list --output table
```

---

## 2. Boot Script Configuration Mappings

You can automate the installation of Nginx using one of two formats via initialization hooks.

### Option A: Cloud-Config YAML Format (`cloud-init.txt`)
Create a local file named `cloud-init.txt` on your machine. This script forces the machine to upgrade packages, install, and start Nginx immediately upon booting up.

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
runcmd:
  - systemctl start nginx
  - systemctl enable nginx
```

### Option B: Pure Bash Script Format (`nginx-setup.sh`)
Alternatively, generate an equivalent shell script directly inside the cloud shell:

```bash
cat << 'EOF' > nginx-setup.sh
#!/bin/bash
# Update package repositories
apt-get update -y

# Install Nginx silently
apt-get install nginx -y

# Start Nginx service immediately
systemctl start nginx

# Enable Nginx to automatically start if the VM reboots
systemctl enable nginx
EOF
```

---

## 3. Creating the Virtual Machine

Deploy the compliant Virtual Machine using the strict storage rules enforced by the lab policy (`Standard_LRS` storage SKU and disk under 128 GB):

```bash
az vm create \
  --resource-group kml_rg_main-f4c0cd747ffa4190 \
  --name xfusion-vm \
  --image Ubuntu2204 \
  --size Standard_B1s \
  --storage-sku Standard_LRS \
  --os-disk-size-gb 127 \
  --public-ip-address xfusion-vm-publicip \
  --public-ip-sku Standard \
  --admin-username azureuser \
  --generate-ssh-keys \
  --location eastus \
  --custom-data nginx-setup.sh
```

---

## 4. Networking Configurations

### Open Port 80 for HTTP Web Traffic
```bash
az vm open-port \
  --resource-group kml_rg_main-f4c0cd747ffa4190 \
  --name xfusion-vm \
  --port 80 \
  --priority 800
```

### Create a Standalone Public IP Address
If required, provision a standalone static public IP resource:
```bash
az network public-ip create \
  --resource-group kml_rg_main-d0793582ea3b4b3b \
  --name nautilus-vm-ip \
  --sku Standard \
  --allocation-method Static
```
*Expected output slice:* `"ipAddress": "20.127.103.158"`

---

## 5. Troubleshooting SSH and Missing Public IP Links

If SSH connections hang, verify if the public IP is correctly linked to the virtual network card interface.

### Step 1: Query the Current IP Assignments
```bash
az network nic list \
  --resource-group kml_rg_main-d0793582ea3b4b3b \
  --query "[].ipConfigurations[].{Name:name, PublicIP:publicIpAddress.id}" \
  --output json
```

*Example of a broken, missing public IP assignment context (`null`):*
```json
[
  {
    "Name": "ipconfignautilus-vm",
    "PublicIP": null
  }
]
```

### Step 2: List Known Public IP Resources
```bash
az network public-ip list \
  --resource-group kml_rg_main-d0793582ea3b4b3b \
  --query "[].name" \
  --output tsv
```
*Output instances:* 
* `nautilus-vm-ip`
* `nautilus-vmPublicIP`

### Step 3: Force Update the NIC Binding Map
Remap the valid public IP back onto your network config interface profile name:
```bash
az network nic ip-config update \
  --resource-group kml_rg_main-d0793582ea3b4b3b \
  --nic-name nautilus-vmVMNic \
  --name ipconfignautilus-vm \
  --public-ip-address "nautilus-vmPublicIP"
```

### Step 4: Review Script Installation Output Logs
SSH into the machine and check the cloud-init installation steps execution tracking details:
```bash
cat /var/log/cloud-init-output.log
```

---

## 6. Purging and Resetting Broken/Stuck Resources

If policy actions block configurations or throw structural conflicts, manually wipe the individual components or delete the entire deployment state block to restart cleanly.

### Delete Isolated Storage Managed Disks
```bash
az resource delete \
  --ids /subscriptions/f0c3bcdd-5ce2-4fa0-8cf3-41559747512b/resourceGroups/KML_RG_MAIN-D0793582EA3B4B3B/providers/Microsoft.Compute/disks/nautilus-vm_disk1_ffcdf280ae5f4b07a8e196aac5e3724a
```

### Clean Up `nautilus-vm` Deployment State
```bash
az vm delete --resource-group kml_rg_main-d0793582ea3b4b3b --name nautilus-vm --yes
```

### Complete Stack Wipe Sequence (`xfusion-vm`)
Run this combined stack purge order to completely clear out dependent blocks:
```bash
# Remove instance
az vm delete --resource-group kml_rg_main-f4c0cd747ffa4190 --name xfusion-vm --yes

# Delete NIC
az network nic delete --resource-group kml_rg_main-f4c0cd747ffa4190 --name xfusion-vmVMNic

# Drop public IP
az network public-ip delete --resource-group kml_rg_main-f4c0cd747ffa4190 --name xfusion-vm-publicip

# Clear underlying OS storage disk artifacts
az disk delete --resource-group kml_rg_main-f4c0cd747ffa4190 -n xfusion-vm_OsDisk_1_* --yes
```
