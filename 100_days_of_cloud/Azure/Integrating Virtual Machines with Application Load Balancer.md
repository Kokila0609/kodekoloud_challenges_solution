### Requirement:

The Nautilus DevOps team is currently working on setting up a simple application on the Azure cloud. They aim to establish an Azure Load Balancer in front of a Virtual Machine (VM) where an Nginx server is currently running. While the Nginx server currently serves a sample page, the team plans to deploy the actual application later.

Set up an Azure Load Balancer named xfusion-lb.

Configure the Load Balancer’s frontend IP configuration with the name xfusion-lb-ip and assign a public IP address with the same name (xfusion-lb-ip).

Create a backend pool named xfusion-backend-pool and add the VM running Nginx to this pool.

Create a health probe named xfusion-health-probe on port 80 to check the VM's health.

Set up a load balancer rule named xfusion-lb-rule to route traffic on port 80 to the backend pool on port 80.

Add an inbound rule to the existing NSG of the VM to allow HTTP traffic on port 80.


## Steps: 

# Azure Load Balancer Setup Guide for Nautilus DevOps Team

This guide outlines the steps to deploy a public Azure Load Balancer (`xfusion-lb`) in front of an existing Nginx web server VM.

---


## 1. Create the Azure Load Balancer

1. Search for **Load balancers** in the top search bar and select it.
2. Click **+ Create**.
3. Under the **Basics** tab, fill in the following:
   * **Resource Group**: Choose the same resource group as your VM.
   * **Name**: `xfusion-lb`
   * **Region**: Choose the same region as your VM.
   * **SKU**: **Standard**
   * **Type**: **Public**
4. Click **Next: Frontend IP configuration** at the bottom.

---

## 2. Configure Frontend and Backend Pool

1. On the **Frontend IP configuration** tab, click **+ Add a frontend IP**.
   * **Name**: `xfusion-lb-ip`
   * **IP type**: **IP address**
   * **Public IP address**: Select the `xfusion-lb-ip` created in Step 1.
   * Click **Add**.
2. Click **Next: Backend pools** at the bottom, then click **+ Add a backend pool**.
   * **Name**: `xfusion-backend-pool`
   * **Virtual network**: Select the VNet where your Nginx VM lives.
   * **Backend Pool Configuration**: Select **NIC** or **IP address**.
   * Under **Virtual machines**, click **+ Add**. 
   * Check the box next to your **Nginx VM** and click **Add**.
   * Click **Save**.
3. Click **Next: Inbound rules** at the bottom.

---

## 3. Create Health Probe and Load Balancer Rule

1. Scroll down to the **Health probes** section and click **+ Add a health probe**.
   * **Name**: `xfusion-health-probe`
   * **Protocol**: **TCP**
   * **Port**: `80`
   * **Interval**: `5` seconds (default)
   * Click **Save**.
2. Scroll up to the **Load balancing rules** section and click **+ Add a load balancing rule**.
   * **Name**: `xfusion-lb-rule`
   * **Frontend IP address**: Select `xfusion-lb-ip`.
   * **Backend pool**: Select `xfusion-backend-pool`.
   * **Protocol**: **TCP**
   * **Port**: `80`
   * **Backend port**: `80`
   * **Health probe**: Select `xfusion-health-probe`.
   * Click **Save**.
3. Click **Review + create**, then click **Create**.

---

## 4. Update Network Security Group (NSG) Rules

1. Search for **Network security groups** in the top search bar and click it.
2. Select the **existing NSG** linked to your Nginx VM or its subnet.
3. On the left menu under **Settings**, click **Inbound security rules**.
4. Click **+ Add** and fill in these details:
   * **Source**: **Any**
   * **Source port ranges**: `*`
   * **Destination**: **Any**
   * **Service**: **HTTP** (This automatically sets Port to `80` and Protocol to **TCP**).
   * **Action**: **Allow**
   * **Priority**: `100` (Or any value lower than your deny rules).
   * **Name**: `Allow-HTTP-80`
5. Click **Add**.
