# Deploying a Static Website on Azure Storage Account

## Task Requirements
The Nautilus DevOps team has been tasked with creating an internal information portal for public access. As part of this project, they need to host a static website on Azure using an Azure Storage account. The Storage account must be configured for public access to allow external users to access the static website directly via the Azure Storage URL.

1. Create an Azure Storage account named `datacenterwebst14268` in an existing resource group.
2. Configure the Storage account for static website hosting with `index.html` as the index document.
3. Allow public access to the static website so that the website is publicly accessible.
4. Upload the `index.html` file from the `/root/` directory of the Azure client host to the Storage account's `$web` container.
5. Verify that the website is accessible directly through the Azure Storage static website URL.

---

## Step-by-Step Implementation

### 1. Create a Storage Account
Navigate to **Storage accounts** in the Azure Portal and click **Create**. Fill out the required parameters:
* **Subscription:** Azure Pass-Sponsor
* **Resource group:** devops_nautilus_datacenter
* **Storage account name:** `datacenterwebst14268`
* **Region:** East US
* **Primary service:** Azure Blob Storage or Azure Data Lake Storage
* **Performance:** Standard
* **Replication:** Locally-redundant storage (LRS)

Click **Review + create**, then click **Create**.

### 2. Enable Static Website Hosting
Once deployment is complete, navigate to the storage account dashboard:
1. Under **Data management** on the left menu, select **Static website**.
2. Set the toggle to **Enabled**.
3. Set the **Index document name** to `index.html`.
4. Click **Save**.

### 3. Deploy the File via Azure CLI
Log into your Azure client host and upload the `index.html` file located in the `/root/` directory to the newly created `$web` destination container.

```bash
az storage blob upload -f index.html -c '\$web' --account-name datacenterwebst14268
```

### 4. Fetch the Endpoint URL
Return to the **Static website** settings page under **Data management** to find your unique **Primary endpoint** web address.

* **Primary endpoint:** `https://windows.net`

### 5. Verify the Static Website
Open a web browser and paste your primary endpoint URL to confirm public accessibility. The website should render correctly.

> **Output:** Welcome to KKE labs!
