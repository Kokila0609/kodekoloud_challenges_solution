# DVC Troubleshooting Guide: SeaweedFS Remote Configuration

Follow these steps to diagnose and fix the DVC authentication failure, configure the SeaweedFS S3 remote, and pull the missing dataset.

## Step 1: Navigate to the Project Root
The repository must be executed inside the correct project directory where DVC is initialized.

```bash
root@controlplane ~/code ✖ dvc pull
ERROR: you are not inside of a DVC repository (checked up to mount point '/')

root@controlplane ~/code ✖ cd fraud-detection/
```

## Step 2: Diagnose the Connection Failure
Attempting to pull the data initially fails due to missing S3 credentials required by SeaweedFS.

```bash
root@controlplane fraud-detection on  main ➜  dvc pull
Collecting           |1.00 [00:00,  735entry/s]
ERROR: failed to connect to s3 (dvc-storage/files/md5) - Unable to locate credentials
Fetching
ERROR: failed to pull data from the cloud - 1 files failed to download
```

## Step 3: Check Current Remote Configuration
Inspect the default configured remote name and endpoint settings.

```bash
root@controlplane fraud-detection on  main ✖ dvc remote list
s3      s3://dvc-storage        (default)
```

## Step 4: Configure the S3 Endpoint and Credentials
Modify the DVC configuration to use the local SeaweedFS S3 endpoint and set the required authentication keys.

```bash
# Set the custom SeaweedFS S3 endpoint URL
root@controlplane fraud-detection on  main ➜  dvc remote modify s3 endpointurl http://localhost:8333

# Explicitly set the core default remote
root@controlplane fraud-detection on  main [!] ➜  dvc config core.remote s3

# Set the access key ID
root@controlplane fraud-detection on  main [!] ➜  dvc remote modify s3 access_key_id weedadmin

# Set the secret access key
root@controlplane fraud-detection on  main [!] ➜  dvc remote modify s3 secret_access_key weedadmin123
```

## Step 5: Verify the DVC Configuration File
Verify that `.dvc/config` matches the correct endpoint, bucket URL, and credentials.

```bash
root@controlplane fraud-detection on  main [!] ✖ cat .dvc/config
[core]
    remote = s3
['remote "s3"']
    url = s3://dvc-storage
    endpointurl = http://localhost:8333
    access_key_id = weedadmin
    secret_access_key = weedadmin123
```

## Step 6: Pull the Dataset
Run the pull command again to fetch `data/raw/transactions.csv` and place it onto your disk storage.

```bash
root@controlplane fraud-detection on  main [!] ➜  dvc pull
Collecting           |0.00 [00:00,    ?entry/s]
Fetching
Building workspace index |2.00 [00:00,  661entr
Comparing indexes   |4.00 [00:00, 2.86kentry/s]
Applying changes     |1.00 [00:00, 1.01kfile/s]
A       data/raw/transactions.csv
1 file fetched and 1 file added
```

## links:
https://medium.com/@sthanikamsanthosh1994/version-controlling-of-ml-model-and-data-using-dvc-ee60d9f15cec

https://doc.dvc.org/command-reference/pull

