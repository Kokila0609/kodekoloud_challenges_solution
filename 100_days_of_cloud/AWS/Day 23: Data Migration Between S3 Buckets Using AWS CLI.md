# AWS S3 Data Migration Guide

Follow these steps to create a new private S3 bucket and migrate data between buckets.

### 1. Create a New Private S3 Bucket
Create the new bucket named `datacenter-sync-20643` in the `us-east-1` region:
```bash
aws s3api create-bucket --bucket datacenter-sync-20643 --region us-east-1
```

### 2. Verify Bucket Creation
Confirm that your new bucket is visible in your account:
```bash
aws s3api list-buckets
```

### 3. Migrate the Data
Copy all files and folders from the old bucket to the new bucket:
```bash
aws s3 cp s3://datacenter-s3-5377/ s3://datacenter-sync-20643 --recursive
```

### 4. Ensure Data Consistency
List the contents of both buckets to verify that the data matches exactly:
```bash
aws s3 ls s3://datacenter-sync-20643
aws s3 ls s3://datacenter-s3-5377
```
