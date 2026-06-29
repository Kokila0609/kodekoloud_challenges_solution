

## Objective
Deploy an Nginx container on **Application Server 2** based on the specified engineering requirements.

## Requirements
* **Target Server:** Application Server 2 (`stapp02`)
* **Container Name:** `nginx_2`
* **Docker Image:** `nginx:alpine`
* **Desired State:** Running (`Up`)

---

## Deployment Steps & Execution

### 1. Initialize and Run the Container
Execute the `docker run` command using the detached flag (`-d`) to ensure it runs in the background.

```bash
[steve@stapp02 ~]\$ docker run -d --name nginx_2 nginx:alpine
Unable to find image 'nginx:alpine' locally
alpine: Pulling from library/nginx
e6f31ffc071e: Pull complete 
c16defe09b2f: Pull complete 
5b429a43b8df: Pull complete 
967885d218c5: Pull complete 
ab1fd9049751: Pull complete 
ce42635eeddd: Pull complete 
01bf363d61e6: Pull complete 
c75b9c33e8b0: Pull complete 
Digest: sha256:54f2a904c251d5a34adf545a72d32515a15e08418dae0266e23be2e18c66fefa
Status: Downloaded newer image for nginx:alpine
424d8ec6c959b85fcf3de1dcfb3dc554773d937f0f3421fc7eccdc45f16a2a52
```

### 2. Verify Container Runtime State
Check the active containers to confirm correct naming, image utilization, and operational status.

```bash
[steve@stapp02 ~]\$ docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS         PORTS     NAMES
424d8ec6c959   nginx:alpine   "/docker-entrypoint.…"   10 seconds ago   Up 9 seconds   80/tcp    nginx_2
```

## Conclusion
The container `nginx_2` was successfully created from the `nginx:alpine` image and is verified as actively **Up**.
