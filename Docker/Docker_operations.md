# Docker Operations

> **Target Audience:** DevOps Engineers (1–8 Years Experience)
> **Difficulty:** Beginner → Advanced
> **Interview Weightage:** ⭐⭐⭐⭐⭐

---

# Table of Contents

1. Docker CLI Commands
2. Docker Networking
3. Docker Volumes
4. Docker Compose
5. Docker Registry
6. Image Tagging & Versioning
7. Docker Hub vs Private Registry
8. AWS ECR
9. GitLab Container Registry
10. Best Practices
11. Interview Questions

---

# 1. Docker CLI Commands

## Introduction

Docker provides a command-line interface (CLI) to interact with Docker Engine. These commands allow you to build images, run containers, inspect resources, manage networks, and troubleshoot running applications.

---

# Docker Command Structure

```bash
docker <object> <command> [options]
```

Examples:

```bash
docker image ls
docker container ls
docker volume ls
docker network ls
```

Older commands are still supported:

```bash
docker images
docker ps
```

However, Docker recommends using the object-oriented syntax.

---

# Docker Help

Display Docker help:

```bash
docker --help
```

Help for a specific object:

```bash
docker image --help
docker container --help
docker network --help
```

---

# Docker Version

Display Docker Client and Server versions:

```bash
docker version
```

Sample Output:

```
Client:
 Version:           28.x

Server:
 Engine:
  Version:          28.x
```

---

# Docker Information

Display system-wide Docker information:

```bash
docker info
```

Useful details include:

- Docker Root Directory
- Storage Driver
- Cgroup Version
- Number of Images
- Number of Containers
- CPU Count
- Total Memory
- Logging Driver
- Registry Configuration

---

# Working with Images

## List Images

```bash
docker image ls
```

or

```bash
docker images
```

Example Output:

```
REPOSITORY      TAG      IMAGE ID       SIZE

ubuntu          22.04    a1b2c3d4       78MB

nginx           latest   d5e6f7g8       188MB
```

---

## Pull an Image

Download an image from a registry.

```bash
docker pull nginx
```

Specific version:

```bash
docker pull nginx:1.27
```

---

## Search Images

```bash
docker search redis
```

---

## Remove an Image

```bash
docker image rm nginx
```

Force removal:

```bash
docker image rm -f nginx
```

---

## Remove Unused Images

```bash
docker image prune
```

Remove everything unused:

```bash
docker image prune -a
```

---

## View Image History

```bash
docker history nginx
```

Displays all image layers.

---

## Inspect Image

```bash
docker image inspect nginx
```

Returns detailed JSON information.

---

# Working with Containers

## Run a Container

```bash
docker run nginx
```

---

## Run in Detached Mode

```bash
docker run -d nginx
```

---

## Assign a Name

```bash
docker run --name web nginx
```

---

## Run with Port Mapping

```bash
docker run -d -p 8080:80 nginx
```

Access:

```
http://localhost:8080
```

---

## Interactive Container

```bash
docker run -it ubuntu bash
```

Explanation:

- `-i` → Interactive
- `-t` → Terminal

---

## List Running Containers

```bash
docker ps
```

---

## List All Containers

```bash
docker ps -a
```

---

## Stop Container

```bash
docker stop web
```

---

## Start Container

```bash
docker start web
```

---

## Restart Container

```bash
docker restart web
```

---

## Pause Container

```bash
docker pause web
```

---

## Resume Container

```bash
docker unpause web
```

---

## Remove Container

```bash
docker rm web
```

Force removal:

```bash
docker rm -f web
```

---

# Execute Commands Inside Containers

Open a shell:

```bash
docker exec -it web bash
```

If Bash is unavailable:

```bash
docker exec -it web sh
```

---

## View Running Processes

```bash
docker top web
```

---

## Display Logs

```bash
docker logs web
```

Follow logs:

```bash
docker logs -f web
```

Last 100 lines:

```bash
docker logs --tail 100 web
```

---

## Container Statistics

```bash
docker stats
```

Displays:

- CPU Usage
- Memory Usage
- Network I/O
- Block I/O
- PIDs

---

## Copy Files

Host → Container

```bash
docker cp file.txt web:/tmp/
```

Container → Host

```bash
docker cp web:/var/log/app.log .
```

---

## Rename Container

```bash
docker rename old-name new-name
```

---

## Commit Running Container

```bash
docker commit web myimage:v2
```

> **Note:** This is generally **not recommended** for production. Prefer rebuilding images using a Dockerfile.

---

# Docker System Commands

## Disk Usage

```bash
docker system df
```

---

## Remove Unused Resources

```bash
docker system prune
```

Includes:

- Stopped Containers
- Unused Networks
- Build Cache

---

Remove Everything

```bash
docker system prune -a
```

---

# Common Docker Command Options

| Option      | Description                 |
| ----------- | --------------------------- |
| `-d`        | Detached Mode               |
| `-it`       | Interactive Terminal        |
| `--rm`      | Remove Container After Exit |
| `--name`    | Container Name              |
| `-p`        | Port Mapping                |
| `-v`        | Mount Volume                |
| `-e`        | Environment Variable        |
| `--network` | Select Network              |
| `--restart` | Restart Policy              |
| `--cpus`    | CPU Limit                   |
| `--memory`  | Memory Limit                |

---

# Useful Production Commands

View events:

```bash
docker events
```

Display disk usage:

```bash
docker system df
```

View resource consumption:

```bash
docker stats
```

Inspect container:

```bash
docker inspect web
```

View running processes:

```bash
docker top web
```

---

# Common Mistakes

❌ Using `docker exec` to modify application code directly in production.

✔ Rebuild the image and redeploy.

---

❌ Using `docker commit` for application releases.

✔ Use Dockerfiles and CI/CD pipelines.

---

❌ Running every container with `latest`.

✔ Use versioned tags (`v1.2.3`, `2026.07.24`, etc.).

---

❌ Forgetting to clean up unused images and containers.

✔ Periodically run:

```bash
docker system prune
```

or automate cleanup as part of maintenance.

---

# Quick Revision

| Command               | Purpose                  |
| --------------------- | ------------------------ |
| `docker pull`         | Download Image           |
| `docker run`          | Create & Start Container |
| `docker ps`           | List Running Containers  |
| `docker logs`         | View Logs                |
| `docker exec`         | Execute Commands         |
| `docker stop`         | Stop Container           |
| `docker rm`           | Remove Container         |
| `docker image ls`     | List Images              |
| `docker image rm`     | Remove Image             |
| `docker stats`        | Resource Usage           |
| `docker inspect`      | Detailed Information     |
| `docker system prune` | Cleanup Unused Resources |

---

# Interview Questions

### Q1. What is the difference between `docker run`, `docker start`, and `docker create`?

**Answer:**

- `docker create` creates a container but does not start it.
- `docker start` starts an existing stopped container.
- `docker run` creates a new container and immediately starts it. If the required image is missing locally, Docker first pulls it from the registry.

---

### Q2. What is the purpose of `docker exec`?

**Answer:**

`docker exec` runs a command inside an already running container. It is commonly used for debugging, checking logs, inspecting files, or opening an interactive shell (`bash` or `sh`).

---

### Q3. How do you monitor resource usage of running containers?

**Answer:**

Use:

```bash
docker stats
```

This displays real-time CPU, memory, network I/O, and block I/O usage for each running container.

---

### Q4. Why is using the `latest` tag discouraged in production?

**Answer:**

The `latest` tag is mutable and can point to different image versions over time, making deployments unpredictable. Production environments should use immutable, versioned tags (for example, `v1.2.3` or a Git commit SHA) to ensure reproducible deployments.

---

### Q5. Why is `docker commit` not considered a best practice?

**Answer:**

`docker commit` creates images from manually modified containers, making builds non-reproducible. Production environments should always use Dockerfiles and automated CI/CD pipelines so that images can be rebuilt consistently from source.

# Docker Operations

> **Target Audience:** DevOps Engineers (1–8 Years Experience)
> **Difficulty:** Beginner → Advanced
> **Interview Weightage:** ⭐⭐⭐⭐⭐

---

# Table of Contents

1. Docker CLI Commands
2. Docker Networking
3. Docker Volumes
4. Docker Compose
5. Docker Registry
6. Image Tagging & Versioning
7. Docker Hub vs Private Registry
8. AWS ECR
9. GitLab Container Registry
10. Best Practices
11. Interview Questions

---

# 1. Docker CLI Commands

## Introduction

Docker provides a command-line interface (CLI) to interact with Docker Engine. These commands allow you to build images, run containers, inspect resources, manage networks, and troubleshoot running applications.

---

# Docker Command Structure

```bash
docker <object> <command> [options]
```

Examples:

```bash
docker image ls
docker container ls
docker volume ls
docker network ls
```

Older commands are still supported:

```bash
docker images
docker ps
```

However, Docker recommends using the object-oriented syntax.

---

# Docker Help

Display Docker help:

```bash
docker --help
```

Help for a specific object:

```bash
docker image --help
docker container --help
docker network --help
```

---

# Docker Version

Display Docker Client and Server versions:

```bash
docker version
```

Sample Output:

```
Client:
 Version:           28.x

Server:
 Engine:
  Version:          28.x
```

---

# Docker Information

Display system-wide Docker information:

```bash
docker info
```

Useful details include:

- Docker Root Directory
- Storage Driver
- Cgroup Version
- Number of Images
- Number of Containers
- CPU Count
- Total Memory
- Logging Driver
- Registry Configuration

---

# Working with Images

## List Images

```bash
docker image ls
```

or

```bash
docker images
```

Example Output:

```
REPOSITORY      TAG      IMAGE ID       SIZE

ubuntu          22.04    a1b2c3d4       78MB

nginx           latest   d5e6f7g8       188MB
```

---

## Pull an Image

Download an image from a registry.

```bash
docker pull nginx
```

Specific version:

```bash
docker pull nginx:1.27
```

---

## Search Images

```bash
docker search redis
```

---

## Remove an Image

```bash
docker image rm nginx
```

Force removal:

```bash
docker image rm -f nginx
```

---

## Remove Unused Images

```bash
docker image prune
```

Remove everything unused:

```bash
docker image prune -a
```

---

## View Image History

```bash
docker history nginx
```

Displays all image layers.

---

## Inspect Image

```bash
docker image inspect nginx
```

Returns detailed JSON information.

---

# Working with Containers

## Run a Container

```bash
docker run nginx
```

---

## Run in Detached Mode

```bash
docker run -d nginx
```

---

## Assign a Name

```bash
docker run --name web nginx
```

---

## Run with Port Mapping

```bash
docker run -d -p 8080:80 nginx
```

Access:

```
http://localhost:8080
```

---

## Interactive Container

```bash
docker run -it ubuntu bash
```

Explanation:

- `-i` → Interactive
- `-t` → Terminal

---

## List Running Containers

```bash
docker ps
```

---

## List All Containers

```bash
docker ps -a
```

---

## Stop Container

```bash
docker stop web
```

---

## Start Container

```bash
docker start web
```

---

## Restart Container

```bash
docker restart web
```

---

## Pause Container

```bash
docker pause web
```

---

## Resume Container

```bash
docker unpause web
```

---

## Remove Container

```bash
docker rm web
```

Force removal:

```bash
docker rm -f web
```

---

# Execute Commands Inside Containers

Open a shell:

```bash
docker exec -it web bash
```

If Bash is unavailable:

```bash
docker exec -it web sh
```

---

## View Running Processes

```bash
docker top web
```

---

## Display Logs

```bash
docker logs web
```

Follow logs:

```bash
docker logs -f web
```

Last 100 lines:

```bash
docker logs --tail 100 web
```

---

## Container Statistics

```bash
docker stats
```

Displays:

- CPU Usage
- Memory Usage
- Network I/O
- Block I/O
- PIDs

---

## Copy Files

Host → Container

```bash
docker cp file.txt web:/tmp/
```

Container → Host

```bash
docker cp web:/var/log/app.log .
```

---

## Rename Container

```bash
docker rename old-name new-name
```

---

## Commit Running Container

```bash
docker commit web myimage:v2
```

> **Note:** This is generally **not recommended** for production. Prefer rebuilding images using a Dockerfile.

---

# Docker System Commands

## Disk Usage

```bash
docker system df
```

---

## Remove Unused Resources

```bash
docker system prune
```

Includes:

- Stopped Containers
- Unused Networks
- Build Cache

---

Remove Everything

```bash
docker system prune -a
```

---

# Common Docker Command Options

| Option      | Description                 |
| ----------- | --------------------------- |
| `-d`        | Detached Mode               |
| `-it`       | Interactive Terminal        |
| `--rm`      | Remove Container After Exit |
| `--name`    | Container Name              |
| `-p`        | Port Mapping                |
| `-v`        | Mount Volume                |
| `-e`        | Environment Variable        |
| `--network` | Select Network              |
| `--restart` | Restart Policy              |
| `--cpus`    | CPU Limit                   |
| `--memory`  | Memory Limit                |

---

# Useful Production Commands

View events:

```bash
docker events
```

Display disk usage:

```bash
docker system df
```

View resource consumption:

```bash
docker stats
```

Inspect container:

```bash
docker inspect web
```

View running processes:

```bash
docker top web
```

---

# Common Mistakes

❌ Using `docker exec` to modify application code directly in production.

✔ Rebuild the image and redeploy.

---

❌ Using `docker commit` for application releases.

✔ Use Dockerfiles and CI/CD pipelines.

---

❌ Running every container with `latest`.

✔ Use versioned tags (`v1.2.3`, `2026.07.24`, etc.).

---

❌ Forgetting to clean up unused images and containers.

✔ Periodically run:

```bash
docker system prune
```

or automate cleanup as part of maintenance.

---

# Quick Revision

| Command               | Purpose                  |
| --------------------- | ------------------------ |
| `docker pull`         | Download Image           |
| `docker run`          | Create & Start Container |
| `docker ps`           | List Running Containers  |
| `docker logs`         | View Logs                |
| `docker exec`         | Execute Commands         |
| `docker stop`         | Stop Container           |
| `docker rm`           | Remove Container         |
| `docker image ls`     | List Images              |
| `docker image rm`     | Remove Image             |
| `docker stats`        | Resource Usage           |
| `docker inspect`      | Detailed Information     |
| `docker system prune` | Cleanup Unused Resources |

---

# Interview Questions

### Q1. What is the difference between `docker run`, `docker start`, and `docker create`?

**Answer:**

- `docker create` creates a container but does not start it.
- `docker start` starts an existing stopped container.
- `docker run` creates a new container and immediately starts it. If the required image is missing locally, Docker first pulls it from the registry.

---

### Q2. What is the purpose of `docker exec`?

**Answer:**

`docker exec` runs a command inside an already running container. It is commonly used for debugging, checking logs, inspecting files, or opening an interactive shell (`bash` or `sh`).

---

### Q3. How do you monitor resource usage of running containers?

**Answer:**

Use:

```bash
docker stats
```

This displays real-time CPU, memory, network I/O, and block I/O usage for each running container.

---

### Q4. Why is using the `latest` tag discouraged in production?

**Answer:**

The `latest` tag is mutable and can point to different image versions over time, making deployments unpredictable. Production environments should use immutable, versioned tags (for example, `v1.2.3` or a Git commit SHA) to ensure reproducible deployments.

---

### Q5. Why is `docker commit` not considered a best practice?

**Answer:**

`docker commit` creates images from manually modified containers, making builds non-reproducible. Production environments should always use Dockerfiles and automated CI/CD pipelines so that images can be rebuilt consistently from source.
