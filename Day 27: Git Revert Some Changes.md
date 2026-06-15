# Git Repository Revert Guide

Follow these steps to revert the latest commit in your repository and save the changes with a custom message.

### 1. Move to the Repository
Change your directory to the correct project folder:
```bash
cd /usr/src/kodekloudrepos/media/
```

### 2. Check the Git History
Look at the past commits to see your current position:
```bash
git log
```

### 3. Revert the Latest Commit
Undo the work from the last commit (`HEAD`):
```bash
sudo git revert HEAD
```

### 4. Check the Reverted Commit
Verify that the history shows the revert action:
```bash
git log
```

### 5. Stage the Changes
Prepare the files for your new commit:
```bash
sudo git add .
```

### 6. Add the Commit Message
Save the changes with your custom lowercase note:
```bash
sudo git commit -m "revert media"
```

### 7. Verify the Final Results
Inspect the exact file changes and check the final log list:
```bash
git show
git log
```
