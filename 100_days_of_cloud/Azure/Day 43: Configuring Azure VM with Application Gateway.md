# Azure Resource Cleanup: Blob Migration and Deletion

The Nautilus DevOps team is currently engaged in a cleanup process, focusing on removing unnecessary data and services from their Azure environment. As part of the migration process, several resources were created for one-time use only, necessitating a cleanup effort to optimize their Azure environment.

A private blob container named `xfusion-blob-18151` already exists in the `westus` region under the storage account `xfusionst7764`.

## Objectives
1. Copy the contents of the `xfusion-blob-18151` blob container to the `/opt` directory on the `azure-client` host.
2. Delete the blob container `xfusion-blob-18151` from the storage account.

---

## Step-by-Step Execution Log

### 1. Authenticate with Azure CLI
Log in to your Azure account using the device code authentication flow:
```bash
az login
```
*Output:*
> To sign in, use a web browser to open the page https://login.microsoft.com/device and enter the code **CM73GERJC** to authenticate.

#### Tenant and Subscription Selection
```text
No     Subscription name    Subscription ID                       Tenant
-----  -------------------  ------------------------------------  ----------------
[1] *  Azure Free Labs      f0c3bcdd-5ce2-4fa0-8cf3-41559747512b  azurefreekmlprod
```

### 2. Verify Available Blobs in the Container
List the contents of the container to identify files for migration:
```bash
az storage blob list \
  --account-name xfusionst7764 \
  --container-name xfusion-blob-18151 \
  --auth-mode login \
  --output table
```
*Output:*
```text
Name         Blob Type    Blob Tier    Length    Content Type    Last Modified              Snapshot
-----------  -----------  -----------  --------  --------------  -------------------------  ----------
xfusion.txt  BlockBlob    Hot          33        text/plain      2026-07-03T07:01:38+00:00
```

### 3. Download the Container Contents
Download all blobs from the target container batch to the local `/opt` directory:
```bash
az storage blob download-batch \
  --account-name xfusionst7764 \
  --source xfusion-blob-18151 \
  --destination /opt \
  --auth-mode login
```
*Output:*
```json
1/1: "xfusion.txt"[##############################################Finished[#############################################################]  100.0000%
[
  "xfusion.txt"
]
```

### 4. Verify Local Files
Confirm that the files were successfully downloaded and contents are intact:
```bash
ls /opt
cat /opt/xfusion.txt
```
*Output:*
```text
creds.json  showcreds  xfusion.txt

Welcome to KKE Azure Cloud Labs!
```

### 5. Delete the Remote Blob Container
Remove the container from the Azure storage account to complete the cleanup:
```bash
az storage container delete \
  --account-name xfusionst7764 \
  --name xfusion-blob-18151 \
  --auth-mode login 
```
*Output:*
```json
{
  "deleted": true
}
```
