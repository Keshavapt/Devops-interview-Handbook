---

# CI/CD Pipeline Stages (Stage-by-Stage)

A production CI/CD pipeline consists of multiple stages. Each stage has a specific responsibility to ensure that only high-quality, secure, and tested code reaches production.

Typical Flow:

```
Developer

↓

Git Push

↓

Source Checkout

↓

Build

↓

Unit Testing

↓

Code Quality Analysis

↓

Security Scanning

↓

Package

↓

Docker Build

↓

Container Scan

↓

Push Image

↓

Deploy (Dev)

↓

Integration Tests

↓

Deploy (QA)

↓

Approval

↓

Deploy (Production)

↓

Smoke Tests

↓

Monitoring
```

---

# Stage 1 - Source (Checkout)

## Purpose

Retrieve the latest application source code from the repository.

Typical Tasks

- Clone Repository
- Checkout Branch
- Download Dependencies
- Verify Commit

Example

```
Git Push

↓

Pipeline Starts

↓

Checkout Source Code
```

---

# Stage 2 - Build

## Purpose

Compile the application into an executable artifact.

Examples

Java

```
mvn clean package
```

Maven creates

```
payment-service.jar
```

NodeJS

```
npm install

npm run build
```

Python

Usually no compilation, but dependency installation occurs.

Expected Output

- JAR
- WAR
- Binary
- Compiled Application

---

# Stage 3 - Unit Testing

Purpose

Verify individual functions/classes.

Tools

- JUnit
- TestNG
- pytest
- Jest

Pipeline

```
Build

↓

Run Unit Tests

↓

Pass?

↓

Continue

↓

Fail?

↓

Stop Pipeline
```

Production Rule

Never deploy code if unit tests fail.

---

# Stage 4 - Code Quality Analysis

Purpose

Check code quality and maintainability.

Common Tool

SonarQube

Checks

- Code Smells
- Bugs
- Duplicates
- Maintainability
- Test Coverage

Pipeline

```
Application

↓

SonarQube

↓

Quality Gate

↓

Pass

↓

Continue
```

If the Quality Gate fails, the pipeline should stop.

---

# Stage 5 - Security Scanning

Purpose

Identify security vulnerabilities before deployment.

Common Tools

- Trivy
- Snyk
- OWASP Dependency Check
- Grype

Checks

- Vulnerable Packages
- CVEs
- Dependency Risks
- Secrets

Pipeline

```
Docker Image

↓

Trivy Scan

↓

Critical Vulnerability?

↓

Yes

↓

Fail Pipeline
```

Never deploy images with unresolved critical vulnerabilities.

---

# Stage 6 - Package

Purpose

Prepare the application artifact for deployment.

Examples

Java

```
payment.jar
```

NodeJS

```
dist/
```

Python

```
wheel package
```

The packaged artifact is stored as a pipeline artifact.

---

# Stage 7 - Docker Build

Purpose

Package the application into a Docker image.

Example

```
docker build -t payment:v1 .
```

Result

```
payment:v1
```

Best Practices

- Multi-stage builds
- Small base images
- Versioned image tags
- Non-root user

---

# Stage 8 - Container Image Scan

Purpose

Scan the Docker image for vulnerabilities.

Tools

- Trivy
- Docker Scout
- Grype

Checks

- Base Image CVEs
- Installed Packages
- OS Vulnerabilities

Pipeline

```
Docker Image

↓

Trivy

↓

Pass

↓

Push Image
```

---

# Stage 9 - Push Image

Purpose

Store the Docker image in a registry.

Examples

- Amazon ECR
- Docker Hub
- GitLab Registry
- Azure ACR

Pipeline

```
Docker Image

↓

ECR

↓

Version Stored
```

Production deployments always pull images from the registry, not from the CI runner.

---

# Stage 10 - Deploy to Development

Purpose

Deploy the application into the development environment.

Typical Deployment

```
kubectl apply

or

Helm Upgrade

or

Terraform Apply
```

Verify

- Pods Running
- Application Starts
- Health Checks Pass

---

# Stage 11 - Integration Testing

Purpose

Verify communication between multiple services.

Examples

- API Tests
- Database Connectivity
- Kafka/RabbitMQ
- Third-party Integrations

If integration tests fail, deployment should stop.

---

# Stage 12 - Deploy to QA/UAT

Purpose

Deploy to a testing environment for QA or User Acceptance Testing.

Activities

- Manual Testing
- Regression Testing
- Performance Testing
- Business Validation

---

# Stage 13 - Manual Approval

Production deployments often require manual approval.

Pipeline

```
QA Success

↓

Approval

↓

Production Deployment
```

Why?

To reduce the risk of deploying unverified changes.

---

# Stage 14 - Production Deployment

Deployment Strategies

- Rolling Update
- Blue-Green
- Canary

Typical Production Flow

```
Pull Image

↓

Deploy

↓

Health Check

↓

Traffic Shift

↓

Done
```

---

# Stage 15 - Smoke Testing

Purpose

Quickly verify that the deployment was successful.

Examples

- Login Page
- Health Endpoint
- API Response
- Database Connection

If smoke tests fail

↓

Rollback

---

# Stage 16 - Monitoring & Alerting

Purpose

Continuously monitor the application after deployment.

Tools

- Prometheus
- Grafana
- CloudWatch
- ELK/Loki

Monitor

- CPU
- Memory
- Errors
- Latency
- Response Time
- Pod Restarts

---

# Complete Production Pipeline

```
Developer

↓

Git Push

↓

Checkout

↓

Build

↓

Unit Test

↓

SonarQube

↓

Security Scan

↓

Package

↓

Docker Build

↓

Image Scan

↓

Push to ECR

↓

Deploy Dev

↓

Integration Test

↓

Deploy QA

↓

Approval

↓

Deploy Production

↓

Smoke Test

↓

Monitoring
```

---

# Interview Questions

### What happens during the Build stage?

The application source code is compiled and converted into a deployable artifact such as a JAR, WAR, binary, or build directory.

---

### Why perform Unit Testing before deployment?

To catch bugs early and prevent defective code from progressing further in the pipeline.

---

### Why use SonarQube?

To enforce code quality standards by detecting bugs, code smells, duplicated code, and low test coverage.

---

### Why scan Docker images?

To identify known vulnerabilities (CVEs) in the operating system and application dependencies before deployment.

---

### Why push images to ECR?

To store versioned Docker images in a secure, centralized registry from which deployment environments can pull consistent artifacts.

---

### Why include a Manual Approval stage?

To ensure that production deployments are reviewed and approved, reducing the risk of introducing unverified changes.

---

### Why perform Smoke Tests after deployment?

To quickly verify that the core functionality of the application is working before considering the deployment successful.

---

# Interview Tip

When asked **"Explain your CI/CD pipeline"**, explain **each stage, its purpose, and the tools used**. Avoid simply listing stage names. A structured explanation demonstrates practical experience with real-world pipelines.
