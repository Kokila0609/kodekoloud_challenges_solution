# Configuring SeaweedFS S3 Remote Storage for DVC

This guide outlines the steps to correct the DVC remote configuration and push tracked data into a local SeaweedFS S3-compatible bucket.

## Environment & Requirements

- **Project Path:** `/root/code/fraud-detection/`
- **S3 Endpoint:** `http://localhost:8333`
- **Filer UI:** `http://localhost:8888` (Buckets visible under `/buckets/`)
- **Credentials:** `weedadmin` / `weedadmin123` (Pre-configured)
- **Bucket Name:** `dvc-storage`

---

## Step-by-Step Resolution

### 1. Check Existing Configuration
Verify the current remote settings to identify why `dvc push` is failing.
```bash
dvc remote list
```
*Output:*
```text
s3      s3://dvc-wrong-bucket
```

### 2. Correct the Remote URL
Update the remote named `s3` to point to the correct SeaweedFS bucket (`dvc-storage`).
```bash
dvc remote modify s3 url s3://dvc-storage
```

### 3. Set the Custom S3 Endpoint URL
Configure DVC to route traffic to the local SeaweedFS API instead of official AWS servers.
```bash
dvc remote modify s3 endpointurl http://localhost:8333
```

### 4. Set as Default Remote
Mark the corrected `s3` remote as the default storage target for the project.
```bash
dvc config core.remote s3
```

### 5. Verify the Final `.dvc/config` File
Ensure all modifications are accurately written to the configuration file.
```bash
cat .dvc/config
```
*Expected Output:*
```ini
[core]
    remote = s3
['remote "s3"']
    url = s3://dvc-storage
    endpointurl = http://localhost:8333
    access_key_id = weedadmin
    secret_access_key = weedadmin123
```

### 6. Push the Tracked Data
Sync your tracked data artifacts (`data/raw/transactions.csv`) to SeaweedFS.
```bash
dvc push
```
*Output:*
```text
Collecting           |1.00 [00:00,  785entry/s]
Pushing
1 file pushed
```

---

## Verification
Open the **SeaweedFS Filer UI** (`http://localhost:8888/buckets/dvc-storage`). You will now see your data objects successfully stored under the `files/md5/...` prefix.
