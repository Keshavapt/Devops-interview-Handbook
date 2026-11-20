# CI/CD & GitOps Handbook

> **Target Audience:** 5+ Years Experience
> **Role:** Senior DevOps / Platform Engineer
> **Interview Weightage:** ⭐⭐⭐⭐⭐

---

## Table of Contents

- [1. What is CI/CD?](#1-what-is-cicd)
- [2. CI vs CD](#2-ci-vs-cd)
- [3. Complete CI/CD Pipeline Flow](#3-complete-cicd-pipeline-flow)
- [4. 16-Stage Production Pipeline Breakdown](#4-16-stage-production-pipeline-breakdown)
- [5. Deployment Strategies](#5-deployment-strategies)
- [6. GitLab CI/CD Architecture](#6-gitlab-cicd-architecture)
- [7. Pull-Based GitOps & ArgoCD](#7-pull-based-gitops--argocd)
- [8. Pipeline Optimization](#8-pipeline-optimization)
- [9. Rollback Strategy](#9-rollback-strategy)
- [10. Common Production Failures](#10-common-production-failures)
- [11. Interview Questions & Answers](#11-interview-questions--answers)
- [12. Oracle DevOps Experience Positioning](#12-oracle-devops-experience-positioning)
- [13. 5-Minute Quick Revision Table](#13-5-minute-quick-revision-table)

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

## 4. 16-Stage Production Pipeline Breakdown

A production CI/CD pipeline consists of multiple stages. Each stage has a specific responsibility to ensure that only high-quality, secure, and tested code reaches production.

| Stage | Purpose | Key Tools |
| ----- | ------- | --------- |
| 1. Source Checkout | Retrieve latest source code from Git repository | Git, GitLab |
| 2. Build | Compile application into deployable artifact | Maven, npm, pip |
| 3. Unit Testing | Verify individual functions/classes in isolation | JUnit, pytest, Jest, TestNG |
| 4. Code Quality Analysis | Check code quality and maintainability | SonarQube, ESLint |
| 5. Security Scanning | Identify vulnerabilities before deployment | Fortify, Black Duck, OWASP ZAP, Trivy, Snyk |
| 6. Package | Prepare application for packaging | JAR, WAR, binary, build/ |
| 7. Docker Build | Package application into container image | Docker, BuildKit |
| 8. Container Scan | Scan Docker image for vulnerabilities | Trivy, Clair, Docker Scout |
| 9. Push Image | Store image in centralized registry | ECR, Docker Hub, Artifactory |
| 10. Deploy Dev | Deploy to development environment | kubectl, Helm, Terraform |
| 11. Integration Tests | Run integration tests against deployed app | Postman, RestAssured, Cypress |
| 12. Deploy QA | Deploy to QA environment | kubectl, Helm |
| 13. Performance Testing | Validate performance under load | JMeter, Gatling, k6 |
| 14. Manual Approval | Production deployment gate | GitLab Approvals, ServiceNow |
| 15. Deploy Production | Deploy to production (rolling/blue-green/canary) | kubectl, Helm, ArgoCD |
| 16. Smoke Testing & Monitoring | Verify deployment success and monitor | Prometheus, Grafana, CloudWatch, ELK |

### Stage Detail: Source Checkout
Retrieve the latest application source code from the repository.
- Clone Repository, Checkout Branch, Download Dependencies, Verify Commit

### Stage Detail: Build
Compile the application into an executable artifact.
- Java: mvn clean package -> .jar
- NodeJS: npm install + npm run build -> build/
- Python: dependency installation (no compilation)

### Stage Detail: Unit Testing
Verify individual functions/classes. Never deploy if unit tests fail.
- Tools: JUnit, TestNG, pytest, Jest

### Stage Detail: Code Quality Analysis
Check code quality and maintainability (code smells, bugs, duplicates, coverage).
- Tool: SonarQube with Quality Gate

### Stage Detail: Security Scanning
Identify security vulnerabilities before deployment.
- SAST: Fortify, SonarQube Security, Checkmarx
- DAST: OWASP ZAP
- SCA: Black Duck, OWASP Dependency Check
- Container: Trivy, Snyk, Docker Scout
- Secret Detection: GitLab Secret Detection, TruffleHog

### Stage Detail: Container Scan
Scan Docker images for known CVEs in OS packages and application dependencies.
- Never deploy images with unresolved critical vulnerabilities.

### Stage Detail: Push to Registry
Store versioned Docker images in a secure, centralized registry.
- Amazon ECR, Docker Hub, Artifactory, Harbor

### Stage Detail: Deployment Strategies
- Rolling Update: Gradually replaces old pods with new ones
- Blue-Green: Two environments, instant switch
- Canary: New version to small % of users, then expand

### Stage Detail: Manual Approval
Production deployments require manual approval to reduce risk of unverified changes.

### Stage Detail: Smoke Testing
Quickly verify core functionality after deployment (login page, health endpoint, API response, database connection).

### Stage Detail: Monitoring & Alerting
Continuously monitor the application after deployment (CPU, memory, errors, latency, response time, pod restarts).

---

## 5. Deployment Strategies

| Strategy | Description | Best For |
| -------- | ----------- | -------- |
| Rolling Update | Gradually replaces old pods with new ones | Most microservices |
| Blue-Green | Two identical environments, switch traffic at once | Critical releases, instant rollback |
| Canary | New version to small % of users, then expand | Risk reduction, production validation |

### Canary Rollout Example

v1 (100% traffic)
    |
    v
Deploy v2 (10% traffic) -> Monitor errors
    |
    v
v2 (50% traffic) -> Monitor errors
    |
    v
v2 (100% traffic) -> v1 terminated

---

## 6. GitLab CI/CD Architecture

### GitLab Runner

GitLab Runner executes CI/CD jobs. It can run on virtual machines, Docker containers, or Kubernetes clusters. When a pipeline is triggered, GitLab assigns jobs to available runners.

### Shared Templates

Reusable pipeline definitions maintained centrally.

```yaml
include:
  - project: cicd/common
    file: /templates/security-scan.yml
```

**Benefits:** Standardization, reusability, governance across multiple repositories.

### Pipeline Variables

Store tokens, URLs, environment names as protected/masked variables.

```yaml
variables:
  ENV: prod
```

### Protected Branches & Variables

- Protected branches prevent direct push to main/master/production
- Protected variables hide secrets (AWS_KEY, TOKEN, PASSWORD)

### MR Validation Flow

Developer Commit
      |
      v
Push Feature Branch
      |
      v
MR Created
      |
      v
Pipeline Triggered
      |
      v
Build -> Unit Test -> Security Scan -> Approval -> Merge

---

## 7. Pull-Based GitOps & ArgoCD

### Push vs Pull-Based GitOps

| Push-Based (CI/CD) | Pull-Based (GitOps) |
| ------------------ | ------------------- |
| CI/CD pipeline pushes changes to cluster | ArgoCD pulls desired state from Git |
| Pipeline has cluster credentials | ArgoCD has cluster credentials; pipeline only updates Git |
| Imperative: kubectl apply | Declarative: Git is source of truth |
| Drift possible between Git and cluster | ArgoCD continuously reconciles drift |
| Harder to audit what's running | Git history = deployment audit log |

### ArgoCD Architecture

Git Repository (Manifests/Helm/Kustomize)
      |
      v (Pull)
ArgoCD Controller
      |
      v (Reconcile)
Kubernetes Cluster
      |
      v
Application running matches Git state

### How ArgoCD Works

1. ArgoCD continuously monitors Git repository for changes
2. When Git state differs from cluster state, ArgoCD detects drift
3. ArgoCD syncs cluster to match Git (automated or manual)
4. Provides UI diff, rollback, and health assessment

### When to Use Each

- Push-based CI/CD: Build, test, scan, package, publish artifacts
- Pull-based GitOps (ArgoCD): Deploy and reconcile application manifests in cluster
- Hybrid (recommended): CI/CD builds and pushes images -> GitOps deploys manifests to cluster

---

## 8. Pipeline Optimization

- Parallel execution of independent jobs
- Dependency caching (Maven .m2, npm node_modules, Docker layers)
- Multi-stage Docker builds (smaller images, faster builds)
- Selective pipeline triggers (only run affected stages)
- Reusable artifacts across stages
- Self-hosted runners for build-intensive jobs

---

## 9. Rollback Strategy

### Kubernetes Rollback

kubectl rollout undo deployment <app>
kubectl rollout history deployment <app>
kubectl rollout undo deployment <app> --to-revision=2

### Rollback Decision Framework

| Scenario | Action |
| -------- | ------ |
| Build failure | Fix code, redeploy — no rollback needed |
| Test failure | Stop pipeline, fix tests |
| Deployment failure with prod impact | Immediate rollback to last stable version |
| Deployment failure without prod impact | Fix and redeploy in next window |
| Image pull failure | Verify image/tag/registry auth, redeploy |

**Rule:** If production is impacted, rollback immediately. Perform root cause analysis separately.

---

## 10. Common Production Failures

| Failure | Check |
| ------- | ----- |
| Build failure | Dependencies, compilation errors, build logs |
| Test failure | Test reports, environment variables, mock services |
| Deployment failure | Kubernetes events, image availability, resource limits, secrets |
| Image pull failure | Image name, tag, registry authentication |
| Application crash | Application logs, resource limits, configuration |
| Pipeline stuck | Runner availability, queue, stuck jobs, approval pending |

---

## 11. Interview Questions & Answers

### Q1. Explain your CI/CD pipeline from start to finish.

**Answer:** When a developer pushes code to GitLab, the pipeline triggers automatically. Source checkout validates the code. Unit tests validate functionality. Security tools (Fortify, Black Duck, OWASP ZAP, Secret Detection) perform validation. Application artifacts are built and packaged into a Docker image. After image scanning, deployment stages promote through Dev, QA, and Production using controlled approvals. Smoke tests validate production deployment, and monitoring confirms application health.

### Q2. What is the difference between CI and CD?

**Answer:** CI (Continuous Integration) focuses on validating code through build, test, and security scans. CD (Continuous Delivery/Deployment) focuses on releasing validated artifacts to environments. CI catches bugs early; CD ensures reliable, automated deployments.

### Q3. What is the 16-stage pipeline and why does each stage matter?

**Answer:** The 16-stage pipeline covers source checkout -> build -> unit testing -> code quality -> security scanning -> package -> Docker build -> container scan -> push image -> deploy dev -> integration tests -> deploy QA -> performance testing -> manual approval -> deploy production -> smoke testing/monitoring. Each stage acts as a quality gate, preventing defective or vulnerable code from reaching production.

### Q4. What deployment strategy do you use?

**Answer:** We typically use rolling updates for most deployments. For critical releases, we use blue-green or canary deployments to reduce risk and enable instant rollback.

### Q5. How do you handle a failed deployment?

**Answer:** First, verify deployment logs and Kubernetes events. If production is impacted, immediately rollback using kubectl rollout undo. Perform root cause analysis separately, fix the issue, and redeploy.

### Q6. What is GitLab Runner?

**Answer:** GitLab Runner executes CI/CD jobs. It can run on VMs, Docker containers, or Kubernetes clusters. When a pipeline is triggered, GitLab assigns jobs to available runners.

### Q7. How do you reduce pipeline run time?

**Answer:** By implementing parallel execution, dependency caching, multi-stage Docker builds, optimized test selection, reusable artifacts, and selective pipeline triggers.

### Q8. What is the difference between artifact, package, and Docker image?

**Answer:** Artifacts are build outputs (JAR, WAR), packages are installable distributions (RPM, DEB), and Docker images package the application with all its runtime dependencies.

### Q9. Push-based CI/CD vs Pull-based GitOps — when to use each?

**Answer:** Push-based CI/CD is for building, testing, scanning, and publishing artifacts. Pull-based GitOps (ArgoCD) is for deploying and reconciling application manifests in the cluster. Use both: CI/CD builds/pushes images, GitOps deploys/reconciles manifests. GitOps prevents configuration drift and provides an audit trail through Git.

### Q10. How does ArgoCD detect and handle drift?

**Answer:** ArgoCD continuously compares the desired state in Git with the actual state in the cluster. When drift is detected, ArgoCD can alert or automatically sync to reconcile the cluster with Git. This ensures the cluster always matches the declared desired state.

---

## 12. Oracle DevOps Experience Positioning

**Interview Answer:**

At Oracle, we used centralized GitLab CI/CD templates consumed by multiple repositories for standardized build, security, testing, and deployment workflows. Security gates including Fortify (SAST), Black Duck (dependency analysis), OWASP ZAP (DAST), and secret detection were integrated into pipelines before deployments. We used shared templates to enforce consistency across teams, protected branches and variables for security, and manual approval gates for production promotions. Applications were containerized with Docker, scanned with Trivy, stored in ECR, and deployed to Kubernetes environments with zero-downtime rolling updates.

---

## 13. 5-Minute Quick Revision Table

| Topic | Key Concept |
| ----- | ----------- |
| CI | Code validation, building, testing |
| CD | Deployment, promotion, rollback |
| 16-Stage Pipeline | End-to-end quality gates from commit to production |
| Rolling Update | Gradual replacement |
| Blue-Green | Two environments, instant switch |
| Canary | Small % of users first |
| GitLab Runner | Executes pipeline jobs |
| Shared Templates | Reusable pipeline code across repos |
| Rollback | kubectl rollout undo |
| Artifact | JAR, WAR, build/ |
| Security Scan | Fortify, Black Duck, OWASP ZAP, Trivy |
| Push CI/CD | Pipeline deploys to cluster |
| Pull GitOps (ArgoCD) | ArgoCD pulls from Git, reconciles drift |
| GitOps Benefit | Git = source of truth, audit trail, drift prevention |
| Smoke Test | Verify core functionality post-deploy |
| Manual Approval | Production gate before deploy |