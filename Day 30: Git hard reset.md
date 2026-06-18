# Git Reset and Force Push Documentation

## 📌 Requirement

In the `/usr/src/kodekloudrepos/blog` git repository, reset the git commit history so that there are only two commits left in the history:
1. `initial commit`
2. `add data.txt file`

Also, make sure to push your changes to the remote repository.

---

## 🛠️ Steps

### 1. Navigate to the Git Repository
Log into the server and move into the project directory:
```bash
cd /usr/src/kodekloudrepos/blog
```

### 2. Verify Current Git Status and Log History
Check your current setup and find the specific commit ID number you need to roll back to:
```bash
git status
git branch
git log
```

### 3. Reset the Commit History
Use the commit ID you found in the log to wipe out all newer saves. This rolls the project back to that exact point in time:
```bash
sudo git reset --hard 985c2874358803855c66f3a0f5ab23e11c5ec795
```

### 4. Force Push Changes to Origin
Because you deleted newer commits, your local computer is now behind the online backup. You must force the remote repository to accept your changes:
```bash
sudo git push origin master -f
```
*(Note: You can also use `sudo git push origin master --force`)*
