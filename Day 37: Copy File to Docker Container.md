# Copying Confidential Data to Docker Container

## Requirement
The Nautilus DevOps team possesses confidential data on **App Server 3** in the Stratos Datacenter. A container named `ubuntu_latest` is running on the same server.

**Task:** Copy an encrypted file `/tmp/nautilus.txt.gpg` from the Docker host to the `ubuntu_latest` container located at `/usr/src/`. Ensure the file is not modified during this operation.

---

## Step-by-Step Implementation

### 1. Verify the Source File on the Host
Log in to the application server (`stapp03`) and verify the presence, permissions, and size of the encrypted file.

```bash
[banner@stapp03 ~]\$ ls -al /tmp/nautilus.txt.gpg 
-rw-r--r-- 1 root root 105 Jun 30 07:21 /tmp/nautilus.txt.gpg
```

### 2. Identify the Target Container ID
List the active Docker containers to find the ID of the `ubuntu_latest` container.

```bash
[banner@stapp03 src]\$ docker ps
CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS         PORTS     NAMES
4195fcf97e5b   ubuntu    "/bin/bash"   4 minutes ago   Up 4 minutes             ubuntu_latest
```

### 3. Copy the File into the Container
Use the `docker cp` command to transfer the encrypted file from the host system into the destination directory inside the container.

```bash
[banner@stapp03 src]\$ docker cp /tmp/nautilus.txt.gpg 4195fcf97e5b:/usr/src/
Successfully copied 2.05kB to 4195fcf97e5b:/usr/src/
```

### 4. Verify the Transfer Inside the Container
Execute an `ls` command inside the container to ensure the file was successfully copied and that the file size matches the original host file (105 bytes), proving it was not modified.

```bash
[banner@stapp03 src]\$ docker exec -it 4195fcf97e5b ls -al /usr/src/
total 16
drwxr-xr-x 1 root root 4096 Jun 30 07:26 .
drwxr-xr-x 1 root root 4096 Jun 10 03:29 ..
-rw-r--r-- 1 root root  105 Jun 30 07:21 nautilus.txt.gpg
```

---

## Reference Links
* [Docker Container CP CLI Reference](https://docs.docker.com/reference/cli/docker/container/cp/)
