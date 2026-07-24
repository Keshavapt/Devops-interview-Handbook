# Docker Interview Crash Course

> **Goal:** Crack Docker interview questions for DevOps Engineers (2–8 Years Experience)

---

# 1. What is Docker?

## Definition

Docker is an open-source containerization platform that packages an application along with its dependencies, libraries, runtime, and configuration into a portable unit called a **Container**.

---

## Why Docker?

Before Docker:

- Works on Dev machine ❌
- Fails on QA ❌
- Fails on Production ❌

Reason:

- Different OS
- Different Java versions
- Different Python versions
- Missing libraries

Docker packages everything together.

> **Build Once, Run Anywhere**

---

## Real Example

Spring Boot Application

Without Docker

```
Install Java

↓

Install Maven

↓

Copy Jar

↓

Configure Environment

↓

Run Application
```

With Docker

```
docker run company/payment-service:v1
```

One command.

---

# 2. Why Docker?

Docker solves

- Environment mismatch
- Dependency conflicts
- Easy deployment
- Fast rollback
- Better scalability
- Better resource utilization

---

# 3. Virtual Machine vs Docker

| Virtual Machine         | Docker             |
| ----------------------- | ------------------ |
| Hardware Virtualization | OS Virtualization  |
| Guest OS Required       | Shared Host Kernel |
| Startup in Minutes      | Startup in Seconds |
| Heavy                   | Lightweight        |
| GB Image Size           | MB Image Size      |
| More Memory             | Less Memory        |

---

Interview Tip

If asked

> Why are containers lightweight?

Answer:

Containers share the Host OS kernel instead of running a complete Guest Operating System.

---

# 4. Docker Architecture

```
Developer

↓

Docker CLI

↓

Docker Daemon

↓

Docker Engine

↓

Image

↓

Container
```

Components

- Docker Client
- Docker Daemon
- Docker Engine
- Registry
- Images
- Containers

---

## What happens when you execute

```bash
docker run nginx
```

Answer

1. Docker Client receives command.
2. Sends request to Docker Daemon.
3. Checks local image.
4. Pulls image if missing.
5. Creates writable layer.
6. Configures networking.
7. Mounts volumes.
8. Executes CMD/ENTRYPOINT.
9. Container starts.

Very common interview question.

---

# 5. Docker Image vs Container

Docker Image

- Blueprint
- Read-only
- Immutable
- Stored in Registry

Docker Container

- Running Instance
- Writable Layer
- Temporary
- Running Process

Interview Answer

Image is like a class.

Container is like an object.

---

# 6. Docker Layers

Every Dockerfile instruction creates a layer.

```
FROM Ubuntu

↓

RUN apt update

↓

RUN install Java

↓

COPY app.jar

↓

CMD java -jar
```

Each step creates one layer.

Benefits

- Cache
- Faster Build
- Layer Sharing

---

Interview Question

Why Docker builds become faster?

Answer

Docker caches unchanged layers and rebuilds only modified layers.

---

# 7. Copy-on-Write

Image Layers

↓

Shared

↓

Container A

Container B

Container C

Only modified files are copied into the writable layer.

Benefits

- Faster
- Less Storage
- Better Performance

---

# 8. Dockerfile

A Dockerfile is a text file containing instructions to build an image.

Build Command

```bash
docker build -t payment:v1 .
```

---

# Dockerfile Instructions

## FROM

Base Image

```dockerfile
FROM openjdk:21
```

---

## WORKDIR

Working Directory

```dockerfile
WORKDIR /app
```

---

## COPY

Copies files

```dockerfile
COPY app.jar .
```

---

## ADD

Can copy and extract archives.

Prefer COPY.

---

## RUN

Executes commands during build.

```dockerfile
RUN apt update
```

---

## CMD

Default command.

```dockerfile
CMD ["java","-jar","app.jar"]
```

---

## ENTRYPOINT

Main executable.

Cannot be overridden easily.

---

Interview

CMD vs ENTRYPOINT

CMD

Default command.

ENTRYPOINT

Main application.

Usually

```
ENTRYPOINT java

CMD app.jar
```

---

## ENV

Environment Variable

```dockerfile
ENV JAVA_HOME=/usr/lib/java
```

---

## ARG

Build-time variable.

Not available after container starts.

---

Interview

ARG vs ENV

ARG

Build Time

ENV

Runtime

---

## EXPOSE

Documents application port.

Does NOT publish.

---

Interview

EXPOSE vs -p

EXPOSE

Documentation

-p

Publishes port.

---

## USER

Never run as root.

```dockerfile
USER appuser
```

Production Best Practice.

---

# 9. Multi-stage Build

Problem

Builder image contains

- Maven
- Gradle
- Source Code

Production doesn't need them.

Solution

Builder

↓

Compile

↓

Copy only Jar

↓

Runtime Image

Smaller Image.

---

Benefits

- Smaller Images
- Faster Downloads
- Better Security

---

# 10. Important Commands

## Images

```bash
docker images

docker pull

docker push

docker rmi
```

---

## Containers

```bash
docker ps

docker ps -a

docker run

docker stop

docker start

docker restart

docker rm
```

---

## Logs

```bash
docker logs container
```

Follow

```bash
docker logs -f container
```

---

## Execute

```bash
docker exec -it container bash
```

---

## Inspect

```bash
docker inspect container
```

---

## Statistics

```bash
docker stats
```

---

# 11. Docker Networking

Default Driver

Bridge

Types

- Bridge
- Host
- None
- Overlay
- Macvlan

Remember

Bridge → Default

Host → Host Network

None → No Network

Overlay → Multi-host

---

Interview

Bridge vs Host

Bridge

Separate Network

Host

Shares Host Network

---

# 12. Docker Volumes

Containers are temporary.

If container dies,

Data disappears.

Volumes solve this.

```
Container

↓

Volume

↓

Host Disk
```

---

Types

Named Volume

```bash
docker volume create data
```

Bind Mount

```
Host Folder

↓

Container Folder
```

---

Interview

Bind Mount vs Volume

Bind Mount

Host Directory

Volume

Managed by Docker

---

# 13. Docker Compose

Runs multiple containers.

Example

Frontend

↓

Backend

↓

Database

Single command

```bash
docker compose up -d
```

Stop

```bash
docker compose down
```

---

# 14. Docker Registry

Stores Images.

Examples

Docker Hub

AWS ECR

GitLab Registry

Azure ACR

---

Workflow

Build

↓

Push

↓

Registry

↓

Pull

↓

Run

---

# 15. Docker Security

Production

Never

- Run as root
- Store passwords
- Use latest tag
- Ignore vulnerabilities

Use

- Trivy
- Non-root User
- Alpine Images
- Secrets Manager

---

# 16. Production Best Practices

✅ Multi-stage Builds

✅ Smaller Images

✅ Version Tags

✅ .dockerignore

✅ Healthcheck

✅ Non-root User

✅ Resource Limits

✅ Scan Images

---

# 17. CI/CD Flow

Developer

↓

Git Push

↓

GitLab Pipeline

↓

Build

↓

Test

↓

Docker Build

↓

Trivy Scan

↓

Push to ECR

↓

Deploy to Kubernetes

---

# Top Interview Questions

### What is Docker?

Containerization platform.

---

### Difference between Image and Container?

Blueprint vs Running Instance.

---

### Difference between VM and Docker?

Hardware Virtualization vs OS Virtualization.

---

### Why are containers lightweight?

Shared Host Kernel.

---

### Explain docker run internally.

Client

↓

Daemon

↓

Pull Image

↓

Create Writable Layer

↓

Networking

↓

CMD

↓

Running Container

---

### Difference between CMD and ENTRYPOINT?

CMD

Default command.

ENTRYPOINT

Main executable.

---

### Difference between COPY and ADD?

COPY

Copies files.

ADD

Can extract archives and fetch URLs.

---

### Difference between ARG and ENV?

ARG

Build Time.

ENV

Runtime.

---

### Difference between EXPOSE and -p?

EXPOSE

Documents ports.

-p

Publishes ports.

---

### Why Multi-stage Build?

Smaller Image.

Better Security.

Faster Deployment.

---

### Why Docker Volumes?

Persist Data.

---

### Bind Mount vs Volume?

Host Directory vs Docker Managed Storage.

---

### Which network is default?

Bridge.

---

### Why use Alpine?

Small Image.

Less Vulnerabilities.

Faster Download.

---

### Why avoid latest tag?

Deployments become unpredictable.

Always use versioned tags.

---

### How do you debug a running container?

```bash
docker logs

docker exec -it

docker inspect

docker stats
```

---

# 5-Minute Revision

- Docker = Containerization
- Image = Blueprint
- Container = Running Image
- Dockerfile = Image Recipe
- Docker Hub = Image Repository
- Bridge = Default Network
- Volume = Persistent Storage
- Compose = Multi-container
- Multi-stage = Smaller Images
- Trivy = Security Scanner
- CMD = Default Command
- ENTRYPOINT = Main Process
- COPY > ADD
- ENV = Runtime
- ARG = Build Time
- EXPOSE ≠ Port Mapping
- `-p` = Publish Port
- `docker logs` = Logs
- `docker exec` = Shell
- `docker stats` = Resources
