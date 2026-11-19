# Requirement

The Nautilus DevOps teams is planning to set up a Grafana tool to collect and analyze analytics from some applications. They are planning to deploy it on Kubernetes cluster. Below you can find more details. 

1.) Create a deployment named grafana-deployment-nautilus using any grafana image for Grafana app. Set other parameters as per your choice.  

2.) Create NodePort type service with nodePort 32000 to expose the app. 

You do not need to make any configuration changes inside the Grafana app once deployed; just make sure you can access the Grafana login page. 

Note: The kubectl utility on the jump-host has been configured to work with the Kubernetes cluster

Solution:

# Deploy Grafana on Kubernetes

## 1. Create the Kubernetes Manifest
Create a file named `grafana.yaml` with the following Service and Deployment configuration:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: grafana-service
spec:
  type: NodePort
  selector:
    app: grafana
  ports:
    - port: 3000
      targetPort: 3000
      nodePort: 32000

---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: grafana-deployment-nautilus
  labels:
    app: grafana
spec:
  replicas: 1
  selector:
    matchLabels:
      app: grafana
  template:
    metadata:
      labels:
        app: grafana
    spec:
      containers:
        - name: grafana-container
          image: grafana/grafana:latest
          ports:
            - name: grafana-service
              containerPort: 3000
```

## 2. Apply the Configuration
Deploy the resources to the cluster:

```bash
kubectl apply -f grafana.yaml
```

## 3. Verify the Deployment

### Check Deployments
```bash
kubectl get deployments.apps
```
**Output:**
```text
NAME                          READY   UP-TO-DATE   AVAILABLE   AGE
grafana-deployment-nautilus   1/1     1            1           2m57s
```

### Check Pods
```bash
kubectl get po
```
**Output:**
```text
NAME                                           READY   STATUS    RESTARTS   AGE
grafana-deployment-nautilus-787859648d-5n9zf   1/1     Running   0          2m59s
```

### Check Services
```bash
kubectl get svc
```
**Output:**
```text
NAME              TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
grafana-service   NodePort    10.43.50.202   <none>        3000:32000/TCP   3m7s
kubernetes        ClusterIP   10.43.0.1      <none>        443/TCP          61m
```
