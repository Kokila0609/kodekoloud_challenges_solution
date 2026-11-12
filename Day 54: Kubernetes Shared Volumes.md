### Problem

We are working on an application that will be deployed on multiple containers within a pod on Kubernetes cluster. There is a requirement to share a volume among the containers to save some temporary data. The Nautilus DevOps team is developing a similar template to replicate the scenario. Below you can find more details about it.
 
 
 
Create a pod named volume-share-datacenter.
 
 
For the first container, use image debian with latest tag only and remember to mention the tag i.e debian:latest, container should be named as volume-container-datacenter-1, and run a sleep command for it so that it remains in running state. Volume volume-share should be mounted at path /tmp/beta.
 
 
For the second container, use image debian with the latest tag only and remember to mention the tag i.e debian:latest, container should be named as volume-container-datacenter-2, and again run a sleep command for it so that it remains in running state. Volume volume-share should be mounted at path /tmp/cluster.
 
 
Volume name should be volume-share of type emptyDir.
 
 
After creating the pod, exec into the first container i.e volume-container-datacenter-1, and just for testing create a file beta.txt with the content Welcome to xFusionCorp Industries under the mounted path of first container i.e /tmp/beta.
 
 
The file beta.txt should be present under the mounted path /tmp/cluster on the second container volume-container-datacenter-2 as well, since they are using a shared volume.
 
 
Note: The kubectl utility on the jump-host has been configured to work with the Kubernetes cluster.


---
# Pod YAML Configuration
Create a file:
```bash
vi volume-share-datacenter.yaml
```
Add the following configuration:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: volume-share-datacenter
spec:
  restartPolicy: Never
  volumes:
    - name: volume-share
      emptyDir: {}
  containers:
    - name: volume-container-datacenter-1
      image: ubuntu:latest
      command: ["sleep", "3600"]
      volumeMounts:
        - name: volume-share
          mountPath: /tmp/beta
    - name: volume-container-datacenter-2
      image: ubuntu:latest
      command: ["sleep", "3600"]
      volumeMounts:
        - name: volume-share
          mountPath: /tmp/demo
```
---
# Create the Pod
Apply the YAML file:
```bash
kubectl apply -f volume-share-datacenter.yaml
```
Or, using the `k` alias:
```bash
k apply -f volume-share-datacenter.yaml
```
---
# Verify the Pod
Check the Pod status:
```bash
k get po
```
Expected output:
```text
NAME                      READY   STATUS    RESTARTS   AGE
volume-share-datacenter   2/2     Running   0          30s
```
The following status confirms that both containers are running:
```text
2/2 Running
```
---
# Create a File in the First Container
Exec into the first container:
```bash
k exec -it volume-share-datacenter \
  -c volume-container-datacenter-1 -- /bin/bash
```
Verify the current directory:
```bash
pwd
```
Output:
```text
/
```
Move to the shared volume mounted at `/tmp/beta`:
```bash
cd /tmp/beta
```
Create the required file:
```bash
echo "Welcome to xFusionCorp Industries" > beta.txt
```
Verify the file:
```bash
cat beta.txt
```
Expected output:
```text
Welcome to xFusionCorp Industries
```
Exit the container:
```bash
exit
```
---
# Verify the Shared File in the Second Container
Exec into the second container:
```bash
k exec -it volume-share-datacenter \
  -c volume-container-datacenter-2 -- /bin/bash
```
Move to the second container's shared volume mount:
```bash
cd /tmp/demo
```
List the files:
```bash
ls
```
Expected output:
```text
beta.txt
```
Read the contents of the file:
```bash
cat beta.txt
```
Expected output:
```text
Welcome to xFusionCorp Industries
```
---
# How the Shared Volume Works
Both containers mount the same `emptyDir` volume:
```yaml
volumes:
  - name: volume-share
    emptyDir: {}
```
The first container mounts it at:
```text
/tmp/beta
```
The second container mounts the same volume at:
```text
/tmp/demo
```
Therefore:
```text
Container 1
/tmp/beta/beta.txt
        Shared emptyDir
        volume-share
Container 2
/tmp/demo/beta.txt
```
When the following file is created in container 1:
```text
/tmp/beta/beta.txt
```
it becomes available in container 2 as:
```text
/tmp/demo/beta.txt
```
even though the mount paths are different.
---
# Quick Commands
## Create Pod
```bash
k apply -f volume-share-datacenter.yaml
```
## Check Pod
```bash
k get po
```
## Enter Container 1
```bash
k exec -it volume-share-datacenter \
  -c volume-container-datacenter-1 -- /bin/bash
```
## Create File
```bash
echo "Welcome to xFusionCorp Industries" > /tmp/beta/beta.txt
```
## Enter Container 2
```bash
k exec -it volume-share-datacenter \
  -c volume-container-datacenter-2 -- /bin/bash
```
## Verify Shared File
```bash
cat /tmp/demo/beta.txt
```
Expected:
```text
Welcome to xFusionCorp Industries
```
---
# Faster Verification Without Entering the Containers
Instead of opening an interactive shell, create the file directly in container 1:
```bash
k exec volume-share-datacenter \
  -c volume-container-datacenter-1 -- \
  bash -c 'echo "Welcome to xFusionCorp Industries" > /tmp/beta/beta.txt'
```
Then read it directly from container 2:
```bash
k exec volume-share-datacenter \
  -c volume-container-datacenter-2 -- \
  cat /tmp/demo/beta.txt
```
Expected output:
```text
Welcome to xFusionCorp Industries
```
---
# Final Verification Checklist
- [x] Pod name is `volume-share-datacenter`
- [x] First container uses `ubuntu:latest`
- [x] Second container uses `ubuntu:latest`
- [x] First container is named `volume-container-datacenter-1`
- [x] Second container is named `volume-container-datacenter-2`
- [x] Volume name is `volume-share`
- [x] Volume type is `emptyDir`
- [x] First container mounts the volume at `/tmp/beta`
- [x] Second container mounts the volume at `/tmp/demo`
- [x] Both containers use `sleep` to remain running
- [x] `beta.txt` is created inside `/tmp/beta`
- [x] `beta.txt` is accessible from `/tmp/demo`
- [x] File contains `Welcome to xFusionCorp Industries`
## Key Concept
An `emptyDir` volume is created when the Pod starts and is shared by all containers in that Pod that mount the volume.
The containers can mount the same volume at different paths:
```text
volume-container-datacenter-1
        |
        | /tmp/beta
        |
    volume-share
      (emptyDir)
        |
        | /tmp/demo
        |
volume-container-datacenter-2
```
This is why a file created under `/tmp/beta` in the first container is immediately accessible under `/tmp/demo` in the second container.
 
