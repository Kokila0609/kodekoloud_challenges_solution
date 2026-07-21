# Requirements

An application currently running on the Kubernetes cluster employs the nginx web server. The Nautilus application development team has introduced some recent changes that need deployment. They've crafted an image nginx:1.18 with the latest updates.


Execute a rolling update for this application, integrating the nginx:1.18 image. The deployment is named nginx-deployment.

Ensure all pods are operational post-update.

# Kubernetes Application Rolling Update Documentation

Follow this guide to execute a rolling update for the `nginx-deployment` to deploy the updated `nginx:1.18` container image and verify its operational status.

### Step 1: Verify Current Deployment Status
Before updating, check the existing deployment and its active pods to confirm everything is running healthy.

```bash
# Check the deployment status
k get deployments.apps 

# Check the running pods
k get po

# Verify the container name and current labels
k describe pods nginx-deployment-fc677cbc9-dn7m6
```

### Step 2: Execute the Rolling Update
Update the container image of the deployment to `nginx:1.18`. Replace `nginx-container` with the exact container name defined in your deployment template.

```bash
k set image deployments/nginx-deployment nginx-container=nginx:1.18
```

### Step 3: Track Rollout Progress
Monitor the status of the rolling update to ensure the new replicas spin up successfully and the old ones terminate safely.

```bash
# Watch the pod transition states
k get po

# Wait for the rollout to complete successfully
k rollout status deployment/nginx-deployment 
```

### Step 4: Service Discovery and Traffic Verification
Locate the `NodePort` mapping allocated for the application service, and execute a `curl` request against the designated port to confirm the update is serving traffic.

```bash
# Identify the exposed NodePort
k get svc

# Test application access locally via the assigned NodePort
curl http://localhost:30008
```
