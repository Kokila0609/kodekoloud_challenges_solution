# Git Conflict Resolution Guide: Story Blog Repository

This documentation outlines the steps required to resolve a merge conflict and fix typos in the `story-blog` repository for the Nautilus team.

---

## 📋 Requirement Overview

Sarah and Max have been writing stories and pushing them to a shared repository. Max made recent local updates but encountered a push failure due to out-of-sync remote changes. 

### Objectives:
1. **Access Server**: SSH into the storage server as user `max`.
2. **Fix Typos**: Correct the typo in `story-index.txt` where `Mooose` must be changed to `Mouse`.
3. **Ensure 4 Stories**: Verify that `story-index.txt` retains the titles for all 4 stories.
4. **Resolve Conflict**: Pull remote changes, clean Git conflict markers, commit the resolution, and successfully push to the `master` branch.

---

## ❓ Why Do Merge Conflicts Happen?

Merge conflicts occur when two developers modify the same lines in the same file across different versions of a branch, or when one developer modifies a file that another developer deleted. Git cannot automatically determine which version is correct and requests manual intervention.

---

## 🛠️ Step-by-Step Execution Procedure

### 1. Navigate to the Git Repository
Log into the server and access the repository directory to inspect its current status.
```bash
cd /home/max/story-blog
git status
```

### 2. Correct the Typo
Inspect the contents of the file and edit it to fix the spelling of "Mouse".
```bash
cat story-index.txt
vi story-index.txt
```
*Locate the line containing `The Lion and the Mooose` and change it to `The Lion and the Mouse`.*

### 3. Stage and Attempt to Commit Changes
Staging and pushing at this point will trigger a rejection because the remote repository contains updates you lack locally.
```bash
git add .
git commit -m "fixed Mouse spelling"
git push origin master
```

### 4. Pull Remote Changes
Fetch and merge the latest remote commits into your local working branch. This action will explicitly flag the conflict.
```bash
git pull origin master
```
*Output will display:* `CONFLICT (add/add): Merge conflict in story-index.txt`

### 5. Clean Up Conflict Markers
Open the conflicted file to manually resolve the overlapping lines.
```bash
vi story-index.txt
```

Inside the file, look for the following layout structure:
```text
<<<<<<< HEAD
[Your local changes: The Lion and the Mouse]
=======
[The remote changes pushed by Sarah to Gitea]
>>>>>>> cc3199a093...
```

* **`<<<<<<< HEAD`**: Marks the start of your local changes.
* **`=======`**: The dividing line between your work and the online work.
* **`>>>>>>> cc3199a...`**: Marks the end of the online changes.

**The Fix:** Delete the marker lines (`<<<<<<<`, `=======`, `>>>>>>>`) entirely. Combine the text blocks carefully to ensure **all 4 story titles** are present and correctly spelled. Save and close the file.

### 6. Commit and Push the Final Resolution
Inform Git that the conflict is fully resolved, finalize the merge commit, and push the clean history back to the Gitea server.
```bash
git add .
git commit -m "Resolve merge conflict by incorporating both suggestions"
git push origin master
```
