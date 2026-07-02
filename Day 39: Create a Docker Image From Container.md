# Container Backup and Image Creation Guide

A Nautilus developer was working to test new changes on a container. To maintain a backup of these changes, a request was raised for the DevOps team to create a new image from this running container.

## Objective
* Create an image named `blog:nautilus` on **Application Server 3** from a container named `ubuntu_latest` running on that same server.

---

## Step-by-Step Execution

### 1. Identify the Running Container
Locate the container name or ID to target for the image backup.

```bash
[banner@stapp03 ~]\$ docker ps -a
CONTAINER ID   IMAGE     COMMAND       CREATED          STATUS          PORTS     NAMES
c2c9b0aa5d88   ubuntu    "/bin/bash"   11 minutes ago   Up 11 minutes             ubuntu_latest
```

### 2. Commit the Container Changes
Create a new image from the identified `ubuntu_latest` container.

```bash
[banner@stapp03 ~]\$ docker commit ubuntu_latest
sha256:294ead75acb765690bb3b7488d9c2bbdcf5ffc82294ce261fa788f053c93fa5b
```

### 3. Verify the Saved Image
Check the local image list to ensure the container state was successfully committed. The initial commit will appear with a `<none>` repository and tag name.

```bash
[banner@stapp03 ~]\$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED          SIZE
<none>       <none>    294ead75acb7   10 seconds ago   140MB
ubuntu       latest    4aaf0b273f92   5 days ago       100MB
```

### 4. Tag the New Image
Assign the required repository name (`blog`) and tag (`nautilus`) to the new image ID (`294ead75acb7`).

```bash
[banner@stapp03 ~]\$ docker tag 294ead75acb7 blog:nautilus
```

### 5. Final Image Verification
Verify that the `blog:nautilus` image is successfully registered and available.

```bash
[banner@stapp03 ~]\$ docker images
REPOSITORY   TAG        IMAGE ID       CREATED          SIZE
blog         nautilus   294ead75acb7   54 seconds ago   140MB
ubuntu       latest     4aaf0b273f92   5 days ago       100MB
[banner@stapp03 ~]\$
```
### Note: 

you can also run the below command at once

```
docker commit <container_id_or_name> <new_image_name>:<tag>

docker commit ubuntu_latest blog:nautilus

```
