# Restore Git rebase Task

## Requirements
The Nautilus application development team has been working on a project repository /opt/media.git. This repo is cloned at /usr/src/kodekloudrepos on storage server in Stratos DC. They recently shared the following requirements with DevOps team:

One of the developers is working on feature branch and their work is still in progress, however there are some changes which have been pushed into the master branch, the developer now wants to rebase the feature branch with the master branch without loosing any data from the feature branch, also they don't want to add any merge commit by simply merging the master branch into the feature branch. Accomplish this task as per requirements mentioned.


## Step-by-Step Instructions

### 1. Set Safe Directory and Check StatusNavigate to your repository and ensure Git trusts the directory. Then, check the current status.

```bash
git config --global --add safe.directory /usr/src/kodekloudrepos/media
git status
git log
```

### 2.Fetch Latest Remote Changes and Verify Master

Switch to the master branch, pull the latest updates from the remote repository, and check the commit log.

```bash
sudo git checkout master
sudo git fetch origin
git log
```


### 3. Switch to the Feature Branch

Switch to your working feature branch and verify your current active branch.

```bash
sudo git checkout feature
git branch
```

### 4.Rebase the Feature Branch onto Master

Reapply your local feature commits on top of the latest commits from the master branch. This keeps the history linear without creating a merge commit.

```bash
sudo git rebase master
git log
```

### 5. Force Push Updates to Remote

Because rebasing rewrites the commit history, a standard git push will be rejected by the remote repository.Standard Push (Will Fail):

```bash
git push origin feature

To /opt/media.git
 ! [rejected]        feature -> feature (non-fast-forward)
error: failed to push some refs to '/opt/media.git'

```
### 6. Safe Force Push (Solution):

Use the --force-with-lease flag to safely update the remote branch. This ensures you do not overwrite anyone else's work on the remote server.

```bash
sudo git push origin feature --force-with-lease
To /opt/media.git
 + 5776d08...906edb7 feature -> feature (forced update)
```


ref: (https://www.geeksforgeeks.org/git/rebasing-of-branches-in-git/)
