
# Requirements:

The Nautilus DevOps team is delving into Kubernetes for app management. One team member needs to create a deployment following these details:


Create a deployment named httpd to deploy the application httpd using the image httpd:latest (ensure to specify the tag)




### Step 1: Generate the Deployment Manifest
Use a dry-run command to generate the `httpd.yaml` manifest without actually creating the resource in the cluster.

```bash
thor@jump-host ~\$ kubectl create deployment httpd --image=httpd:latest --dry-run=client -o yaml > httpd.yaml
```

### Step 2: Verify the Generated YAML File
Inspect the contents of the generated `httpd.yaml` file to ensure the configuration is correct.

```bash
thor@jump-host ~\$ cat httpd.yaml 
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: httpd
  name: httpd
spec:
  replicas: 1
  selector:
    matchLabels:
      app: httpd
  strategy: {}
  template:
    metadata:
      labels:
        app: httpd
    spec:
      containers:
      - image: httpd:latest
        name: httpd
        resources: {}
status: {}
```

### Step 3: Apply the Manifest to the Cluster
Deploy the configuration file to your active Kubernetes cluster using the `kubectl apply` command.

```bash
thor@jump-host ~\$ kubectl apply -f httpd.yaml 
deployment.apps/httpd created
```

### Step 4: Verify the Resources
Check the status of the deployment and the running pods to confirm successful initialization.

* **Check the deployment status:**
  ```bash
  thor@jump-host ~\$ k get deployments.apps 
  NAME    READY   UP-TO-DATE   AVAILABLE   AGE
  httpd   1/1     1            1           7s
  ```

* **Check the running pods:**
  ```bash
  thor@jump-host ~\$ k get po
  NAME                     READY   STATUS    RESTARTS   AGE
  httpd-6c755866c7-cbc9p   1/1     Running   0          12s
  ```
