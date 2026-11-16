# Requirements

Some of the Nautilus team developers are developing a static website and they want to deploy it on Kubernetes cluster. They want it to be highly available and scalable. Therefore, based on the requirements, the DevOps team has decided to create a deployment for it with multiple replicas. Below you can find more details about it:

Create a deployment using nginx image with latest tag only and remember to mention the tag i.e nginx:latest. Name it as nginx-deployment. The container should be named as nginx-container, also make sure replica counts are 3.
Create a NodePort type service named nginx-service. The nodePort should be 30011.
Note: The kubectl utility on the jump-host has been configured to work with the Kubernetes cluster.


# Kubernetes Nginx Deployment & NodePort Service

This guide covers deploying a 3-replica **Nginx Deployment** (`nginx-deployment`) and exposing it externally using a **NodePort Service** (`nginx-service`) on port `30011`.

---

## 1. Kubernetes Manifest (`nginx-deployment.yaml`)

Save the following configuration to a file named `nginx-deployment.yaml`:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: NodePort
  selector:
    app: nginx
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30011

---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx-container
          image: nginx:latest
          ports:
            - name: nginx-service
              containerPort: 80
```

> **Important Corrections Made for Valid Deployment:**
> * **`apiVersion: v1` added** to the `Service` block (required by Kubernetes).
> * **Selector aligned:** Changed the Service selector from `app.kubernetes.io/name: nginx` to `app: nginx` so traffic routes properly to the Deployment pods.
> * **`containerPort: 80` added:** Required alongside `name: nginx-service` under `containers[].ports`.

---

## 2. Deployment & Verification Steps

### Step 1: Apply the Manifest

Create the Service and Deployment in your cluster:

```bash
kubectl apply -f nginx-deployment.yaml
```

### Step 2: Check Running Pods

Verify that all 3 replicas of the Nginx Pod are up and running:

```bash
kubectl get po
```

**Expected Output:**
```text
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-xxxxxxxxxx-xxxxx   1/1     Running   0          30s
nginx-deployment-xxxxxxxxxx-xxxxx   1/1     Running   0          30s
nginx-deployment-xxxxxxxxxx-xxxxx   1/1     Running   0          30s
```

### Step 3: Check Deployment Status

Confirm that the Deployment shows `3/3` ready replicas:

```bash
kubectl get deployment
```

**Expected Output:**
```text
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   3/3     3            3           35s
```

### Step 4: Verify Service Exposure

Check that `nginx-service` is listening on NodePort `30011`:

```bash
kubectl get svc nginx-service
```
