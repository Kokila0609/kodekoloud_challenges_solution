# Git Repository Update Steps

### 1. Navigate to the Repository
cd /usr/src/kodekloudrepos/cluster

### 2. Check Out Master
git checkout master

### 3. Create New Branch
git checkout -b xfusion

### 4. Copy the File
cp /tmp/index.html .

### 5. Add and Commit
git add index.html
git commit -m "Add index.html to xfusion branch"

### 6. Push New Branch
git push origin xfusion

### 7. Return to Master
git checkout master

### 8. Merge the Branch
git merge xfusion

### 9. Push Master Branch
git push origin master
