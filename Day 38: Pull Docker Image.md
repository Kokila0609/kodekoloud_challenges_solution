# Re-tagging Docker Images for Containerized Application Testing

## Requirement
The Nautilus project developers are preparing to start testing on a new project. To test containerized environment application features, the DevOps team must perform the following task:

* Pull the `busybox:musl` image on **App Server 2** in the Stratos Datacenter.
* Re-tag (create a new tag) this image as `busybox:local`.

---

## Step-by-Step Implementation

### 1. Pull the Target Docker Image
Log in to the application server (`stapp02`) and pull the specific `musl` variant of the `busybox` image from the official repository.

```bash
[steve@stapp02 ~]\$ docker image pull busybox:musl
musl: Pulling from library/busybox
a5156bff9914: Pull complete 
Digest: sha256:8635836765b0c4c43970660219739baa58b0883c2e429e4b8918f7dd1519455c
Status: Downloaded newer image for busybox:musl
docker.io/library/busybox:musl
```

### 2. Verify the Downloaded Image
Confirm that the image was downloaded successfully and note its unique **Image ID**.

```bash
[steve@stapp02 ~]\$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED       SIZE
busybox      musl      5a93558ae921   7 weeks ago   1.53MB
```

### 3. Create the New Local Tag
Re-tag the existing image to match the `busybox:local` specification required by the development team.

```bash
[steve@stapp02 ~]\$ docker tag busybox:musl busybox:local
```

### 4. Verify the New Image Tag
List the local Docker images again to confirm that `busybox:local` now exists and points to the exact same **Image ID** (`5a93558ae921`) as the source image.

```bash
[steve@stapp02 ~]\$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED       SIZE
busybox      local     5a93558ae921   7 weeks ago   1.53MB
busybox      musl      5a93558ae921   7 weeks ago   1.53MB
```
### ref: https://docs.docker.com/reference/cli/docker/image/tag/
