# Restore Git Stash Task

## Requirements
Look for the stashed changes under the `/usr/src/kodekloudrepos/apps` git repository. Restore the stash with the `stash@{1}` identifier. Commit and push your changes to the origin.

---

## Step-by-Step Instructions

### 1. Navigate and Fix Repository Permissions
Go to the project folder. You must tell Git it is safe to use this folder, or you will get an ownership error.

```bash
cd /usr/src/kodekloudrepos/apps/
git config --global --add safe.directory /usr/src/kodekloudrepos/apps
```

### 2. Inspect the Stash List
Check what changes are stored in the temporary stash stack.

```bash
git stash list
```

Your terminal should show the stack like this:
```text
stash@{0}: WIP on master: 699f678 initial commit
stash@{1}: WIP on master: 699f678 initial commit
```

### 3. Apply the Target Stash
Restore the specific changes from `stash@{1}`. You must use `sudo` here to avoid permission denied errors on the `.git` index.

```bash
sudo git stash apply stash@{1}
```

### 4. Commit and Push the Changes
Save your newly restored files to your local history and send them up to the server.

```bash
sudo git commit -m "apply the stash changes"
sudo git push origin master
```

ref: https://www.w3schools.com/git/git_stash.asp
