# Git Remote Update and Code Push Guide

Follow these steps to remove an existing remote repository, connect to a new remote target, commit your local changes, and push the updates.

### Step 1: Navigate to the Project Directory
Change your current working directory to the local Git repository.
```bash
cd /usr/src/kodekloudrepos/demo/
```

### Step 2: Check Existing Remotes
Verify the current remote connections linked to this repository.
```bash
git remote -v
```

### Step 3: Remove the Old Remote
Remove the old remote named `origin`. *Note: If a regular command fails due to permission restrictions, prepend it with `sudo`.*
```bash
sudo git remote remove origin
```

### Step 4: Verify the Removal
Confirm that the old `origin` remote was successfully deleted. The list should now be blank.
```bash
git remote -v
```

### Step 5: Add the New Remote Destination
Link your local repository to the new remote path (`/opt/xfusioncorp_demo.git`) and name it `dev_demo`.
```bash
sudo git remote add dev_demo /opt/xfusioncorp_demo.git
```

### Step 6: Verify the New Remote
Ensure that `dev_demo` is successfully mapped to the correct paths.
```bash
git remote -v
```

### Step 7: Stage and Commit Changes
Stage all your new files and commit them to your local history with a clear message.
```bash
git add .
git commit -m "added html files"
```

### Step 8: Push to the New Remote
Push your local `master` branch up to the newly configured `dev_demo` remote.
```bash
sudo git push dev_demo master
```
