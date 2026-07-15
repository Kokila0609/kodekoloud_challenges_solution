# Requirements

A python app needed to be Dockerized, and then it needs to be deployed on App Server 1. We have already copied a requirements.txt file (having the app dependencies) under /python_app/src/ directory on App Server 3. Further complete this task as per details mentioned below:



Create a Dockerfile under /python_app directory:

Use any python image as the base image.
Install the dependencies using requirements.txt file.
Expose the port 5002.
Run the server.py script using CMD.

Build an image named nautilus/python-app using this Dockerfile.


Once image is built, create a container named pythonapp_nautilus:

Map port 5001 of the container to the host port 8099.

Once deployed, you can test the app using curl command on App Server 1.


curl http://localhost:8099/


# Python Application Containerization Deployment

Follow this step-by-step workflow to construct, verify, and execute the Python application container environment.

### Step 1: Create the Dockerfile
Create a file named `Dockerfile` under the `/python_app` workspace directory with the configuration below:

```dockerfile
FROM python:3.13-slim
WORKDIR /app
COPY /src/requirements.txt ./
RUN pip install -r requirements.txt
COPY /src ./
EXPOSE 5002
CMD ["python", "server.py"]
```

### Step 2: Build the Container Image
Compile your environment layers and tag the resulting image as `nautilus/python-app` by targeting the local context point:

```bash
docker build -t nautilus/python-app .
```

### Step 3: Verify the Target Image Location
Confirm that the compilation engine successfully generated the structural file artifact inside your local repository:

```bash
docker images
```

**Expected terminal output status:**
```text
REPOSITORY            TAG       IMAGE ID       CREATED          SIZE
nautilus/python-app   latest    8cbc1aefef04   54 seconds ago   132MB
```

### Step 4: Deploy and Launch the Active Container
Run the build layer inside detached background operational status, forwarding incoming traffic routing from host destination port `8099` into your exposed container interface engine at port `5002`:

```bash
docker run --name pythonapp_nautilus -d -p 8099:5002 nautilus/python-app
```

### Step 5: Check Running Container Status
Verify that the runtime microservice engine initialized properly and is actively listening for client interactions:

```bash
docker ps
```

**Expected terminal output status:**
```text
CONTAINER ID   IMAGE                 COMMAND              CREATED         STATUS         PORTS                                       NAMES
e0c1be141e4b   nautilus/python-app   "python server.py"   8 seconds ago   Up 6 seconds   0.0.0.0:8099->5002/tcp, :::8099->5002/tcp   pythonapp_nautilus
```

### Step 6: Validate Application API Connectivity
Query the active port via a localized HTTP handshake curl operation to ensure standard runtime output functionality:

```bash
curl http://localhost:8099/
```

**Expected terminal output state:**
```text
Welcome to xFusionCorp Industries!
```
