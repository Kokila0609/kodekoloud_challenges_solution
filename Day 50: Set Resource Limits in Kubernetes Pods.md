# Requirement:

Create a pod named httpd-pod with a container named httpd-container. Use the httpd image with the latest tag (specify as httpd:latest). Configure the following container-level resource requests and limits for the container:

Requests: Memory: 15Mi, CPU: 100m

# Kubernetes Pod Deployment Documentation

Follow these steps to generate, configure, and verify the `httpd-pod` manifest with required resource limits.

### Step 1: Generate the Base Manifest
Run the following `kubectl` command to generate a template YAML file using dry-run mode:

```bash
kubectl run httpd-pod --image=httpd:latest --dry-run=client -o yaml > httpd.yaml
```

### Step 2: Configure Resources and Verify Manifest
Edit the `httpd.yaml` file to ensure the container name, resource requests, and resource limits are exactly as specified (ensuring `cpu` is written in lowercase). 

```bash
cat httpd.yaml
```

**Expected Output:**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: httpd-pod
spec:
  containers:
    - name: httpd-container
      image: httpd:latest
      resources: 
        requests:
          memory: "15Mi"
          cpu: "100m"
        limits:
          memory: "20Mi"
          cpu: "100m"
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```
