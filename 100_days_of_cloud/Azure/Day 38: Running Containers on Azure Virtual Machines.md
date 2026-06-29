

## Objective
The Nautilus DevOps team requires an Azure Virtual Machine (VM) setup to securely interact with an Azure Blob Storage container for storing and retrieving operational data.

## Task Details

### 1. Environment & Infrastructure Setup
* **Virtual Machine:** `datacenter-vm` (Pre-existing)
* **Region:** East US
* **Storage Account Name:** `datacenterstor22446`
* **Replication Strategy:** Locally-redundant storage (LRS)
* **Blob Container Name:** `datacenter-container22446` (Private)

### 2. Retrieved Storage Credentials
* **Account Key:**  


---

## Step-by-Step Execution

### Step 1: Provision Storage Infrastructure
1. Create the private storage account named `datacenterstor22446` in the `East US` region using LRS.
2. Inside the storage account, create a private Blob container named `datacenter-container22446`.

### Step 2: Create a Local Test File
SSH into your pre-configured virtual machine (`datacenter-vm`) and execute the following commands to create your sample payload:

```bash
# Navigate to the home directory
cd /home/azureuser

# Create the test file with the specified content
echo "this is a test file" > testfile.txt
```

### Step 3: Upload the Payload to Azure Blob Storage
Run the Azure CLI command from inside the VM terminal session to securely upload `testfile.txt` using the retrieved access key:

```bash
az storage blob upload \
  --account-name datacenterstor22446 \
  --account-key "account-key" \
  --container-name datacenter-container22446 \
  --name testfile.txt \
  --file /home/azureuser/testfile.txt
```

---

## Verification
To confirm that the object was successfully stored in the private container, you can list the blobs:

```bash
az storage blob list \
  --account-name datacenterstor22446 \
  --account-key "account-key" \
  --container-name datacenter-container22446 \
  --output table
```
