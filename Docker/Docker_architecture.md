---
# 8. Docker Architecture

## What is Docker Architecture?

Docker follows a **Client-Server Architecture** where the Docker Client communicates with the Docker Daemon using REST APIs.

The daemon performs all operations such as building images, running containers, creating networks, and managing volumes.

```
+----------------------+
|    Docker Client     |
| (docker CLI / API)   |
+----------+-----------+
|
| REST API
|
+----------v-----------+
|    Docker Daemon     |
|      (dockerd)       |
+----------+-----------+
|
-------------------------------------------------
|                |               |              |
|                |               |              |
+-------v------+ +--------v------+ +------v------+ +-----v------+
| Docker Image | | Docker Volume | | Docker Net | | Containers  |
+--------------+ +---------------+ +------------+ +------------+
|
|
+---------v----------+
| Docker Registry    |
| Docker Hub / ECR   |
| GitLab Registry    |
+--------------------+
```
---

# Docker Components

Docker Architecture consists of six major components.

1. Docker Client
2. Docker Daemon
3. Docker Engine
4. Docker Registry
5. Docker Image
6. Docker Container

---

# 8.1 Docker Client

## Definition

The Docker Client is the interface through which users interact with Docker.

Whenever you execute a Docker command, the client sends the request to the Docker Daemon.

Examples

```bash
docker build
docker run
docker ps
docker images
docker pull
docker push
```

The Docker Client does **not** directly create containers.

It only sends requests.

---

## Working

```
Developer

↓

docker run nginx

↓

Docker Client

↓

Docker Daemon
```

---

## Real-world Example

A DevOps Engineer executes

```bash
docker build -t payment-service:v1 .
```

The client sends the build request to the daemon.

The daemon builds the image.

---

## Interview Question

### What is Docker Client?

### Answer

Docker Client is the command-line interface used to communicate with Docker Engine.

It sends REST API requests to Docker Daemon for executing Docker operations.

---

# 8.2 Docker Daemon (dockerd)

## Definition

Docker Daemon is the background service responsible for managing Docker objects.

Process Name

```
dockerd
```

---

## Responsibilities

- Builds Images
- Creates Containers
- Starts Containers
- Stops Containers
- Removes Containers
- Pulls Images
- Pushes Images
- Creates Networks
- Creates Volumes

---

## How to Verify Daemon

Linux

```bash
systemctl status docker
```

or

```bash
ps -ef | grep dockerd
```

---

## Interview Question

### What is Docker Daemon?

### Answer

Docker Daemon is the background process that listens for Docker API requests and performs all Docker operations such as image management, container lifecycle management, networking, and storage.

---

# 8.3 Docker Engine

## Definition

Docker Engine is the runtime responsible for creating and managing containers.

It includes

- Docker Client
- Docker Daemon
- REST API

It is installed on every Docker Host.

---

## Components

```
Docker Engine

↓

Docker Client

↓

REST API

↓

Docker Daemon
```

---

## Responsibilities

- Build Images
- Run Containers
- Manage Networking
- Manage Storage
- Handle Docker API Requests

---

## Interview Question

### What is Docker Engine?

### Answer

Docker Engine is the complete runtime environment responsible for building, running, and managing Docker containers.

It consists of Docker Client, Docker Daemon, and REST APIs.

---

# 8.4 Docker Registry

## Definition

A Docker Registry stores Docker Images.

Images can be

- Downloaded
- Uploaded
- Shared
- Version Controlled

---

## Popular Registries

- Docker Hub
- AWS Elastic Container Registry (ECR)
- Azure Container Registry (ACR)
- Google Artifact Registry (GAR)
- GitLab Container Registry
- Harbor

---

## Workflow

```
docker push

↓

Docker Registry

↓

docker pull

↓

Docker Host
```

---

## Example

Pull image

```bash
docker pull nginx
```

Push image

```bash
docker push company/payment-service:v2
```

---

## Interview Question

### What is Docker Registry?

### Answer

Docker Registry is a repository used to store Docker Images.

It allows teams to share, version, and distribute container images.

---

# Public vs Private Registry

| Public Registry    | Private Registry  |
| ------------------ | ----------------- |
| Docker Hub         | AWS ECR           |
| Anyone can access  | Restricted Access |
| Internet           | Internal Network  |
| Open Source Images | Company Images    |

---

# 8.5 Docker Image

## Definition

A Docker Image is a read-only template used to create Docker Containers.

It contains

- Application
- Runtime
- Libraries
- Dependencies
- Configuration
- Environment Variables

---

## Characteristics

- Immutable
- Versioned
- Read-only
- Layered
- Reusable

---

## Example

```
ubuntu:22.04

nginx:latest

python:3.12

payment-service:v3
```

---

## Lifecycle

```
Dockerfile

↓

docker build

↓

Image

↓

docker push

↓

Registry

↓

docker pull

↓

docker run

↓

Container
```

---

## Interview Question

### What is Docker Image?

### Answer

Docker Image is an immutable read-only template that contains everything required to run an application.

Containers are created from Docker Images.

---

# 8.6 Docker Container

## Definition

A Docker Container is a running instance of a Docker Image.

Think of it as

Image → Blueprint

Container → Running Application

---

## Example

Pull Image

```bash
docker pull nginx
```

Create Container

```bash
docker run nginx
```

---

## Multiple Containers

One image can create multiple containers.

```
Payment Image

↓

----------------------------

Container 1

Container 2

Container 3

Container 4
```

---

## Characteristics

- Lightweight
- Isolated
- Fast
- Portable
- Ephemeral
- Writable Layer

---

## Interview Question

### What is a Docker Container?

### Answer

A Docker Container is the running instance of a Docker Image.

It includes a writable layer on top of immutable image layers and executes the application process.

---

# Docker Request Flow

Suppose we execute

```bash
docker run nginx
```

The internal workflow is

```
Developer

↓

Docker Client

↓

Docker Daemon

↓

Image Exists?

↓

YES ----------------------------→ Create Container

↓

NO

↓

Download Image

↓

Create Writable Layer

↓

Configure Network

↓

Mount Volumes

↓

Execute ENTRYPOINT/CMD

↓

Running Container
```

---

# Container Lifecycle

```
Created

↓

Running

↓

Paused

↓

Stopped

↓

Restarted

↓

Removed
```

---

## Lifecycle Commands

Create

```bash
docker create nginx
```

Run

```bash
docker run nginx
```

Pause

```bash
docker pause
```

Resume

```bash
docker unpause
```

Stop

```bash
docker stop
```

Restart

```bash
docker restart
```

Remove

```bash
docker rm
```

---

# Real Production Example

```
Developer

↓

Git Push

↓

GitLab Pipeline

↓

Build Java Application

↓

Docker Build

↓

Trivy Scan

↓

Push Image to AWS ECR

↓

Deploy to Kubernetes

↓

Pods Running Containers
```

Docker ensures that exactly the same image moves through Development, QA, UAT, and Production.

---

# Common Mistakes

❌ Docker Client creates containers.

✔ Docker Daemon creates containers.

---

❌ Docker Engine and Docker Daemon are the same.

✔ Docker Engine includes Docker Client, Daemon, and REST API.

---

❌ Images are writable.

✔ Images are immutable.

---

❌ Containers are stored in Docker Registry.

✔ Images are stored in Docker Registry.

---

# Quick Revision

| Component        | Purpose                   |
| ---------------- | ------------------------- |
| Docker Client    | Sends Commands            |
| Docker Daemon    | Executes Commands         |
| Docker Engine    | Runtime Environment       |
| Docker Registry  | Stores Images             |
| Docker Image     | Read-only Blueprint       |
| Docker Container | Running Instance of Image |

---

# Interview Questions

### Q1. Explain Docker Architecture.

**Answer**

Docker follows a Client-Server Architecture where the Docker Client communicates with the Docker Daemon using REST APIs. The daemon manages images, containers, networks, and volumes. Images are stored in registries such as Docker Hub or AWS ECR, and containers are created from these images.

---

### Q2. Difference between Docker Engine and Docker Daemon?

**Answer**

Docker Daemon is the background service (`dockerd`) that performs Docker operations.

Docker Engine is the complete runtime environment consisting of the Docker Client, Docker Daemon, and REST API.

---

### Q3. What happens internally when you execute `docker run nginx`?

**Answer**

Docker performs the following steps:

1. Receives the request from the Docker Client.
2. Checks whether the image exists locally.
3. Pulls the image from the registry if it is not available.
4. Creates a writable container layer.
5. Configures namespaces and cgroups.
6. Sets up networking and volumes.
7. Executes the `ENTRYPOINT` or `CMD`.
8. Starts the container.

---

### Q4. Can one Docker Image create multiple Containers?

**Answer**

Yes. A single Docker Image can be used to create multiple independent containers. Each container gets its own writable layer while sharing the same read-only image layers.
