# Git Automation: Server-Side Release Tagging Deployment

This documentation outlines the steps to set up an automated `post-update` release tagging hook on the central repository (`/opt/apps.git`) and execute a feature branch merge from the working directory (`/usr/src/kodekloudrepos/apps`) as the `natasha` user.

## Prerequisites
* **User**: `natasha`
* **Central Bare Repository**: `/opt/apps.git`
* **Cloned Working Directory**: `/usr/src/kodekloudrepos/apps` (or configured project directory)

---

## Step 1: Configure the Server-Side Hook

The `post-update` hook scans all pushed branches. If the `master` branch is updated, it automatically generates a timestamped release tag (`release-YYYY-MM-DD`).

1. Open or create the hook file in the bare repository:
   ```bash
   vi /opt/apps.git/hooks/post-update
   ```

2. Populate the file with the following verified shell script:
   ```bash
   #!/bin/bash
   
   # Generate dynamic date variables
   RELEASE_DATE=$(date +%Y-%m-%d)
   RELEASE_TAG="release-$RELEASE_DATE"

   # Loop through all pushed refs to intercept updates to master
   for ref in "$@"; do
     if [[ "$ref" == "refs/heads/master" ]]; then
         echo "Master branch updated, creating release tag: $RELEASE_TAG"
         
         # Apply the tag directly onto the master branch commit
         git tag "$RELEASE_TAG" master
         echo "Release tag $RELEASE_TAG created successfully"
     fi
   done

   # Prepare repository metadata for transport compatibility
   exec git update-server-info
   ```

3. Grant executable permissions to the hook script without modifying owner privileges:
   ```bash
   chmod +x /opt/apps.git/hooks/post-update
   ```

---

## Step 2: Merge the Feature Branch Locally

Execute the integration workflow inside the active project directory before pushing changes back to the origin server.

1. Navigate into the cloned repository layout:
   ```bash
   cd /usr/src/kodekloudrepos/apps
   ```

2. Checkout the targeting branch:
   ```bash
   git checkout master
   ```

3. Merge the production-ready `feature` updates into the local staging layout:
   ```bash
   git merge feature
   ```

---

## Step 3: Publish Changes & Execute Automation

Push the merged codebase changes to upstream storage. This action automatically triggers the deployed server-side script.

1. Push upstream changes:
   ```bash
   git push origin master
   ```

2. Validate terminal feedback. The remote server execution should produce tracking outputs similar to the following:
   ```text
   remote: Master branch updated, creating release tag: release-2026-06-25
   remote: Release tag release-2026-06-25 created successfully
   ```

---

## Step 4: Verification Checklist

Run these diagnostics locally to confirm systemic success:

1. Fetch fresh tag objects from upstream:
   ```bash
   git fetch --tags
   ```

2. Display available release identifiers:
   ```bash
   git tag -l
   ```
   *Expected Output:* `release-2026-06-25` (reflecting the exact system execution date).

3. Inspect commit linkage details:
   ```bash
   git show release-2026-06-25
   ```
   *Expected Output:* Verification details linking your daily runtime signature to the latest target merge hash.
