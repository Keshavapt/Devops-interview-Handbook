# Docker Interview Handbook

> **Target Audience:** DevOps Engineers (2–8 Years Experience)
> **Difficulty:** Intermediate → Advanced
> **Interview Weightage:** ⭐⭐⭐⭐⭐

---

## Table of Contents

### Core Concepts
- [1. What is Docker?](#1-what-is-docker)
- [2. Why Docker?](#2-why-docker)
- [3. Virtualization vs Containerization](#3-virtualization-vs-containerization)
- [4. Docker Architecture](#4-docker-architecture)
- [5. Docker Image vs Container](#5-docker-image-vs-container)

### Images, Layers & Storage
- [6. Docker Images, Layers & Storage](#6-docker-images-layers--storage)
- [7. Dockerfile](#7-dockerfile)
- [8. Dockerfile Instructions](#8-dockerfile-instructions)
- [9. Multi-Stage Builds](#9-multi-stage-builds)

### Operations
- [10. Docker Commands](#10-docker-commands)
- [11. Docker Volumes](#11-docker-volumes)
- [12. Docker Networking](#12-docker-networking)
- [13. Docker Compose](#13-docker-compose)
- [14. Docker Registry](#14-docker-registry)

### Security & Troubleshooting
- [15. Docker Security](#15-docker-security)
- [16. Docker Troubleshooting](#16-docker-troubleshooting)
- [17. Common Interview Scenarios](#17-common-interview-scenarios)

### CI/CD Integration
- [18. Docker in CI/CD Pipeline](#18-docker-in-cicd-pipeline)

### Interview Questions & Answers
- [19. Core Docker Interview Questions](#19-core-docker-interview-questions)
- [20. Advanced Docker Interview Questions](#20-advanced-docker-interview-questions)
- [21. Scenario-Based Questions](#21-scenario-based-questions)
- [22. Oracle DevOps Answer](#22-oracle-devops-answer)

---

## 1. What is Docker?

Docker is an open-source containerization platform that packages applications along with their dependencies, libraries, runtime, and configuration into a lightweight, portable unit called a **Container**.

### Key Characteristics

- Open Source
- Lightweight
- Portable
- Fast
- Isolated
- Consistent
- Platform Independent

### Benefits

- Portability
- Consistency
- Faster deployment
- Resource efficiency
- Scalability
- Easy rollback
- Version-controlled deployments

### Real-world Example

A Java application requires Java 21, Maven, Spring Boot, and Linux packages. Instead of installing everything manually on every server, Docker packages all required components into one Docker Image that can be deployed anywhere.

---

## 2. Why Docker?

### Problems Before Docker

| Problem | Description |
| ------- | ----------- |
| Environment Drift | Dev and Production environments differ (different OS, Java, Python versions) |
| Dependency Conflicts | App A needs Python 3.9, App B needs Python 3.12 — conflicts on same server |
| Complex Deployments | Manual install of OS packages, runtimes, dependencies, config on every server |
| "Works on my machine" | App works on dev machine but fails in QA/Production |

### What Docker Solves

- **Environment Consistency** — Same image runs identically in Dev, QA, UAT, Production
- **Dependency Isolation** — Each container has its own runtime, libraries, config
- **Portability** — Build once, run anywhere (local, VM, AWS, Azure, GCP, Kubernetes)
- **Faster Deployment** — Containers start in seconds (share host kernel)
- **Easy Rollback** — Deploy an earlier image version
- **Scalability** — Create multiple containers from one image

---

## 3. Virtualization vs Containerization

### Virtual Machines

```text
Hardware
 └─ Hypervisor
     ├─ VM1 (OS + App)
     ├─ VM2 (OS + App)
     └─ VM3 (OS + App)
```

Characteristics: Heavy, Full OS per VM, More resources, Minutes to boot

### Containers

```text
Hardware
 └─ Host OS
     └─ Docker Engine
         ├─ Container1
         ├─ Container2
         └─ Container3
```

Characteristics: Lightweight, Share Host Kernel, Faster startup, Seconds to boot

### VM vs Docker Comparison

| Feature      | Virtual Machine   | Docker Container   |
| ------------ | ----------------- | ------------------ |
| Virtualizes  | Hardware          | Operating System   |
| OS           | Separate Guest OS | Shared Host Kernel |
| Startup Time | Minutes           | Seconds            |
| Memory Usage | High              | Low                |
| Performance  | Lower             | Higher             |
| Image Size   | GBs               | MBs                |
| Isolation    | Strong            | Moderate           |
| Portability  | Good              | Excellent          |

### When to Choose Which

| Choose Docker When | Choose VMs When |
| ------------------ | --------------- |
| Building Microservices | Multiple OS required |
| CI/CD Pipelines | Legacy apps need complete OS |
| Cloud-native apps | Kernel-level customization |
| Running APIs/Web apps | Strong isolation mandatory |
| Scaling services | |

---

## 4. Docker Architecture

Docker follows a **Client-Server Architecture** where the Docker Client communicates with the Docker Daemon using REST APIs.

```text
Docker Client
      |
      | REST API
      v
Docker Daemon (dockerd)
      |
      +-- Docker Image
      +-- Docker Volume
      +-- Docker Network
      +-- Containers
      |
Docker Registry (Docker Hub / ECR / ACR)
```

### Components

| Component | Purpose |
| --------- | ------- |
| Docker Client | Sends commands to the daemon via CLI/API |
| Docker Daemon (dockerd) | Background service that manages containers, images, networks, volumes |
| Docker Engine | Complete runtime: Client + Daemon + REST API |
| Docker Registry | Stores and distributes images |
| Docker Image | Read-only blueprint for containers |
| Docker Container | Running instance of an image |

### What Happens When You Run `docker run nginx`

1. Docker Client receives the command
2. Sends request to Docker Daemon via REST API
3. Daemon checks if image exists locally
4. Pulls image from registry if missing
5. Creates a writable container layer
6. Configures namespaces and cgroups
7. Sets up networking and volumes
8. Executes ENTRYPOINT/CMD
9. Container starts

### Container Lifecycle

```
Created → Running → Paused → Stopped → Restarted → Removed
```

---

## 5. Docker Image vs Container

| Image     | Container        |
| --------- | ---------------- |
| Blueprint | Running instance |
| Read-only | Read-write       |
| Immutable | Temporary        |
| Stored in Registry | Runs on Docker Host |
| Can create many containers | Created from one image |

**Analogy:** Image is like a class; Container is like an object.

---

## 6. Docker Images, Layers & Storage

### What are Docker Layers?

Every Dockerfile instruction creates a new read-only layer.

```dockerfile
FROM ubuntu:22.04          # Layer 1
RUN apt-get update         # Layer 2
RUN apt-get install nginx  # Layer 3
COPY index.html /usr/share/nginx/html/  # Layer 4
CMD ["nginx","-g","daemon off;"]        # Layer 5
```

### Layer Caching

Docker caches each layer independently. If only the application code changes, Docker reuses all previous layers and rebuilds only the changed layer.

**Good Dockerfile Ordering:**
```dockerfile
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
```

**Bad Dockerfile Ordering:**
```dockerfile
COPY . .
RUN pip install -r requirements.txt
```

### Copy-on-Write (CoW)

All containers initially share the same read-only image layers. When a container modifies a file, Docker copies only that file into the container's writable layer.

**Benefits:** Saves disk space, faster startup, better memory utilization

### OverlayFS

The default storage driver on Linux. It merges multiple read-only layers with a writable layer into a single unified filesystem.

### Storage Drivers

| Driver | Status |
| ------ | ------ |
| overlay2 | Recommended (default on Linux) |
| fuse-overlayfs | Alternative |
| aufs | Deprecated |
| btrfs | Available |
| zfs | Available |
| devicemapper | Legacy |

### Image Optimization Best Practices

- Use small base images (Alpine, debian:slim, distroless)
- Use multi-stage builds
- Remove temporary files (`rm -rf /var/lib/apt/lists/*`)
- Minimize layers (combine RUN instructions)
- Use `.dockerignore` to exclude unnecessary files
- Copy dependency files before application code

---

## 7. Dockerfile

A Dockerfile is a text file containing instructions to build a Docker image.

```dockerfile
FROM openjdk:17
WORKDIR /app
COPY target/app.jar .
EXPOSE 8080
ENTRYPOINT ["java","-jar","app.jar"]
```

Build command:
```bash
docker build -t myapp:v1 .
```

---

## 8. Dockerfile Instructions

| Instruction | Purpose | Example |
| ----------- | ------- | ------- |
| `FROM` | Base image | `FROM nginx` |
| `WORKDIR` | Working directory | `WORKDIR /app` |
| `COPY` | Copy local files | `COPY app.jar .` |
| `ADD` | Copy files + URL extraction | `ADD archive.tar.gz /app` |
| `RUN` | Execute command during build | `RUN apt-get update` |
| `CMD` | Default command (overridable) | `CMD ["java","-jar","app.jar"]` |
| `ENTRYPOINT` | Fixed executable (not easily overridden) | `ENTRYPOINT ["java","-jar","app.jar"]` |
| `EXPOSE` | Document port (does NOT publish) | `EXPOSE 8080` |
| `ENV` | Environment variable (runtime) | `ENV JAVA_HOME=/usr/lib/java` |
| `ARG` | Build-time variable (not available at runtime) | `ARG VERSION=1.0` |
| `USER` | Run as non-root user | `USER appuser` |

### COPY vs ADD

| COPY | ADD |
| ---- | --- |
| File copy only | File copy + extract archives |
| Preferred | Less preferred |
| Predictable | Extra functionality (URL fetch) |

### CMD vs ENTRYPOINT

| Instruction | Overridable | Behavior |
| ----------- | ----------- | -------- |
| `CMD` | Yes | Can be overridden by `docker run image bash` |
| `ENTRYPOINT` | No | Cannot easily override; always starts |

### ARG vs ENV

| ARG | ENV |
| --- | --- |
| Build-time only | Runtime |
| Not available after container starts | Available in running container |

### EXPOSE vs -p

| EXPOSE | -p |
| ------ | -- |
| Documentation only | Publishes port |
| Does NOT expose port | Maps host:container port |

---

## 9. Multi-Stage Builds

### Problem

Build image becomes huge because build dependencies (Maven, Gradle, source code) remain in the final image.

### Solution

```dockerfile
FROM maven:3.9 AS builder
COPY . .
RUN mvn package

FROM openjdk:17
COPY --from=builder target/app.jar .
ENTRYPOINT ["java","-jar","app.jar"]
```

### Benefits

- Smaller image
- Better security (fewer packages = smaller attack surface)
- Faster deployment
- Build dependencies not in runtime image

### When to Use

When building Java, Go, NodeJS, .NET applications where build dependencies should not exist in the final runtime image.

---

## 10. Docker Commands

### Building Images

| Command | Description |
| ------- | ----------- |
| `docker build -t myapp:v1 .` | Build image from Dockerfile |
| `docker images` | List images |
| `docker image ls` | List images (object syntax) |
| `docker rmi image-id` | Remove an image |
| `docker image prune` | Remove unused images |
| `docker history nginx` | View image layers |
| `docker image inspect nginx` | View detailed image info (JSON) |

### Running Containers

| Command | Description |
| ------- | ----------- |
| `docker run nginx` | Run container from image |
| `docker run -d nginx` | Run in detached mode |
| `docker run -p 8080:80 nginx` | Map host port 8080 → container port 80 |
| `docker run --name web nginx` | Run with custom name |
| `docker run -it ubuntu bash` | Interactive terminal |
| `docker run --rm nginx` | Remove container after exit |
| `docker run -e ENV=value nginx` | Set environment variable |
| `docker run --memory="512m" --cpus="1.5" nginx` | Resource limits |
| `docker run --read-only nginx` | Read-only filesystem |
| `docker run --cap-drop ALL nginx` | Drop all Linux capabilities |

### Container Management

| Command | Description |
| ------- | ----------- |
| `docker ps` | List running containers |
| `docker ps -a` | List all containers |
| `docker stop container-id` | Stop a container (graceful) |
| `docker kill container-id` | Force kill a container |
| `docker start container-id` | Start a stopped container |
| `docker restart container-id` | Restart a container |
| `docker pause container-id` | Pause a container |
| `docker unpause container-id` | Resume a paused container |
| `docker rm container-id` | Remove a container |
| `docker create nginx` | Create container without starting |
| `docker rename old new` | Rename a container |
| `docker commit web myimage:v2` | Create image from container (not recommended) |

### Logs & Debugging

| Command | Description |
| ------- | ----------- |
| `docker logs container-id` | View container logs |
| `docker logs -f container-id` | Follow logs |
| `docker logs --tail 100 container-id` | Last 100 lines |
| `docker exec -it container-id bash` | Enter container with bash |
| `docker exec -it container-id sh` | Enter container with sh |
| `docker top container-id` | View running processes |
| `docker inspect container-id` | Detailed config/state (JSON) |
| `docker stats` | Real-time resource usage |
| `docker events` | Stream real-time daemon events |
| `docker cp file.txt web:/tmp/` | Copy host → container |
| `docker cp web:/var/log/app.log .` | Copy container → host |

### System Commands

| Command | Description |
| ------- | ----------- |
| `docker system df` | Disk usage |
| `docker system prune` | Remove stopped containers, unused networks, dangling images, build cache |
| `docker system prune -a` | Remove ALL unused images (not just dangling) |
| `docker version` | Client and server versions |
| `docker info` | System-wide Docker info |
| `docker --help` | Docker help |

### Common Command Options

| Option | Description |
| ------ | ----------- |
| `-d` | Detached mode |
| `-it` | Interactive terminal |
| `--rm` | Remove container after exit |
| `--name` | Container name |
| `-p` | Port mapping |
| `-v` | Mount volume |
| `-e` | Environment variable |
| `--network` | Select network |
| `--restart` | Restart policy |
| `--cpus` | CPU limit |
| `--memory` | Memory limit |
| `--read-only` | Read-only filesystem |
| `--cap-drop` | Drop Linux capabilities |

---

## 11. Docker Volumes

### Why Volumes?

Container storage is temporary. When a container dies, data disappears. Volumes provide persistence.

### Types

| Type | Command | Description |
| ---- | ------- | ----------- |
| Named Volume | `docker volume create data-vol` | Managed by Docker |
| Bind Mount | `docker run -v /host:/container nginx` | Uses host filesystem |
| Volume Mount | `docker run -v data-vol:/container nginx` | Managed by Docker |

### Bind Mount vs Volume

| Bind Mount | Volume |
| ---------- | ------ |
| Uses host filesystem | Managed by Docker |
| Host directory | Docker-managed storage |
| Less portable | Portable |
| Good for dev | Preferred for production |

**Interview Answer:** Volumes are preferred for production because Docker manages them and they are portable.

---

## 12. Docker Networking

### Network Types

| Network | Description |
| ------- | ----------- |
| Bridge | Default network, most common |
| Host | Uses host network directly |
| None | No network access |
| Overlay | Used in Docker Swarm (multi-host) |
| Macvlan | Assign MAC addresses to containers |

### Commands

| Command | Description |
| ------- | ----------- |
| `docker network ls` | List networks |
| `docker network create my-net` | Create custom bridge network |
| `docker run --network host nginx` | Use host network |
| `docker run --network none nginx` | No network |
| `docker network inspect my-net` | View network details |

### Container Communication

Containers communicate through:
- **Bridge Network:** Same network → use container names or IPs
- **Host Network:** Share host's network stack
- **Overlay Network:** Across multiple Docker hosts
- **Port Mapping:** Expose container ports to host with `-p host:container`

---

## 13. Docker Compose

### What is Docker Compose?

Tool for running multi-container applications defined in a YAML file.

```yaml
version: "3"

services:
  web:
    image: nginx
    ports:
      - "8080:80"

  db:
    image: mysql
    environment:
      MYSQL_ROOT_PASSWORD: secret
```

### Commands

| Command | Description |
| ------- | ----------- |
| `docker-compose up -d` | Start services in detached mode |
| `docker-compose up --build` | Build images and start services |
| `docker-compose down` | Stop and remove containers, networks |
| `docker-compose down -v` | Also remove volumes (data loss!) |
| `docker-compose down --rmi all` | Also remove all images |
| `docker-compose stop` | Stop containers (keep them) |
| `docker-compose start` | Start existing containers |
| `docker-compose restart` | Restart containers |
| `docker-compose ps` | List service status |
| `docker-compose logs` | View service logs |
| `docker-compose exec web bash` | Run command in service container |
| `docker-compose run web cmd` | Run one-off command |
| `docker-compose build` | Build service images |
| `docker-compose pull` | Pull service images |
| `docker-compose push` | Push service images |
| `docker-compose config` | Validate and show resolved config |
| `docker-compose scale web=3` | Scale service replicas |

### Key Differences

| Command | Behavior |
| ------- | -------- |
| `docker-compose up` | Creates and starts containers |
| `docker-compose start` | Starts existing stopped containers |
| `docker-compose down` | Stops and removes containers, networks |
| `docker-compose stop` | Stops containers but keeps them |
| `docker-compose down -v` | Also removes volumes (data loss) |
| `docker-compose down --rmi all` | Also removes all images |

---

## 14. Docker Registry

### What is a Registry?

A Docker Registry stores Docker Images. Images can be downloaded, uploaded, shared, and version-controlled.

### Popular Registries

| Public | Private |
| ------ | ------- |
| Docker Hub | AWS ECR |
| | Azure ACR |
| | Google Artifact Registry |
| | GitLab Container Registry |
| | Harbor |

### Workflow

```
docker build → docker push → Registry → docker pull → docker run
```

### Image Tagging & Versioning

- Always use versioned tags (`v1.2.3`, `2026.07.24`, Git commit SHA)
- Never use `latest` in production (mutable, unpredictable)
- Tag images with meaningful version identifiers

---

## 15. Docker Security

### Why Security Matters

A Docker container is **not a virtual machine**. Containers share the host OS kernel, so a compromised container can impact the host if proper controls are not in place.

### Security Best Practices

| Practice | Description |
| -------- | ----------- |
| Use Official Images | Trusted, maintained, regularly scanned |
| Use Minimal Base Images | Alpine, debian:slim, distroless — smaller attack surface |
| Never Run as Root | Use `USER appuser` — least privilege |
| Scan Images | Trivy, Docker Scout, Grype, Snyk |
| Keep Images Updated | Rebuild regularly with security patches |
| Read-only Filesystems | `docker run --read-only nginx` |
| Limit Capabilities | `docker run --cap-drop ALL` |
| Resource Limits | `--memory="512m" --cpus="1.5"` |
| Use Secrets Management | Docker Secrets, K8s Secrets, AWS Secrets Manager, Vault |
| Sign Images | Docker Content Trust, Cosign |

### Secrets Management

❌ Never:
```dockerfile
ENV DB_PASSWORD=admin123
```

✔ Use:
- Docker Secrets
- Kubernetes Secrets
- AWS Secrets Manager
- HashiCorp Vault

### Image Scanning Tools

- Trivy
- Docker Scout
- Grype
- Snyk
- Clair

---
## 16. Senior Production Internals

Exit Code 137 (SIGKILL/OOMKilled) vs 143 (SIGTERM):
- Exit Code 137: Kernel OOM Kill - the kernel kills the process when memory is exhausted
- Exit Code 143: SIGTERM - graceful shutdown signal; the process should handle this gracefully
- Always use exec form ENTRYPOINT ["java", "-jar", "app.jar"] or tini so SIGTERM reaches the app instead of hanging PID 1 /bin/sh.
- Always handle SIGTERM gracefully in your application main loop.

Distroless Debugging:
- Distroless images lack /bin/sh and standard debugging tools
- Use: `kubectl debug pod/<pod-name> -it --image=busybox:1.36 --target=<container-name>`
- Or: `docker run --pid container:<id> --network container:<id> nicolaka/netshoot`
- Without /bin/sh, use /proc filesystem, /sys filesystem, and kernel logs for debugging

BuildKit Secret Mounts:
- Use `RUN --mount=type=secret,id=npmrc,target=/root/.npmrc` instead of ARG/ENV
- Build-time secrets never land in the final image layer
- The --mount=type=tmpfs approach also works for runtime-only secrets

Non-Root Volume Permissions:
- Set `securityContext.fsGroup: 1001` in Kubernetes Pod specs when mounting volumes for non-root USER 1001 containers
- For containers running as USER 1001, set `securityContext.runAsNonRoot: true` and `securityContext.runAsUser: 1001`
- Ensure volume permissions allow access for GROUP 1001 without root privileges

---
## 16. Docker Troubleshooting
---

## 16. Docker Troubleshooting

### ImagePull Error

Possible causes:
- Wrong image name
- Authentication issue
- Registry unavailable

### Container Exits Immediately

Common reasons:
- Application crash
- Missing environment variable
- Incorrect entrypoint

### Container High CPU

Check with `docker stats`.

### Disk Full

Check with `docker system df` and clean up with `docker system prune -a`.

### Debugging Steps

1. Check logs: `docker logs container-id`
2. Inspect container: `docker inspect container-id`
3. Enter container: `docker exec -it container-id bash`
4. Check resource usage: `docker stats`
5. Check processes: `docker top container-id`
6. Check events: `docker events`

---

## 17. Common Interview Scenarios

### Application Not Accessible

Check:
- Container running? → `docker ps`
- Port mapping correct? → `docker ps`
- Logs → `docker logs`
- Firewall
- Network

### Container Keeps Restarting

Check:
- Logs → `docker logs`
- App startup
- Memory
- Environment variables
- Dependencies

---

## 18. Docker in CI/CD Pipeline

### Production Pipeline Flow

```
Developer → Git Push → GitLab CI → Build → Unit Test → SonarQube
→ Security Scan → Docker Build → Trivy Scan → Push to ECR
→ Deploy Dev → Integration Test → Deploy QA → Approval
→ Deploy Production → Smoke Test → Monitoring
```

### Docker's Role in CI/CD

- **Build Stage:** Package application into Docker image
- **Scan Stage:** Trivy/Docker Scout scan for vulnerabilities
- **Push Stage:** Store versioned images in ECR/registry
- **Deploy Stage:** Pull image and deploy to Kubernetes
- **Rollback:** Deploy earlier image version

### Best Practices in CI/CD

- Multi-stage builds
- Small base images
- Versioned image tags
- Non-root user
- Image scanning before push
- Never deploy images with unresolved critical vulnerabilities

---

## 19. Core Docker Interview Questions

### Q1. What is Docker?

**Answer:**
Docker is an open-source containerization platform that packages applications along with their dependencies into lightweight containers, ensuring consistent execution across different environments.

### Q2. Why are Docker containers lightweight?

**Answer:**
Containers share the host operating system kernel instead of running a separate guest operating system, making them consume less CPU, memory, and storage.

### Q3. What is the difference between Docker Image and Container?

**Answer:**
An image is an immutable, read-only blueprint stored in a registry. A container is a running instance of an image with a writable layer. One image can create many containers.

### Q4. What is the difference between VM and Docker?

**Answer:**
A VM virtualizes hardware and includes a complete guest OS. Docker virtualizes the OS, shares the host kernel, starts in seconds, and uses significantly fewer resources.

### Q5. Explain Docker Architecture.

**Answer:**
Docker follows a Client-Server Architecture. The Docker Client sends REST API requests to the Docker Daemon (dockerd), which manages images, containers, networks, and volumes. Images are stored in registries like Docker Hub or AWS ECR.

### Q6. What happens internally when you execute `docker run nginx`?

**Answer:**
1. Client receives command → 2. Sends request to daemon → 3. Checks local image → 4. Pulls image if missing → 5. Creates writable layer → 6. Configures namespaces/cgroups → 7. Sets up networking/volumes → 8. Executes ENTRYPOINT/CMD → 9. Container starts.

### Q7. What is the difference between CMD and ENTRYPOINT?

**Answer:**
ENTRYPOINT defines the main executable that cannot be easily overridden. CMD provides default arguments that can be overridden by `docker run image bash`. Typically: `ENTRYPOINT java` + `CMD app.jar`.

### Q8. What is the difference between COPY and ADD?

**Answer:**
COPY only copies files. ADD can copy files, extract archives, and fetch URLs. COPY is preferred because it's more predictable and transparent.

### Q9. What is the difference between ARG and ENV?

**Answer:**
ARG is a build-time variable, not available after the container starts. ENV is a runtime environment variable available in the running container.

### Q10. What is the difference between EXPOSE and -p?

**Answer:**
EXPOSE only documents the port in the Dockerfile. `-p` actually publishes the port, mapping host port to container port.

### Q11. Why use multi-stage builds?

**Answer:**
To keep the final runtime image small by excluding build dependencies (Maven, Gradle, source code). Only the compiled artifact is copied to the final image.

### Q12. What are Docker Layers?

**Answer:**
Each Dockerfile instruction creates a read-only layer. Docker caches unchanged layers and rebuilds only modified layers, making builds faster. Containers share these layers via Copy-on-Write.

### Q13. What is Copy-on-Write (CoW)?

**Answer:**
A storage optimization where containers initially share the same read-only image layers. A file is copied into a container's writable layer only when that container modifies it.

### Q14. What is OverlayFS?

**Answer:**
The default storage driver on Linux. It merges multiple read-only image layers with a writable container layer into a single unified filesystem.

### Q15. Why should Dockerfiles copy dependency files before application code?

**Answer:**
To maximize Docker layer caching. When only application code changes, Docker reuses the cached dependency installation layer, significantly reducing build time.

### Q16. How can you reduce Docker image size?

**Answer:**
- Use minimal base images (Alpine, Slim)
- Use multi-stage builds
- Remove temporary files
- Combine RUN instructions
- Use `.dockerignore`
- Install only required packages

### Q17. What is the difference between `docker run`, `docker start`, and `docker create`?

**Answer:**
- `docker create` creates a container but does not start it
- `docker start` starts an existing stopped container
- `docker run` creates a new container and immediately starts it

### Q18. What is the difference between `docker stop` and `docker kill`?

**Answer:**
`docker stop` sends SIGTERM (graceful shutdown), waits for timeout, then SIGKILL. `docker kill` sends SIGKILL immediately (force kill).

### Q19. What is the difference between `docker rm` and `docker rmi`?

**Answer:**
`docker rm` removes a container. `docker rmi` removes an image.

### Q20. What is the difference between `docker build` and `docker commit`?

**Answer:**
`docker build` builds an image from a Dockerfile (reproducible). `docker commit` creates an image from a running container's current state (not reproducible, not recommended for production).

### Q21. What is the difference between `docker save` and `docker export`?

**Answer:**
`docker save` saves an image (with layers and metadata) to a tar file. `docker export` exports a container's filesystem (without layers/metadata).

### Q22. What is the difference between `docker exec` and `docker attach`?

**Answer:**
`docker exec` runs a new command in a running container. `docker attach` attaches to the container's main process (stdin/stdout/stderr).

### Q23. Why is using the `latest` tag discouraged in production?

**Answer:**
The `latest` tag is mutable and can point to different image versions over time, making deployments unpredictable. Use immutable, versioned tags (e.g., `v1.2.3` or Git commit SHA).

### Q24. Why is `docker commit` not considered a best practice?

**Answer:**
It creates images from manually modified containers, making builds non-reproducible. Always use Dockerfiles and automated CI/CD pipelines.

### Q25. What is the difference between `docker system prune` and `docker system prune -a`?

**Answer:**
`docker system prune` removes stopped containers, unused networks, dangling images, and build cache. `docker system prune -a` also removes ALL unused images, including tagged images not in use.

---

## 20. Advanced Docker Interview Questions

### Q1. How do containers communicate with each other?

**Answer:**
Containers communicate through:
- **Bridge Network:** Same network → use container names or IPs
- **Host Network:** Share host's network stack
- **Overlay Network:** Across multiple Docker hosts (Swarm)
- **Port Mapping:** Expose container ports to host
- **Service Discovery:** Consul, etcd, Docker Swarm DNS
- **Environment Variables:** Pass service URLs/ports
- **Shared Volumes:** Exchange data through mounted volumes

### Q2. How do you debug a running container?

**Answer:**
1. Check logs: `docker logs container-id`
2. Inspect container: `docker inspect container-id`
3. Enter container: `docker exec -it container-id bash`
4. Check resource usage: `docker stats`
5. Check processes: `docker top container-id`
6. Check events: `docker events`

### Q3. What is the difference between `docker-compose up` and `docker-compose start`?

**Answer:**
`docker-compose up` creates and starts containers (also builds images if needed). `docker-compose start` starts existing containers that were stopped.

### Q4. What is the difference between `docker-compose down` and `docker-compose stop`?

**Answer:**
`docker-compose down` stops and removes containers, networks, and volumes. `docker-compose stop` stops containers but keeps them (can restart with `start`).

### Q5. What is the difference between `docker-compose down` and `docker-compose down -v`?

**Answer:**
`docker-compose down` stops and removes containers and networks. `docker-compose down -v` also removes volumes (data loss!).

### Q6. What is the difference between `docker-compose exec` and `docker-compose run`?

**Answer:**
`docker-compose exec` runs a command in a running service container. `docker-compose run` runs a one-off command in a new container.


**Answer:**
1. Use multi-stage builds (Maven builder → JRE runtime)
2. Use minimal base image (eclipse-temurin:17-jre-alpine)
3. Use `.dockerignore` to exclude target/, .git, etc.
4. Combine RUN instructions
5. Remove temporary files

### Q8. How do you handle secrets in Docker?

**Answer:**
Never hardcode secrets in Dockerfiles or images. Use:
- Docker Secrets
- Kubernetes Secrets
- AWS Secrets Manager
- HashiCorp Vault
- Environment variables injected at runtime

### Q9. How do you ensure Docker security in production?

**Answer:**
- Use official/minimal base images
- Run as non-root user
- Scan images with Trivy/Docker Scout
- Use read-only filesystems
- Limit Linux capabilities
- Set resource limits
- Use signed images
- Store secrets securely
- Keep images updated

### Q10. How does Docker fit into your CI/CD pipeline?

**Answer:**
Docker is used to package microservices into portable images. Images are built through GitLab CI/CD pipelines, scanned for vulnerabilities, pushed to a registry (ECR), and deployed into Kubernetes environments. Security scanning and deployment validations are part of the release process before production promotion.

---

## 22. Oracle DevOps Answer

### How did you use Docker?

**Answer:**
Docker was used to package microservices into portable images. Images were built through GitLab CI/CD pipelines and deployed into Kubernetes environments. Security scanning and deployment validations were part of the release process before production promotion.

---

## Quick Revision

| Topic | Key Point |
| ----- | --------- |
| Docker | Containerization Platform |
| Image | Read-only Blueprint |
| Container | Running Instance of Image |
| Layer | Read-only Filesystem Layer |
| Writable Layer | Stores Runtime Changes |
| Copy-on-Write | Copies Files Only When Modified |
| OverlayFS | Merges Layers into One View |
| Bridge | Default Network |
| Volume | Persistent Storage |
| Compose | Multi-container |
| Multi-stage | Smaller Images |
| Trivy | Security Scanner |
| CMD | Default Command (overridable) |
| ENTRYPOINT | Main Process (fixed) |
| COPY > ADD | Prefer COPY |
| ENV | Runtime Variable |
| ARG | Build-time Variable |
| EXPOSE | Documentation only |
| `-p` | Publish Port |
| `docker logs` | View Logs |
| `docker exec` | Enter Container |
| `docker stats` | Resource Usage |
| `docker system prune -a` | Cleanup Everything |