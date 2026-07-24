---

# 9. Docker Images, Layers & Storage

## What is a Docker Image?

A Docker Image is an **immutable, read-only template** that contains everything required to run an application.

Think of it as a blueprint from which one or more containers are created.

A Docker Image typically contains:

- Application Source Code
- Runtime (Java, Python, Node.js, etc.)
- Required Libraries
- System Packages
- Environment Variables
- Configuration Files
- Metadata

A Docker Image **does not execute** by itself.

Only when an image is started using `docker run` does it become a **Docker Container**.

---

## Image Lifecycle

```
Developer

↓

Dockerfile

↓

docker build

↓

Docker Image

↓

docker push

↓

Docker Registry

↓

docker pull

↓

docker run

↓

Docker Container
```

---

## Characteristics of Docker Images

- Immutable (cannot be modified after creation)
- Read-only
- Versioned using Tags
- Layered Architecture
- Portable
- Reusable
- Can create multiple containers

---

## Example

```
ubuntu:22.04

python:3.12

mysql:8

redis:7

company/payment-service:v1
```

---

# Image vs Container

| Image                      | Container              |
| -------------------------- | ---------------------- |
| Blueprint                  | Running Instance       |
| Read-only                  | Writable               |
| Stored in Registry         | Runs on Docker Host    |
| Immutable                  | Temporary              |
| Can create many Containers | Created from one Image |

---

# What are Docker Layers?

Every Docker Image is built using **multiple read-only layers**.

Each instruction in a Dockerfile creates a new layer.

Example Dockerfile

```dockerfile
FROM ubuntu:22.04

RUN apt-get update

RUN apt-get install -y nginx

COPY index.html /usr/share/nginx/html/

CMD ["nginx","-g","daemon off;"]
```

Generated Layers

```
Layer 5

CMD

--------------------

Layer 4

COPY index.html

--------------------

Layer 3

Install Nginx

--------------------

Layer 2

apt update

--------------------

Layer 1

Ubuntu Base Image
```

Every layer is cached independently.

---

# Why Docker Uses Layers

Instead of rebuilding the entire image every time, Docker only rebuilds the layers that have changed.

Example

Suppose only

```
COPY app.py
```

changes.

Docker will reuse all previous layers.

```
Ubuntu

↓

Java

↓

Dependencies

↓

Application Code (Changed)

↓

Rebuild Only This Layer
```

This makes builds much faster.

---

# Layer Caching

Consider the Dockerfile

```dockerfile
FROM python:3.12

COPY requirements.txt .

RUN pip install -r requirements.txt

COPY . .

CMD ["python","app.py"]
```

Suppose only

```
app.py
```

changes.

Docker will reuse

- Base Image
- requirements.txt
- pip install layer

Only

```
COPY . .
```

is rebuilt.

---

## Good Dockerfile Ordering

```dockerfile
COPY requirements.txt .

RUN pip install -r requirements.txt

COPY . .
```

Dependencies rarely change.

Docker cache is reused.

---

## Bad Dockerfile Ordering

```dockerfile
COPY . .

RUN pip install -r requirements.txt
```

Every source code modification invalidates the cache.

Dependencies are installed again.

Build becomes slow.

---

# Read-only Layers

Every Image Layer is immutable.

```
Layer 5

Application

↓

Layer 4

Libraries

↓

Layer 3

Python

↓

Layer 2

Ubuntu Packages

↓

Layer 1

Ubuntu Base
```

These layers never change.

Every container shares them.

---

# Writable Layer

When a container starts,

Docker creates one additional writable layer.

```
Application Layer

↓

Read-only Layers

↓

Writable Layer
```

All runtime modifications happen here.

Examples

- Logs
- Temporary Files
- User Uploads
- Cache
- Generated Reports

---

# Copy-on-Write (CoW)

Docker does not duplicate files unnecessarily.

Instead,

All containers initially share the same read-only image layers.

When a container modifies a file,

Docker copies that file into the writable layer.

This mechanism is called **Copy-on-Write (CoW)**.

---

## Example

Three containers

```
Image

↓

Container A

Container B

Container C
```

Initially

All containers share

```
Ubuntu Files

Python

Libraries

Application
```

Now

Container B modifies

```
config.yaml
```

Docker copies only that file into Container B's writable layer.

Other containers continue using the original shared file.

---

# Benefits of Copy-on-Write

- Saves Disk Space
- Faster Container Startup
- Better Memory Utilization
- Multiple Containers Share Same Layers

---

# OverlayFS

Docker commonly uses **OverlayFS** as its storage driver on Linux.

OverlayFS merges multiple directories into one unified filesystem.

Imagine:

```
Layer 5

Application

↓

Layer 4

Python Packages

↓

Layer 3

Ubuntu Packages

↓

Layer 2

Ubuntu

↓

Writable Layer

↓

Merged Filesystem
```

The container sees only one filesystem even though multiple layers exist underneath.

---

# Advantages of OverlayFS

- Faster Builds
- Efficient Storage
- Layer Sharing
- Reduced Disk Usage
- Better Performance

---

# Storage Drivers

Docker supports different storage drivers depending on the operating system.

Common drivers include:

- overlay2 (Recommended on Linux)
- fuse-overlayfs
- aufs (Deprecated)
- btrfs
- zfs
- devicemapper (Legacy)

Today, **overlay2** is the default and recommended driver for most Linux distributions.

---

# Inspecting Image Layers

View image history

```bash
docker history nginx
```

Inspect image details

```bash
docker image inspect nginx
```

List local images

```bash
docker images
```

Check image size

```bash
docker image ls
```

---

# Image Optimization

Large Docker Images increase:

- Download Time
- CI/CD Duration
- Registry Storage
- Kubernetes Startup Time

Always optimize images.

---

## Best Practices

### Use Small Base Images

Instead of

```dockerfile
FROM ubuntu
```

Prefer

```dockerfile
FROM alpine
```

or

```dockerfile
FROM debian:slim
```

---

### Remove Temporary Files

Instead of

```dockerfile
RUN apt-get update

RUN apt-get install nginx
```

Prefer

```dockerfile
RUN apt-get update && \
    apt-get install -y nginx && \
    rm -rf /var/lib/apt/lists/*
```

---

### Use Multi-stage Builds

Build dependencies should not exist in production images.

Builder Image

↓

Compile

↓

Copy only binary

↓

Runtime Image

---

### Minimize Layers

Instead of

```dockerfile
RUN apt update

RUN apt install git

RUN apt install curl
```

Use

```dockerfile
RUN apt update && \
    apt install -y git curl
```

---

### Use .dockerignore

Avoid copying

- .git
- node_modules
- target
- logs
- temp files
- IDE folders

---

# Real-world Example

Java Spring Boot

```
Developer

↓

Maven Build

↓

Jar Generated

↓

Docker Build

↓

Image

↓

Push to AWS ECR

↓

Deploy to Kubernetes
```

Only the application layer changes between releases.

Ubuntu and Java layers remain cached.

Deployment becomes significantly faster.

---

# Common Mistakes

❌ Believing every container has its own copy of image files.

✔ Containers share image layers.

---

❌ Modifying Docker Images at runtime.

✔ Runtime modifications occur only in the writable layer.

---

❌ Using Ubuntu for every application.

✔ Prefer minimal base images whenever possible.

---

❌ Copying the entire project before installing dependencies.

✔ Copy dependency files first to maximize Docker cache reuse.

---

# Quick Revision

| Concept        | Description                          |
| -------------- | ------------------------------------ |
| Image          | Read-only Template                   |
| Container      | Running Instance of Image            |
| Layer          | Read-only Filesystem Layer           |
| Writable Layer | Stores Runtime Changes               |
| Copy-on-Write  | Copies Files Only When Modified      |
| OverlayFS      | Merges Multiple Layers into One View |
| Storage Driver | Manages Image Layers on Disk         |
| Layer Cache    | Reuses Unchanged Layers During Build |

---

# Interview Questions

### Q1. Why are Docker Images immutable?

**Answer**

Docker Images are immutable to ensure consistency, reproducibility, and version control. Instead of modifying an existing image, a new image is created with the required changes.

---

### Q2. What are Docker Layers?

**Answer**

Docker Layers are read-only filesystem layers created for each Dockerfile instruction. Docker reuses unchanged layers through caching, making image builds faster and more efficient.

---

### Q3. What is Copy-on-Write?

**Answer**

Copy-on-Write is a storage optimization mechanism where containers initially share the same read-only image layers. A file is copied into a container's writable layer only when that container modifies it.

---

### Q4. What is OverlayFS?

**Answer**

OverlayFS is the default storage driver on most Linux systems. It combines multiple read-only image layers with a writable container layer into a single unified filesystem presented to the container.

---

### Q5. Why should Dockerfiles copy dependency files before application code?

**Answer**

Copying dependency files first allows Docker to cache the dependency installation layer. When only the application code changes, Docker reuses the cached dependency layer, significantly reducing build time.

---

### Q6. How can you reduce Docker Image size?

**Answer**

- Use minimal base images (Alpine or Slim variants).
- Use multi-stage builds.
- Remove temporary files.
- Combine related `RUN` instructions.
- Exclude unnecessary files with `.dockerignore`.
- Install only required packages.
