# Requirement:

The Nautilus DevOps team aims to containerize various applications following a recent meeting with the application development team. They intend to conduct testing with the following steps:


Install docker-ce and docker compose packages on App Server 1.


Initiate the docker service.


# Docker Engine Installation Guide

Follow these steps to configure the repository and install Docker Engine using the DNF package manager.

## Step 1: Set Up the Repository
Install the `dnf-plugins-core` package. This package provides the core commands required to manage your DNF repositories.

```bash
sudo dnf -y install dnf-plugins-core
```

---

## Step 2: Install Docker Engine
Run the following command to install the latest production-ready version of Docker, along with its CLI components and plugins:

```bash
sudo dnf install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

---

## Step 3: Enable and Start the Docker Service
Configure the system to start the Docker Engine immediately and ensure it launches automatically every time the server boots up:

```bash
sudo systemctl enable --now docker
```

---

## Step 4: Verify Installation Status
Check the real-time operational status of the Docker system service to ensure it is running successfully:

```bash
sudo systemctl status docker
```


ref: https://docs.docker.com/engine/install/rhel/
