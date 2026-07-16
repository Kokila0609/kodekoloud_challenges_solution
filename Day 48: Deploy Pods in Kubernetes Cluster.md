# Requirements:

The Nautilus DevOps team is diving into Kubernetes for application management. One team member has a task to create a pod according to the details below:


Create a pod named pod-nginx using the nginx image with the latest tag. Ensure to specify the tag as nginx:latest.

Set the app label to nginx_app, and name the container as nginx-container.

Note: The kubectl utility on the jump-host has been configured to work with the Kubernetes cluster.

# Kubernetes Pod Deployment Documentation

The following steps demonstrate how to define a multi-labeled Kubernetes pod using a YAML manifest and verify its deployment in the cluster.

## 1. Create the Manifest File

View the contents of the `nginx.yaml` file defining the pod configuration:

```bash
thor@jump-host ~\$ cat nginx.yaml
apiVersion: v1
kind: Pod
metadata:
   name: pod-nginx
   labels:
     app: nginx_app
spec:
    containers:
      - name: nginx-container
        image: nginx:latest
```

## 2. Deploy the Pod

Apply the manifest file to your Kubernetes cluster using `kubectl`:

```bash
thor@jump-host ~\$  kubectl apply -f nginx.yaml 
pod/pod-nginx created
```

## 3. Verify Deployment Status

Check the status of the running pods to confirm successful deployment:

```bash
thor@jump-host ~\$ k get po
NAME        READY   STATUS    RESTARTS   AGE
pod-nginx   1/1     Running   0          5s
```
