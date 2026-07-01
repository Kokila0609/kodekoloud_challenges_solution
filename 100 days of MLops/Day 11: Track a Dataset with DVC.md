# DVC Initialization Guide

The **xFusionCorp Industries ML team** is implementing DVC to ensure that datasets and model files are versioned independently from the codebase. Initialize DVC within the existing Git repository located at `/root/code/fraud-detection/` and record this initialization in Git.

## Requirements

1. **Existing Setup**: A Git repository already exists at `/root/code/fraud-detection/` with an initial commit.
2. **Initialization**: Initialize DVC inside that repository so that the standard `.dvc/` control directory and `.dvcignore` file are created alongside the existing Git working tree.
3. **Commit Changes**: Stage every file that DVC produces during initialization, and record them in a new Git commit with the message `Initialize DVC`.
4. **Verification**: Once initialization is complete, the DVC extension will detect the new `.dvc/` directory and surface the **DVC TRACKED** section in the **EXPLORER** panel together with a **DVC indicator** in the bottom status bar.

---

## Terminal Execution Walkthrough

```bash
root@controlplane ~/code ✖ pwd
/root/code

root@controlplane ~/code ➜  cd fraud-detection/
 
root@controlplane fraud-detection on  master ➜  dvc init
Initialized DVC repository.

You can now commit the changes to git.

+---------------------------------------------------------------------+

|                                                                     |
|        DVC has enabled anonymous aggregate usage analytics.         |
|     Read the analytics documentation (and how to opt-out) here:     |
|             <https://dvc.org/doc/user-guide/analytics>              |
|                                                                     |
+---------------------------------------------------------------------+

What's next?
------------
- Check out the documentation: <https://dvc.org/doc>
- Get help and share ideas: <https://dvc.org/chat>
- Star us on GitHub: <https://github.com/treeverse/dvc>

root@controlplane fraud-detection on  master [+] ➜  git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        new file:   .dvc/.gitignore
        new file:   .dvc/config
        new file:   .dvcignore


root@controlplane fraud-detection on  master [+] ➜  git add .dvc .dvcignore 

root@controlplane fraud-detection on  master [+] ➜  git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        new file:   .dvc/.gitignore
        new file:   .dvc/config
        new file:   .dvcignore


root@controlplane fraud-detection on  master [+] ➜  git commit -m "Initialize DVC"
[master ba37c5c] Initialize DVC
 3 files changed, 6 insertions(+)
 create mode 100644 .dvc/.gitignore
 create mode 100644 .dvc/config
 create mode 100644 .dvcignore

root@controlplane fraud-detection on  master ➜  git status
On branch master
nothing to commit, working tree clean

root@controlplane fraud-detection on  master ➜  
```
