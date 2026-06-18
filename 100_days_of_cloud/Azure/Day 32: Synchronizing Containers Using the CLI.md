# Azure Blob Storage Data Migration Documentation

## 📌 Requirement

As part of a data migration project, the team lead has tasked the team with migrating data from an existing Azure Blob container to a new Blob container. The existing container contains a substantial amount of data that must be accurately transferred to the new container. The team is responsible for creating the new Blob container and ensuring that all data from the existing container is copied or synced to the new container completely and accurately. It is imperative to perform thorough verification steps to confirm that all data has been successfully transferred to the new container without any loss or corruption.

As a member of the Nautilus DevOps Team, your task is to perform the following:
1. **Create a New Private Azure Blob Container**: Name the container `datacenter-dest-4890` under the storage account `datacenterst10479`.
2. **Data Migration**: Migrate the file `datacenter.txt` from the existing `datacenter-source-21945` container to the new `datacenter-dest-4890` container.
3. **Ensure Data Consistency**: Ensure that both containers have the file `datacenter.txt` and confirm the file content is identical in both containers.

---

## 🛠️ Step 1: Verify the Existing Source Container

Run the following command to check the status and metadata of the existing source container:

```bash
az storage container show --name datacenter-source-21945 --account-name datacenterst10479 --auth-mode login
```

### Output:
```json
{
  "deleted": null,
  "encryptionScope": {
    "defaultEncryptionScope": "\$account-encryption-key",
    "preventEncryptionScopeOverride": false
  },
  "immutableStorageWithVersioningEnabled": false,
  "metadata": {},
  "name": "datacenter-source-21945",
  "properties": {
    "etag": "\"0x8DECD05C50F25C1\"",
    "hasImmutabilityPolicy": false,
    "hasLegalHold": false,
    "lastModified": "2026-06-18T06:49:39+00:00",
    "lease": {
      "duration": null,
      "state": "available",
      "status": "unlocked"
    },
    "publicAccess": null
  },
  "version": null
}
```

---

## 🛠️ Step 2: Create the Destination Container

Run this command to create the new target private container under the same storage account:

```bash
az storage container create --name datacenter-dest-4890 --account-name datacenterst10479 --auth-mode login
```

### Output:
```json
{
  "created": true
}
```

---

## 🛠️ Step 3: Migrate the File

Use the blob copy command to copy `datacenter.txt` directly from the source container to the new destination container:

```bash
az storage blob copy start \
  --source-container datacenter-source-21945 \
  --source-blob datacenter.txt \
  --destination-container datacenter-dest-4890 \
  --destination-blob datacenter.txt \
  --account-name datacenterst10479
```

### Output:
```json
{
  "client_request_id": "02e8ae58-6ae7-11f1-a9b0-863aab4391e2",
  "copy_id": "bcbe6fc4-365c-44ca-89e3-61a64608ea9d",
  "copy_status": "success",
  "date": "2026-06-18T07:26:24+00:00",
  "etag": "\"0x8DECD0AE775FB73\"",
  "last_modified": "2026-06-18T07:26:24+00:00",
  "request_id": "5a376410-701e-0030-65f3-fe5f9a000000",
  "version": "2022-11-02",
  "version_id": null
}
```

---

## 🛠️ Step 4: Verification & Data Consistency

### 1. Verify File Presence in Both Containers
List the files in both containers to verify the file exists in both locations.

**List source container files:**
```bash
az storage blob list \
  --account-name datacenterst10479 \
  --container-name datacenter-source-21945 \
  --output table
```

**List destination container files:**
```bash
az storage blob list \
  --account-name datacenterst10479 \
  --container-name datacenter-dest-4890 \
  --output table
```

### 2. Verify File Content Identity
Download the file from both containers to your local environment to perform a local consistency check.

**Download from source:**
```bash
az storage blob download \
  --account-name datacenterst10479 \
  --container-name datacenter-source-21945 \
  --name datacenter.txt \
  --file ./source-datacenter.txt
```

**Download from destination:**
```bash
az storage blob download \
  --account-name datacenterst10479 \
  --container-name datacenter-dest-4890 \
  --name datacenter.txt \
  --file ./dest-datacenter.txt
```

> 💡 **Tip:** Once downloaded, you can run `diff ./source-datacenter.txt ./dest-datacenter.txt` or use `md5sum` to guarantee the files are 100% identical.
