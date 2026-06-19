# 03-Docker.md

# Docker Interview Handbook

---

# What is Docker?

Docker is a containerization platform that packages applications and their dependencies into lightweight, portable containers.

Benefits:

- Portability
- Consistency
- Faster deployment
- Resource efficiency
- Scalability

---

# Why Docker?

Traditional Deployment:

```text
Application
+
Dependencies
+
OS Configuration
```

Problems:

- Works on my machine
- Dependency conflicts
- Environment drift

Docker solves this by packaging everything together.

---

# Virtualization vs Containerization

## Virtual Machines

```text
Hardware
 └─ Hypervisor
     ├─ VM1 (OS + App)
     ├─ VM2 (OS + App)
     └─ VM3 (OS + App)
```

Characteristics:

- Heavy
- Full OS per VM
- More resources

---

## Containers

```text
Hardware
 └─ Host OS
     └─ Docker Engine
         ├─ Container1
         ├─ Container2
         └─ Container3
```

Characteristics:

- Lightweight
- Share Host Kernel
- Faster startup

---

## Interview Answer

### Difference Between VM and Docker

| VM                  | Docker             |
| ------------------- | ------------------ |
| Full OS             | Shared Kernel      |
| Heavy               | Lightweight        |
| Minutes to boot     | Seconds            |
| High resource usage | Low resource usage |
| Hypervisor          | Docker Engine      |

---

# Docker Architecture

```text
Docker Client
      |
Docker Daemon
      |
Docker Engine
      |
Containers
```

---

## Components

### Docker Client

Commands:

```bash
docker build
docker run
docker pull
```

---

### Docker Daemon

Background service that manages containers.

---

### Docker Registry

Stores images.

Examples:

- Docker Hub
- AWS ECR
- Azure ACR
- GitLab Registry

---

# Docker Image

## What is an Image?

Read-only template used to create containers.

Example:

```bash
docker pull nginx
```

---

# Docker Container

## What is a Container?

Running instance of an image.

Example:

```bash
docker run nginx
```

---

## Image vs Container

| Image     | Container        |
| --------- | ---------------- |
| Template  | Running instance |
| Read-only | Read-write       |
| Static    | Dynamic          |

---

# Dockerfile

## What is Dockerfile?

Blueprint for building images.

Example:

```dockerfile
FROM openjdk:17

WORKDIR /app

COPY target/app.jar .

EXPOSE 8080

ENTRYPOINT ["java","-jar","app.jar"]
```

---

# Dockerfile Instructions

## FROM

Base image.

```dockerfile
FROM nginx
```

---

## WORKDIR

Working directory.

```dockerfile
WORKDIR /app
```

---

## COPY

Copy local files.

```dockerfile
COPY app.jar .
```

---

## ADD

Copy files + URL extraction.

```dockerfile
ADD archive.tar.gz /app
```

---

## COPY vs ADD

| COPY        | ADD                 |
| ----------- | ------------------- |
| File copy   | File copy + extract |
| Preferred   | Less preferred      |
| Predictable | Extra functionality |

---

## RUN

Execute command during build.

```dockerfile
RUN apt-get update
```

---

## CMD

Default command.

```dockerfile
CMD ["java","-jar","app.jar"]
```

---

## ENTRYPOINT

Fixed executable.

```dockerfile
ENTRYPOINT ["java","-jar","app.jar"]
```

---

## CMD vs ENTRYPOINT

### CMD

Can be overridden.

```dockerfile
CMD ["nginx"]
```

Run:

```bash
docker run image bash
```

Result:

```text
bash runs
```

---

### ENTRYPOINT

Cannot easily override.

```dockerfile
ENTRYPOINT ["nginx"]
```

Always starts nginx.

---

## Interview Answer

ENTRYPOINT defines the main executable while CMD provides default arguments.

---

# Building Images

## Build

```bash
docker build -t myapp:v1 .
```

---

## List Images

```bash
docker images
```

---

## Remove Image

```bash
docker rmi image-id
```

---

# Running Containers

## Run Container

```bash
docker run nginx
```

---

## Detached Mode

```bash
docker run -d nginx
```

---

## Port Mapping

```bash
docker run -p 8080:80 nginx
```

Meaning:

```text
Host 8080
     ↓
Container 80
```

---

## Name Container

```bash
docker run --name web nginx
```

---

# Container Management

## Running Containers

```bash
docker ps
```

---

## All Containers

```bash
docker ps -a
```

---

## Stop Container

```bash
docker stop container-id
```

---

## Start Container

```bash
docker start container-id
```

---

## Remove Container

```bash
docker rm container-id
```

---

# Container Logs

## View Logs

```bash
docker logs container-id
```

---

## Follow Logs

```bash
docker logs -f container-id
```

---

# Enter Container

## Interactive Shell

```bash
docker exec -it container-id bash
```

or

```bash
docker exec -it container-id sh
```

---

# Docker Volumes

## Why Volumes?

Container storage is temporary.

Volumes provide persistence.

---

## Create Volume

```bash
docker volume create data-vol
```

---

## Mount Volume

```bash
docker run -v data-vol:/data nginx
```

---

# Bind Mount vs Volume

## Bind Mount

```bash
docker run -v /host:/container
```

Uses host filesystem.

---

## Volume

```bash
docker run -v data-vol:/container
```

Managed by Docker.

---

## Interview Answer

Volumes are preferred for production because Docker manages them and they are portable.

---

# Docker Networking

## Types of Networks

### Bridge

Default.

```bash
docker network ls
```

Most common.

---

### Host

Uses host network directly.

```bash
docker run --network host
```

---

### None

No network access.

```bash
docker run --network none
```

---

### Overlay

Used in Docker Swarm.

---

# Check Networks

```bash
docker network ls
```

---

# Multi-Stage Builds

## Problem

Build image becomes huge.

Example:

```text
Java
Maven
Source
Jar
```

All remain in image.

---

## Solution

Multi-stage build.

```dockerfile
FROM maven:3.9 AS builder

COPY . .

RUN mvn package

FROM openjdk:17

COPY --from=builder target/app.jar .

ENTRYPOINT ["java","-jar","app.jar"]
```

---

## Benefits

- Smaller image
- Better security
- Faster deployment

---

## Interview Question

When should multi-stage builds be used?

Answer:

When building Java, Go, NodeJS, .NET applications where build dependencies should not exist in final runtime image.

---

# Docker Compose

## What is Docker Compose?

Tool for running multi-container applications.

---

Example:

```yaml
version: "3"

services:
  web:
    image: nginx

  db:
    image: mysql
```

---

## Start

```bash
docker-compose up -d
```

---

## Stop

```bash
docker-compose down
```

---

# Docker Security

## Best Practices

### Use Small Base Images

Good:

```dockerfile
FROM alpine
```

Bad:

```dockerfile
FROM ubuntu
```

---

### Run Non-Root User

```dockerfile
USER appuser
```

---

### Scan Images

Tools:

- Trivy
- Clair
- Snyk
- Docker Scout

---

### Remove Secrets

Never:

```dockerfile
ENV PASSWORD=admin123
```

Use:

- Vault
- Secrets Manager
- Kubernetes Secrets

---

# Docker Troubleshooting

## ImagePull Error

Check:

```bash
docker pull image
```

Possible causes:

- Wrong image name
- Authentication issue
- Registry unavailable

---

## Container Exits Immediately

Check:

```bash
docker logs container-id
```

Common reasons:

- Application crash
- Missing environment variable
- Incorrect entrypoint

---

## Container High CPU

Check:

```bash
docker stats
```

---

## Disk Full

Check:

```bash
docker system df
```

Cleanup:

```bash
docker system prune -a
```

---

# Common Interview Scenarios

## Application Not Accessible

Check:

1. Container running?

```bash
docker ps
```

2. Port mapping correct?

```bash
docker ps
```

3. Logs

```bash
docker logs
```

4. Firewall

5. Network

---

## Container Keeps Restarting

Check:

```bash
docker logs
```

Verify:

- App startup
- Memory
- Environment variables
- Dependencies

---

# Oracle DevOps Answer

### How did you use Docker?

Answer:

Docker was used to package microservices into portable images. Images were built through GitLab CI/CD pipelines and deployed into Kubernetes environments. Security scanning and deployment validations were part of the release process before production promotion.

---

# Most Asked Interview Questions

1. What is Docker?
2. Docker Architecture?
3. Image vs Container?
4. VM vs Docker?
5. Dockerfile?
6. COPY vs ADD?
7. CMD vs ENTRYPOINT?
8. Volume vs Bind Mount?
9. Docker Networking Types?
10. Multi-stage Build?
11. Docker Compose?
12. Port Mapping?
13. How do containers communicate?
14. How do you debug a container?
15. ImagePullBackOff equivalent causes?
16. Why containers restart?
17. How do you reduce image size?
18. Docker security best practices?
19. How do you scan images?
20. How does Docker fit into CI/CD?

---

# Docker Cheat Sheet

```bash
docker build -t app:v1 .
docker images
docker pull nginx
docker run nginx
docker run -d nginx
docker run -p 8080:80 nginx
docker ps
docker ps -a
docker logs container
docker exec -it container bash
docker stop container
docker start container
docker rm container
docker rmi image
docker volume ls
docker network ls
docker stats
docker system prune -a
docker-compose up -d
docker-compose down
```
