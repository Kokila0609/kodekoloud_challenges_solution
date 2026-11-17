# Requirements

The Nautilus DevOps team is working on to setup some pre-requisites for an application that will send the greetings to different users. There is a sample deployment, that needs to be tested. Below is a scenario which needs to be configured on Kubernetes cluster. Please find below more details about it.

Create a pod named print-envars-greeting.

Configure spec as, the container name should be print-env-container and use bash image.

Create three environment variables:  

a. GREETING and its value should be Welcome to  

b. COMPANY and its value should be Stratos  

c. GROUP and its value should be Group

Use command ["/bin/sh", "-c", 'echo "$(GREETING) $(COMPANY) $(GROUP)"'] (please use this exact command), also set its restartPolicy policy to Never to avoid crash loop back.

You can check the output using kubectl logs -f print-envars-greeting command. 

Note: The kubectl utility on the jump-host has been configured to work with the Kubernetes cluster.

---

## 1. Pod Manifest (`greetings.yaml`)

Create a file named `greetings.yaml` with the following configuration:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: print-envars-greeting
spec:
  restartPolicy: Never
  containers:
    - name: print-env-container
      image: bash
      command: ["/bin/sh", "-c", 'echo "$(GREETING) $(COMPANY) $(GROUP)"']
      env:
        - name: GREETING
          value: Welcome to
        - name: COMPANY
          value: Stratos
        - name: GROUP
          value: Group
```

---

## 2. Deployment & Verification Steps

### Step 1: Create the Manifest File

Open an editor to create `greetings.yaml`:

```bash
vi greetings.yaml
```

### Step 2: Apply the Pod Manifest

Deploy the Pod to your cluster:

```bash
kubectl apply -f greetings.yaml
```

**Expected Output:**
```text
pod/print-envars-greeting created
```

### Step 3: Check Pod Status

Verify that the Pod executed its command and reached the `Completed` state:

```bash
kubectl get po
```

**Expected Output:**
```text
NAME                    READY   STATUS      RESTARTS   AGE
print-envars-greeting   0/1     Completed   0          4s
```

### Step 4: View Pod Logs

Inspect the output printed by the environment variables:

```bash
kubectl logs -f print-envars-greeting
```

**Expected Output:**
```text
Welcome to Stratos Group
```
