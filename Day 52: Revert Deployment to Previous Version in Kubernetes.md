# Kubernetes Deployment Rollback Guide

## Requirement
Earlier today, the Nautilus DevOps team deployed a new release for an application. However, a customer reported a bug in this release. Revert the deployment named `nginx-deployment` to the previous revision and ensure all pods are healthy after the rollback.

---

## Step-by-Step Execution

### 1. Verify Current Deployment Status
Check the existing state and configuration of the deployment before modifying anything.

```bash
thor@jump-host ~\$ k get deployments.apps 
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   3/3     3            3           5m44s
```

### 2. Check the Rollout History
Inspect the available revisions to determine the previous stable state and find out what changed.

```bash
thor@jump-host ~\$ k get rollout history deployment/nginx-deployment

thor@jump-host ~\$ k rollout history deployment/nginx-deployment
deployment.apps/nginx-deployment 
REVISION  CHANGE-CAUSE
1         <none>
2         kubectl set image deployment nginx-deployment nginx-container=nginx:alpine --record=true
```

### 3. Perform the Rollback
Execute the rollback command to undo the faulty deployment and revert to the previous revision.

```bash
thor@jump-host ~\$ kubectl rollout undo deployment/nginx-deployment
deployment.apps/nginx-deployment rolled back
```

### 4. Verify Rollback and Pod Health
Confirm that the deployment updated successfully and verify that all individual pods are running and healthy.

```bash
thor@jump-host ~\$ k get deployments.apps 
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   3/3     3            3           8m49s

thor@jump-host ~\$ k get po
NAME                               READY   STATUS    RESTARTS   AGE
nginx-deployment-fc677cbc9-gdq2t   1/1     Running   0          13s
nginx-deployment-fc677cbc9-h5npg   1/1     Running   0          14s
nginx-deployment-fc677cbc9-mwfwq   1/1     Running   0          15s
```

### 5. Confirm Final Rollout History
Verify that the revision history reflects the changes after running the rollback command.

```bash
thor@jump-host ~\$ k rollout history deployment/nginx-deployment
deployment.apps/nginx-deployment 
REVISION  CHANGE-CAUSE
3         <none>
4         kubectl set image deployment nginx-deployment nginx-container=nginx:alpine --record=true
```
