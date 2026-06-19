# 06-DevSecOps.md

# DevSecOps Interview Handbook

---

# What is DevSecOps?

DevSecOps is the practice of integrating security into every stage of the software development lifecycle instead of treating security as a separate activity performed only before production deployment.

Interview Answer:

Traditional development performs security checks near the end of the release cycle. DevSecOps shifts security earlier into the CI/CD pipeline so vulnerabilities are detected and fixed before deployment.

---

# Why DevSecOps?

Traditional Flow:

```text id="oq9v4v"
Development
      ↓
Testing
      ↓
Deployment
      ↓
Security Review
```

Problem:

Security issues are discovered too late.

---

DevSecOps Flow:

```text id="2f5z4w"
Development
      ↓
Security Validation
      ↓
Testing
      ↓
Deployment
```

Benefits:

- Faster remediation
- Lower risk
- Continuous compliance
- Reduced production vulnerabilities

---

# Shift Left Security

Interview Answer:

Shift Left Security means moving security activities earlier in the software lifecycle. Instead of waiting until deployment, vulnerabilities are identified during coding, build, testing, and packaging stages.

Example:

A developer accidentally commits an AWS key.

Without DevSecOps:

```text id="r8b0mn"
Production Deployment
      ↓
Security Team Finds Key
```

With DevSecOps:

```text id="42fr5q"
Git Push
      ↓
Secret Detection
      ↓
Pipeline Failure
```

Issue resolved before deployment.

---

# Security in CI/CD Pipeline

Typical Flow:

```text id="lwlp8r"
Code Commit
      ↓
SAST
      ↓
Dependency Scan
      ↓
Secret Scan
      ↓
Build
      ↓
Container Scan
      ↓
DAST
      ↓
Deploy
```

Interview Answer:

Security checks are implemented as automated pipeline gates. If critical vulnerabilities are detected, the deployment process stops until remediation is completed.

---

# Security Layers

Security is usually implemented at multiple levels:

```text id="5m97p0"
Source Code
Dependencies
Container Image
Secrets
Infrastructure
Runtime
Network
Kubernetes
Cloud
```

---

# SAST

## Static Application Security Testing

SAST analyzes source code without running the application.

Examples:

- Fortify
- SonarQube Security
- Checkmarx

Interview Answer:

SAST identifies coding vulnerabilities such as SQL Injection, Cross-Site Scripting, hardcoded secrets, insecure API usage, and unsafe coding patterns directly from source code.

---

# Fortify

## What is Fortify?

Fortify is a SAST tool used for source code security analysis.

Interview Answer:

Fortify scans source code during the CI pipeline and identifies security vulnerabilities before the application is built or deployed.

Common Findings:

- SQL Injection
- Cross-Site Scripting
- Command Injection
- Weak Cryptography

---

# DAST

## Dynamic Application Security Testing

DAST scans a running application.

Examples:

- OWASP ZAP
- Burp Suite

Interview Answer:

DAST evaluates application behavior after deployment by testing live endpoints and identifying runtime vulnerabilities.

---

# OWASP ZAP

## What is OWASP ZAP?

OWASP ZAP is an open-source DAST tool.

Interview Answer:

OWASP ZAP runs after application deployment and performs automated security testing against application endpoints.

Finds:

- XSS
- SQL Injection
- Authentication Issues
- Session Management Problems

---

# SAST vs DAST

| SAST                 | DAST                         |
| -------------------- | ---------------------------- |
| Source Code Analysis | Running Application Analysis |
| Before Deployment    | After Deployment             |
| Fortify              | OWASP ZAP                    |
| Finds Code Issues    | Finds Runtime Issues         |

Interview Answer:

SAST identifies vulnerabilities directly in source code while DAST identifies vulnerabilities in a running application. Both are required for complete security coverage.

---

# SCA

## Software Composition Analysis

Purpose:

Analyze third-party libraries.

Examples:

- Black Duck
- Snyk
- Mend

---

# Black Duck

## What is Black Duck?

Black Duck is a Software Composition Analysis tool.

Interview Answer:

Black Duck scans open-source dependencies and identifies vulnerable libraries, outdated components, and licensing risks.

Example:

Application uses:

```text id="sx2cgb"
log4j
```

Black Duck identifies:

```text id="efl2r2"
Known CVEs
```

and blocks promotion.

---

# Secret Detection

Purpose:

Prevent accidental exposure of sensitive credentials.

Examples:

```text id="xlln9r"
AWS Keys
Git Tokens
Passwords
Certificates
```

Interview Answer:

Secret Detection scans source code repositories and pipeline changes for credentials that should never be committed to version control.

---

# ClamAV

## What is ClamAV?

ClamAV is an antivirus scanning engine.

Interview Answer:

ClamAV scans uploaded files, artifacts, and packages to detect malware before deployment.

---

# Container Security

Security should begin before the image is deployed.

Pipeline Flow:

```text id="2jg7zv"
Docker Build
      ↓
Image Scan
      ↓
Registry
      ↓
Deployment
```

---

# Container Image Scanning

Purpose:

Identify vulnerabilities inside Docker images.

Tools:

- Trivy
- Clair
- Snyk
- Docker Scout

Interview Answer:

Image scanning evaluates operating system packages and application dependencies for known vulnerabilities before deployment.

---

# Common Image Vulnerabilities

Examples:

```text id="ck7jxy"
Outdated Packages
Known CVEs
Weak Libraries
Unpatched OS Components
```

---

# Secure Docker Images

Interview Answer:

Container images should use minimal base images, run non-root users, remove unnecessary packages, and undergo automated vulnerability scanning before deployment.

Bad Example:

```dockerfile id="vx2d63"
FROM ubuntu
```

Better Example:

```dockerfile id="48qpk8"
FROM alpine
```

---

# Kubernetes Security

Security Areas:

```text id="twyw5z"
RBAC
Network Policies
Secrets
Pod Security
Namespaces
Admission Controllers
```

---

# RBAC

Role-Based Access Control restricts permissions within Kubernetes.

Interview Answer:

RBAC ensures users, service accounts, and applications only receive the permissions required to perform their tasks.

Principle:

```text id="3vnh0k"
Least Privilege Access
```

---

# Network Policies

Purpose:

Restrict Pod-to-Pod communication.

Interview Answer:

Network Policies create micro-segmentation inside Kubernetes clusters by explicitly controlling allowed traffic between workloads.

---

# Kubernetes Secrets

Purpose:

Store sensitive information.

Examples:

```text id="d1r1lx"
Passwords
API Keys
Certificates
Tokens
```

Interview Answer:

Sensitive configuration should never be stored directly in deployment manifests and should instead be referenced through Secrets or external secret management systems.

---

# Vaults and Secret Managers

Common Solutions:

- OCI Vault
- AWS Secrets Manager
- Hashicorp Vault
- Azure Key Vault

Interview Answer:

Vault solutions provide centralized secret storage, encryption, rotation, auditing, and access control.

---

# OCI Vault Example

Based on your Oracle experience.

Interview Answer:

OCI Vault was used to securely store sensitive credentials and make them available to applications without exposing secrets in source code or deployment manifests.

---

# Infrastructure Security

Infrastructure should also be scanned.

Examples:

```text id="bx7c2m"
Terraform
CloudFormation
Kubernetes Manifests
```

Tools:

- Checkov
- Terrascan
- tfsec

Interview Answer:

Infrastructure-as-Code security scanning identifies insecure cloud configurations before infrastructure is provisioned.

---

# Cloud Security

Security controls typically include:

```text id="2it15m"
IAM
Encryption
Audit Logs
Network Segmentation
Secrets Management
Monitoring
```

---

# IAM Security

Interview Answer:

IAM should follow least privilege principles, role-based access, MFA enforcement, and temporary credential usage whenever possible.

---

# Security Incident Handling

Question:

Critical vulnerability found in production.

Interview Answer:

First determine the severity and exposure level. If exploitation risk exists, immediately contain the issue through access restrictions or rollback actions. Then identify affected systems, apply remediation, validate fixes, and document the root cause through an incident review process.

---

# Security in Your Oracle Project

Interview Answer:

At Oracle, DevSecOps controls were integrated directly into GitLab pipelines. Security validation included Fortify for SAST scanning, Black Duck for dependency analysis, OWASP ZAP for DAST testing, Secret Detection for credential leakage prevention, and ClamAV for malware scanning. Security gates prevented vulnerable code from progressing through deployment stages.

---

# Common Interview Scenarios

---

## Developer Wants Security Scan Removed

Interview Answer:

Security controls should not be removed simply to improve pipeline speed. Instead, optimize scan execution, run scans in parallel, tune rules, or introduce risk-based gating while maintaining security coverage.

---

## Critical Vulnerability Found Before Release

Interview Answer:

The release should be paused until the vulnerability is assessed. Critical vulnerabilities should be remediated before production deployment.

---

## Secret Found in Repository

Interview Answer:

The secret should be revoked immediately, replaced with a new credential, removed from Git history if required, and migrated into a proper secret management solution.

---

## Image Scan Shows Critical CVEs

Interview Answer:

Review the affected packages, upgrade vulnerable dependencies, rebuild the image, and rerun the scan before deployment.

---

# DevSecOps Pipeline Example

```text id="4zzmtt"
Git Push
      ↓
Fortify Scan
      ↓
Black Duck Scan
      ↓
Secret Detection
      ↓
Build
      ↓
Docker Build
      ↓
Image Scan
      ↓
OWASP ZAP
      ↓
Deploy
```

---

# Most Asked Interview Questions

1. What is DevSecOps?
2. What is Shift Left Security?
3. What is SAST?
4. What is DAST?
5. SAST vs DAST?
6. What is Fortify?
7. What is Black Duck?
8. What is OWASP ZAP?
9. What is Secret Detection?
10. What is ClamAV?
11. How do you secure Docker images?
12. How do you secure Kubernetes?
13. What is RBAC?
14. What are Network Policies?
15. How do you manage secrets?
16. What is Vault?
17. How do you handle vulnerabilities?
18. How do you secure CI/CD pipelines?
19. What security tools have you used?
20. Explain your DevSecOps implementation.

---

# InvestorAI Focus Areas

Most likely discussion areas:

```text id="g0p4ls"
GitLab Security Pipelines
Fortify
Black Duck
OWASP ZAP
Secret Detection
Container Security
Kubernetes Security
RBAC
Network Policies
Secrets Management
Production Security Incidents
```

Prepare these topics thoroughly because they align directly with your resume and are significantly more likely to be discussed than advanced AWS or Terraform questions.
