---
# 1. Docker Security

## Introduction

Security is one of the most important aspects of running containers in production.

A Docker container is **not a virtual machine**. Containers share the host operating system kernel, which means a compromised container can potentially impact the host if proper security controls are not in place.

Docker follows the principle of **defense in depth**, where multiple layers of security work together to reduce risk.
---

# Docker Security Layers

```
                Docker Security

                       │

    ┌──────────────────┼──────────────────┐

    │                  │                  │

 Image Security   Runtime Security   Host Security

    │                  │                  │

 Vulnerability     Least Privilege     Linux Kernel

 Image Signing     Seccomp             AppArmor/SELinux

 Trusted Registry  Capabilities        File Permissions

 Secret Management Read-only FS        Host Updates
```

---

# Why Docker Security Matters

Without proper security:

- Containers may run as root.
- Secrets may be exposed inside images.
- Vulnerable packages may be deployed.
- Malicious images may be downloaded.
- Host systems may be compromised.

Security should be considered during **build**, **deployment**, and **runtime**.

---

# Security Best Practices

## 1. Use Official Images

Prefer trusted images from official repositories.

Good:

```dockerfile
FROM nginx:1.27
```

Avoid:

```dockerfile
FROM random-user/nginx
```

Official images are maintained, updated, and regularly scanned for vulnerabilities.

---

## 2. Use Minimal Base Images

Smaller images reduce the attack surface.

Preferred:

- alpine
- debian:slim
- distroless
- ubi-minimal

Avoid large images unless necessary.

---

## 3. Never Run as Root

By default, containers run as the root user.

This is a security risk.

Instead:

```dockerfile
RUN adduser -D appuser

USER appuser
```

Benefits:

- Limits permissions.
- Reduces privilege escalation risk.
- Follows the principle of least privilege.

---

## 4. Scan Images for Vulnerabilities

Before pushing images to production, scan them.

Popular tools:

- Trivy
- Docker Scout
- Grype
- Snyk

Example:

```bash
trivy image payment-service:v1
```

The scan identifies:

- Critical CVEs
- High vulnerabilities
- Medium vulnerabilities
- Low vulnerabilities

---

## 5. Keep Images Updated

Old images often contain known vulnerabilities.

Regularly rebuild images to include the latest package updates and security patches.

---

## 6. Use Read-only Filesystems

Prevent applications from modifying the container filesystem.

Example:

```bash
docker run --read-only nginx
```

Applications should write only to mounted volumes or temporary filesystems.

---

## 7. Limit Linux Capabilities

Containers inherit Linux capabilities by default.

Drop unnecessary capabilities.

Example:

```bash
docker run --cap-drop ALL
```

Grant only the capabilities that the application requires.

---

## 8. Resource Limits

Limit CPU and memory usage to prevent a single container from exhausting host resources.

Example:

```bash
docker run \
  --memory="512m" \
  --cpus="1.5" \
  nginx
```

Benefits:

- Prevents resource starvation.
- Improves stability.
- Mitigates denial-of-service scenarios.

---

## 9. Use Docker Secrets

Never store secrets directly in Docker images or Git repositories.

❌ Bad:

```dockerfile
ENV DB_PASSWORD=admin123
```

✔ Good:

- Docker Secrets
- Kubernetes Secrets
- AWS Secrets Manager
- HashiCorp Vault

---

## 10. Sign Images

Use image signing tools such as:

- Docker Content Trust (Notary)
- Cosign

This ensures image authenticity and integrity.

---

# Security Checklist

✔ Use official images.

✔ Use minimal base images.

✔ Avoid running as root.

✔ Scan every image.

✔ Keep dependencies updated.

✔ Store secrets securely.

✔ Limit container resources.

✔ Remove unused packages.

✔ Use signed images.

✔ Use private registries for internal applications.

---

# Common Mistakes

❌ Running every container as root.

✔ Create a dedicated application user.

---

❌ Embedding passwords in Dockerfiles.

✔ Use external secret management solutions.

---

❌ Ignoring vulnerability scan reports.

✔ Integrate image scanning into CI/CD pipelines and address critical findings before deployment.

---

❌ Using outdated base images.

✔ Rebuild images regularly with the latest security patches.

---

# Real-world Example

A CI/CD pipeline for a production application:

```
Git Push

↓

Build Docker Image

↓

Run Unit Tests

↓

Trivy Image Scan

↓

Critical Vulnerabilities?

↓

YES → Pipeline Fails

↓

NO

↓

Push Image to AWS ECR

↓

Deploy to Kubernetes
```

This prevents vulnerable images from reaching production.

---

# Quick Revision

| Best Practice        | Reason                 |
| -------------------- | ---------------------- |
| Official Images      | Trusted source         |
| Minimal Base Image   | Smaller attack surface |
| Non-root User        | Least privilege        |
| Vulnerability Scan   | Detect CVEs            |
| Read-only Filesystem | Prevent tampering      |
| Docker Secrets       | Protect credentials    |
| Resource Limits      | Improve stability      |
| Image Signing        | Verify authenticity    |

---

# Interview Questions

### Q1. Why should containers not run as root?

**Answer:**

Running containers as root increases the risk of privilege escalation and host compromise. Using a non-root user limits permissions and follows the principle of least privilege.

---

### Q2. What tools can be used to scan Docker images?

**Answer:**

Common tools include:

- Trivy
- Docker Scout
- Grype
- Snyk

These tools identify known vulnerabilities (CVEs) in images before deployment.

---

### Q3. How should secrets be managed in Docker?

**Answer:**

Secrets should never be hardcoded into Dockerfiles or images. Use dedicated secret management solutions such as Docker Secrets, Kubernetes Secrets, AWS Secrets Manager, or HashiCorp Vault.

---

### Q4. What is the purpose of Docker image signing?

**Answer:**

Image signing verifies that an image was produced by a trusted source and has not been tampered with. Tools such as Cosign and Docker Content Trust provide this capability.

---

### Q5. Why should production images use minimal base images?

**Answer:**

Minimal base images reduce the number of installed packages, resulting in a smaller attack surface, fewer vulnerabilities, faster downloads, and improved startup times.
