# 05-CICD.md

# CI/CD Interview Handbook

---

# What is CI/CD?

CI/CD is an automation process that takes application code from development to production through automated validation, testing, packaging, security scanning, and deployment steps.

Interview Answer:

In our environment, whenever developers commit code to GitLab, a pipeline automatically starts. The pipeline validates the code, runs quality checks, executes security scans, builds application artifacts, creates Docker images, stores them in a registry, and deploys them to Kubernetes environments.

---

# CI vs CD

## Continuous Integration (CI)

Continuous Integration focuses on validating code changes before deployment.

Typical activities:

- Code Checkout
- Linting
- Unit Testing
- Security Scanning
- Build Artifact Creation

---

## Continuous Delivery / Deployment (CD)

Continuous Delivery focuses on releasing validated artifacts into environments.

Typical activities:

- Image Creation
- Deployment
- Smoke Testing
- Promotion
- Rollback

---

# Complete CI/CD Flow

```text
Developer Commit
      ↓
Git Push
      ↓
Merge Request
      ↓
Pipeline Trigger
      ↓
Code Validation
      ↓
Unit Tests
      ↓
Security Scans
      ↓
Build Artifact
      ↓
Docker Build
      ↓
Image Scan
      ↓
Push Registry
      ↓
Deploy Dev
      ↓
Deploy QA
      ↓
Deploy Production
```

---

# Real Interview Question

## Explain your CI/CD pipeline from start to finish.

Interview Answer:

Whenever a developer pushes code into GitLab, the pipeline gets triggered automatically. The first stage checks out the source code and validates its structure. Unit tests are executed to verify application functionality. Security tools such as Fortify, Black Duck, OWASP ZAP, Secret Detection, and ClamAV perform security validation. Once validation succeeds, application artifacts are built and packaged. A Docker image is then created and pushed to the registry. After successful image scanning, deployment stages promote the application through Dev, QA, and Production environments using controlled approvals and deployment strategies.

---

# CI/CD Stages Explained

---

# Stage 1: Source Code Checkout

Purpose:

Retrieve source code from Git repository.

Example:

```bash
git clone repo-url
```

Interview Answer:

The checkout stage downloads the latest application code from the repository so subsequent stages can build and validate it.

---

# Stage 2: Code Quality Checks

Purpose:

Detect coding standard violations.

Common Tools:

- SonarQube
- ESLint
- Pylint
- Flake8

Interview Answer:

Code quality tools identify maintainability issues, duplicated code, complexity problems, and coding standard violations before applications reach production.

---

# Stage 3: Unit Testing

Purpose:

Validate business logic.

Example:

Python:

```bash
pytest
```

React:

```bash
npm test
```

Interview Answer:

Unit tests validate individual components in isolation and ensure new code changes do not break existing functionality.

---

# Stage 4: Security Scanning

Purpose:

Identify vulnerabilities before deployment.

Tools:

- Fortify
- Black Duck
- OWASP ZAP
- Trivy
- Secret Detection

Interview Answer:

Security scanning is integrated directly into the pipeline to enforce shift-left security and prevent vulnerable code from reaching production environments.

---

# Stage 5: Build Stage

This is one of the most asked interview topics.

---

## What happens during Build?

The source code is compiled or packaged into deployable artifacts.

Examples:

### Java

Source Code:

```text
.java
```

Build Output:

```text
.jar
```

Command:

```bash
mvn package
```

---

### Python

Python is interpreted.

Usually no compilation occurs.

Build stage may:

```bash
pip install -r requirements.txt
```

or

```bash
python setup.py bdist_wheel
```

Output:

```text
.whl
```

or packaged application.

---

### React

Source Code:

```text
.jsx
.js
.css
```

Build Command:

```bash
npm install
npm run build
```

Output:

```text
build/
```

This contains optimized static files.

Interview Answer:

For React applications, the build process converts source code into optimized HTML, JavaScript, and CSS assets. For Python applications, dependencies are installed and packages are prepared for deployment.

---

# Stage 6: Docker Build

Purpose:

Package application into a container image.

Example:

```bash
docker build -t app:v1 .
```

Interview Answer:

Docker images provide a consistent runtime environment across development, testing, and production environments.

---

# Stage 7: Image Scanning

Purpose:

Detect vulnerabilities inside Docker images.

Tools:

- Trivy
- Clair
- Snyk
- Docker Scout

Interview Answer:

Image scanning ensures operating system packages and application dependencies do not contain known security vulnerabilities.

---

# Stage 8: Push to Registry

Purpose:

Store images centrally.

Examples:

- Docker Hub
- AWS ECR
- Azure ACR
- GitLab Registry

Command:

```bash
docker push app:v1
```

---

# Stage 9: Deployment

Purpose:

Deploy application into Kubernetes.

Example:

```bash
kubectl apply -f deployment.yaml
```

or

```bash
helm upgrade --install
```

---

# Deployment Strategies

---

## Rolling Update

Interview Answer:

Rolling updates gradually replace old Pods with new versions while keeping the application available throughout the deployment process.

Best for:

Most production deployments.

---

## Blue Green Deployment

Interview Answer:

Blue Green deployments maintain two identical environments. Traffic is switched from the old environment to the new one after successful validation.

Benefits:

- Instant rollback
- Minimal downtime

---

## Canary Deployment

Interview Answer:

Canary deployments release new versions to a small percentage of users before rolling out to the entire user base.

Benefits:

- Reduced deployment risk
- Early detection of issues

---

# Jenkins Architecture

```text
Developer
      ↓
Git Repository
      ↓
Jenkins Master
      ↓
Build Agent
      ↓
Artifact
      ↓
Deployment
```

---

# GitLab Architecture

```text
Developer
      ↓
Git Push
      ↓
GitLab
      ↓
Runner
      ↓
Pipeline
      ↓
Deployment
```

---

# GitLab Runner

Interview Answer:

GitLab Runner executes CI/CD jobs and can run on virtual machines, containers, or Kubernetes clusters.

---

# Shared Templates

Interview Answer:

Shared templates centralize pipeline logic so multiple repositories follow the same build, testing, security, and deployment standards.

This aligns directly with your Oracle experience.

---

# Pipeline Optimization

Question:

How do you reduce pipeline runtime?

Interview Answer:

Pipeline runtime can be reduced by implementing parallel execution, dependency caching, multi-stage Docker builds, optimized test execution, reusable artifacts, and selective pipeline triggers.

---

# Artifact vs Package vs Docker Image

Artifact:

```text
jar
war
zip
build/
```

Package:

```text
rpm
deb
wheel
```

Docker Image:

```text
Container Runtime Package
```

Interview Answer:

Artifacts are application outputs, packages are installable distributions, and Docker images package the application together with its runtime dependencies.

---

# Environment Promotion

Typical Flow:

```text
Development
      ↓
QA
      ↓
Staging
      ↓
Production
```

Interview Answer:

Each environment performs additional validation before promoting artifacts to the next stage. Production deployments often require manual approvals.

---

# Rollback Strategy

Question:

Deployment failed. What will you do?

Interview Answer:

First verify deployment logs, application logs, and Kubernetes events. If production impact exists, immediately rollback to the last stable version while performing root cause analysis separately.

Kubernetes:

```bash
kubectl rollout undo deployment app
```

---

# Common Production Failures

## Build Failure

Check:

- Dependencies
- Compilation errors
- Build logs

---

## Test Failure

Check:

- Test reports
- Environment variables
- Mock services

---

## Deployment Failure

Check:

- Kubernetes Events
- Image Availability
- Resource Limits
- Secrets
- ConfigMaps

---

# Oracle-Specific Interview Answer

Question:

Explain your CI/CD experience.

Answer:

At Oracle, I managed centralized GitLab CI/CD pipelines shared across multiple repositories. The pipelines incorporated build validation, automated testing, security scanning through Fortify, Black Duck, OWASP ZAP, Secret Detection, and ClamAV, followed by controlled deployments into Kubernetes environments. Shared templates standardized deployment workflows and security controls across projects.

---

# Top 20 Interview Questions

1. Explain CI/CD.
2. Explain your pipeline.
3. CI vs CD.
4. Build process for Java.
5. Build process for Python.
6. Build process for React.
7. Unit Testing.
8. SonarQube.
9. Security Scanning.
10. Artifact vs Docker Image.
11. Jenkins vs GitLab.
12. Shared Templates.
13. Pipeline Optimization.
14. Blue Green Deployment.
15. Canary Deployment.
16. Rolling Update.
17. Rollback.
18. Registry.
19. GitLab Runner.
20. Production Deployment Failure.
