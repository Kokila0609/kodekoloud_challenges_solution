# Azure VM Deployment and SSH Configuration Guide

This guide outlines the steps to generate SSH keys, deploy a Linux Virtual Machine in Azure, and connect to it securely.

## Prerequisites

Before deploying the VM, generate a local SSH key pair if you do not already have one.

```bash
ssh-keygen
```
*Press **Enter** to accept the default file location (`~/.ssh/id_rsa`) and optionally provide a secure passphrase.*

---

## Step 1: Create the Azure VM

Run the following Azure CLI command to provision the Ubuntu Virtual Machine. 

```bash
az vm create \
  --resource-group kml_rg_main-cd3d558b439142c7 \
  --name devops-vm \
  --image Ubuntu2204 \
  --size Standard_B1s \
  --storage-sku Standard_LRS \
  --os-disk-size-gb 127 \
  --admin-username azureuser \
  --generate-ssh-keys \
  --location westus \
  --ssh-key-values .ssh/id_rsa.pub
```

---

## Step 2: Verify the Connection

Once the VM creation process completes successfully, note the **publicIpAddress** output from the terminal. Test your SSH connection using the following command:

```bash
ssh azureuser@"public-ip"
```
*(Replace `"public-ip"` with your actual Azure VM public IP address).*

---

## Useful Links

* [DigitalOcean: How to Configure SSH Key-Based Authentication on a Linux Server](https://www.digitalocean.com/community/tutorials/how-to-configure-ssh-key-based-authentication-on-a-linux-server)
* [Microsoft Learn: Create and Use SSH Keys for Linux VMs in Azure](https://learn.microsoft.com/en-us/azure/virtual-machines/linux/mac-create-ssh-keys)
