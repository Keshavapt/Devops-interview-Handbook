# CI/CD Interview Handbook

> **Target Audience:** DevOps Engineers (2-8 Years Experience)
> **Interview Weightage:** ⭐⭐⭐⭐⭐

---

## Table of Contents

- [1. What is CI/CD?](#1-what-is-cicd)
- [2. CI vs CD](#2-ci-vs-cd)
- [3. Complete CI/CD Pipeline Flow](#3-complete-cicd-pipeline-flow)
- [4. Pipeline Stages Explained](#4-pipeline-stages-explained)
- [5. Deployment Strategies](#5-deployment-strategies)
- [6. GitLab CI/CD Architecture](#6-gitlab-cicd-architecture)
- [7. Pipeline Optimization](#7-pipeline-optimization)
- [8. Rollback Strategy](#8-rollback-strategy)
- [9. Common Production Failures](#9-common-production-failures)
- [10. Interview Questions & Answers](#10-interview-questions--answers)

---

## 1. What is CI/CD?

CI/CD is an automation process that takes application code from development to production through automated validation, testing, packaging, security scanning, and deployment steps.

**Interview Answer:** Whenever developers commit code to GitLab, a pipeline automatically starts. The pipeline validates the code, runs quality checks, executes security scans, builds artifacts, creates Docker images, stores them in a registry, and deploys them to Kubernetes environments.

---

## 2. CI vs CD

| Continuous Integration (CI) | Continuous Delivery/Deployment (CD) |
| --------------------------- | ----------------------------------- |
| Code checkout | Image creation |
| Linting | Deployment |
| Unit testing | Smoke testing |
| Security scanning | Environment promotion |
| Build artifacts | Rollback |

**CI** focuses on validating code changes before deployment. **CD** focuses on releasing validated artifacts into environments.

---

## 3. Complete CI/CD Pipeline Flow

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

## 4. Pipeline Stages Explained

### Stage 1: Source Code Checkout
**Purpose:** Retrieve source code from Git repository.
```bash
git clone repo-url
```

### Stage 2: Code Quality Checks
**Purpose:** Detect coding standard violations, maintainability issues, duplicated code.
**Tools:** SonarQube, ESLint, Pylint, Flake8

### Stage 3: Unit Testing
**Purpose:** Validate business logic in isolation.
**Tools:** JUnit, pytest, Jest, TestNG

### Stage 4: Security Scanning
**Purpose:** Identify vulnerabilities before deployment.
**Tools:** Fortify, Black Duck, OWASP ZAP, Trivy, Secret Detection, ClamAV

### Stage 5: Build Stage
**Purpose:** Compile/package source code into deployable artifacts.

| Language | Build Command | Output |
| -------- | ------------- | ------ |
| Java | `mvn package` | .jar |
| Python | `pip install -r requirements.txt` | .whl |
| React/Node | `npm run build` | build/ |

### Stage 6: Docker Build
**Purpose:** Package application into a container image.
```bash
docker build -t app:v1 .
```

### Stage 7: Image Scanning
**Purpose:** Detect vulnerabilities inside Docker images.
**Tools:** Trivy, Clair, Snyk, Docker Scout

### Stage 8: Push to Registry
**Purpose:** Store images centrally for deployment.
**Registries:** Docker Hub, AWS ECR, Azure ACR, GitLab Registry
```bash
docker push app:v1
```

### Stage 9: Deployment
**Purpose:** Deploy application into Kubernetes.
```bash
kubectl apply -f deployment.yaml
# or
helm upgrade --install app ./chart
```

---

## 5. Deployment Strategies

| Strategy | Description | Best For |
| -------- | ----------- | -------- |
| **Rolling Update** | Gradually replaces old pods with new versions | Most production deployments |
| **Blue-Green** | Two identical environments, switch traffic | Zero downtime, instant rollback |
| **Canary** | Release to small % of users first | Risk reduction, early detection |

### Rolling Update
Gradually replaces old pods while keeping the application available throughout deployment.

### Blue-Green
Two identical environments. Traffic switches from old (blue) to new (green) after validation.
**Benefits:** Instant rollback (switch back to blue), minimal downtime.

### Canary
New version released to a small percentage of users before full rollout.
**Benefits:** Reduced deployment risk, early detection of issues.

---

## 6. GitLab CI/CD Architecture

```text
Developer → Git Push → GitLab → Runner → Pipeline → Deployment
```

### GitLab Runner
Executes CI/CD jobs. Can run on:
- Virtual Machines
- Docker containers
- Kubernetes clusters

### Shared Templates
Reusable pipeline definitions that standardize build, test, and deploy across repositories.

```yaml
include:
  - project: ci/common
    file: /templates/deploy.yml
```

**Interview Answer (Oracle):** We maintained centralized GitLab CI/CD templates used across multiple repositories. Build standards, security scans, and deployment logic were inherited from shared templates.

---

## 7. Pipeline Optimization

| Technique | Description |
| --------- | ----------- |
| Parallel execution | Run independent jobs concurrently |
| Dependency caching | Cache npm/maven packages between runs |
| Multi-stage builds | Smaller Docker images |
| Test optimization | Run only affected tests |
| Selective triggers | Only trigger on specific paths |
| Reusable artifacts | Share artifacts between stages |

---

## 8. Rollback Strategy

**Question:** Deployment failed. What will you do?

**Answer:**
1. Verify deployment logs, application logs, and Kubernetes events
2. If production impact exists, immediately rollback to the last stable version
3. Perform root cause analysis separately
4. Fix the issue and redeploy

**Kubernetes Rollback:**
```bash
kubectl rollout undo deployment app
```

---

## 9. Common Production Failures

| Failure | Check |
| ------- | ----- |
| Build failure | Dependencies, compilation errors, build logs |
| Test failure | Test reports, environment variables, mock services |
| Deployment failure | Kubernetes events, image availability, resource limits, secrets |
| Image pull failure | Image name, tag, registry authentication |
| Application crash | Application logs, resource limits, configuration |

---

## 10. Interview Questions & Answers

### Q1. Explain your CI/CD pipeline from start to finish.

**Answer:** When a developer pushes code to GitLab, the pipeline triggers automatically. The first stage checks out source code. Unit tests validate functionality. Security tools (Fortify, Black Duck, OWASP ZAP, Secret Detection) perform validation. Once passed, application artifacts are built and packaged into a Docker image. After image scanning, deployment stages promote through Dev, QA, and Production using controlled approvals.

### Q2. What is the difference between CI and CD?

**Answer:** CI (Continuous Integration) focuses on validating code through build, test, and security scans. CD (Continuous Delivery/Deployment) focuses on releasing validated artifacts to environments. CI catches bugs early; CD ensures reliable, automated deployments.

### Q3. What happens during the Build stage for different languages?

**Answer:**
- Java: `mvn package` produces a .jar file
- Python: `pip install` installs dependencies, may create .whl
- React/Node: `npm run build` produces optimized static files in build/

### Q4. What deployment strategy do you use?

**Answer:** We typically use rolling updates for most deployments. For critical releases, we use blue-green or canary deployments to reduce risk and enable instant rollback.

### Q5. How do you handle a failed deployment?

**Answer:** First, verify deployment logs and Kubernetes events. If production is impacted, immediately rollback using `kubectl rollout undo`. Perform root cause analysis separately, fix the issue, and redeploy.

### Q6. What is GitLab Runner?

**Answer:** GitLab Runner executes CI/CD jobs. It can ruon virtual machines, Dcker containers, or Kubernets clusters. When a pipeline is triggered, GitLab assigns jobs to avalable runrs.

### Q7. How do you reduce pipeline run time?

**Answer:** By mplementing parall exution, dependency caching, multi-stage Docker builds, optimized test selection, reusable artifacts, and selfective pipeline triggers.

### Q8. What is the difference betwen artifact, package, and Docker image?

**Answer:** Arfacts are build outputs (jar, w), packages are installable datributions (rm, d), and Dockr images package the application with all its ntime dence.

---

## Quick Revision

| Topic | Key Concept |
| --| -----------|
| CI | Code validation, building, testing |
| CD | Deploynt, promotion, rollback|
| Rolling Update | Gradual replacment |
| Blue-Green | Two thunder switch |
| Canry | Small % user release |
| GitLab Rnnr | Execues piplie jobs|
| Shared Templates | Reuable pipeline code|
| Rollback | `kubectl rollout ndo`|
| Artifact | JAR, WAR, build/ |
| Secrity Scan | Trivy, Fortify, Black Duck |