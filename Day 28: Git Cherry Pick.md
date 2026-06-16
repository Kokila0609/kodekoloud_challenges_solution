# Git Cherry-Pick Guide

Follow these steps to cherry-pick a specific commit from the `feature` branch into the `master` branch and push the updates.

### 1. Move to the Repository
Change your directory to the project folder:
```bash
cd /usr/src/kodekloudrepos/cluster/
```

### 2. Configure Safe Directory and Check Status
Mark the directory as safe for Git and check the current status of your repository:
```bash
git config --global --add safe.directory /usr/src/kodekloudrepos/cluster
git status
```

### 3. Check Branches and Find the Commit Hash
List the branches to find your targets. (Locate the commit hash for the "Update info.txt" message, which is `cddbdee19bf3144734693cb077fa72b050f38d4e`):
```bash
git branch
```

### 4. Switch to the Master Branch
Move to the `master` branch where you want to apply the specific change:
```bash
sudo git switch master
```

### 5. Cherry-Pick the Commit
Apply the "Update info.txt" commit to the `master` branch using its hash ID:
```bash
sudo git cherry-pick cddbdee19bf3144734693cb077fa72b050f38d4e
```

### 6. Stage and Push the Changes
Verify the status, stage any modifications, and push the updated `master` branch to the online backup:
```bash
git status
sudo git add .
sudo git push origin master
```

### 7. Verify the History
Confirm that the commit was successfully added to your branch history:
```bash
git log
```
