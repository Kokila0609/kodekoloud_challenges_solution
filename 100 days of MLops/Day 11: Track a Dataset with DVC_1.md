# Migrating Datasets from Git to DVC Tracking

A teammate accidentally committed the transactions dataset directly to Git instead of using Data Version Control (DVC). To comply with xFusionCorp Industries engineering standards, all datasets inside the `data/` directory must be managed exclusively by DVC.

This guide details the procedure for transferring ownership of `data/raw/transactions.csv` from Git to DVC without losing local data.

---

## Prerequisites
* **Project Directory**: `/root/code/fraud-detection/`
* **DVC Status**: Initialised
* **Current State**: `data/raw/transactions.csv` is actively tracked by Git

---

## Step-by-Step Migration Guide

### Step 1: Navigate to the Project Root
Switch your active terminal working directory to the project repository.
```bash
cd /root/code/fraud-detection
```

Verify the files currently indexed by Git:
```bash
git ls-files
```
*Expected Output:*
```text
.dvc/.gitignore
.dvc/config
.dvcignore
README.md
data/raw/transactions.csv
```

### Step 2: Stop Git from Tracking the Dataset
Untrack the dataset from Git's index while maintaining the raw file safely on your hard disk.
```bash
git rm --cached data/raw/transactions.csv
```

Confirm that the dataset is removed from the Git index but remains intact in the workspace:
```bash
# Check Git files
git ls-files

# Verify file still exists on disk
ls -l data/raw/transactions.csv
```

### Step 3: Track the Dataset with DVC
Initialize tracking on the raw data file using DVC. This creates a lightweight `.dvc` pointer tracking file and configures git exclusion rules.
```bash
dvc add data/raw/transactions.csv
```
*Expected Output:*
```text
100% Adding...|███████|1/1 [00:00, 39.39file/s]
                                               
To track the changes with git, run:

        git add data/raw/transactions.csv.dvc data/raw/.gitignore
```

### Step 4: Verify Workspace Status
Check the status of your Git repository to ensure changes are correctly reflected.
```bash
git status
```
*Expected Output:*
```text
On branch main
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        deleted:    data/raw/transactions.csv

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        data/
```

### Step 5: Stage and Commit the Pointer Configurations
Stage the new `.dvc` pointer, the generated `.gitignore` file, and record the deletion of the raw data from Git.
```bash
# Stage the tracking pointer
git add data/raw/transactions.csv.dvc

# Stage the local ignore file
git add data/raw/.gitignore

# Stage all updated file deletions (the untracked csv)
git add -u
```

Review the final staging environment before recording the commit:
```bash
git status
```
*Expected Output:*
```text
On branch main
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        new file:   data/raw/.gitignore
        deleted:    data/raw/transactions.csv
        new file:   data/raw/transactions.csv.dvc
```

Commit the configurations with the mandated team message:
```bash
git commit -m "Track transactions dataset with DVC"
```

---

## Verification

### 1. Verify Git Commit History
Ensure that your commit has been properly registered on the current branch.
```bash
git log --oneline -n 1
```
*Expected Output:*
```text
6431249 (HEAD -> main) Track transactions dataset with DVC
```

### 2. Verify File Indexing
Verify that Git is tracking only the `.dvc` file and not the raw data:
```bash
git ls-files | grep transactions.csv
```
*Expected Output:*
```text
data/raw/transactions.csv.dvc
```

### 3. IDE Verification
Open your IDE workspace. The **DVC TRACKED** section located in the **EXPLORER** side panel will now officially list `transactions.csv`, confirming the repository extension identifies it as a DVC-managed file.
