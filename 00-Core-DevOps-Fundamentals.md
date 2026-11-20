# Core DevOps Fundamentals

> **Interview Focus:**  
> These are the foundational concepts every DevOps Engineer (4–6 years experience) is expected to understand and explain confidently in interviews. Most interviewers start with these topics before moving to hands-on scenarios.

---

# Table of Contents

1. DevOps Principles
2. CI/CD Concepts
3. Collaboration Between Development & Operations
4. Benefits of Automation
5. Cloud Computing Basics
6. AWS Core Services
7. Infrastructure as Code (IaC)
8. Why IaC Matters
9. Benefits of IaC
10. Common IaC Tools
11. Interview Questions
12. 5-Minute Revision

---

# 1. DevOps Principles

## What is DevOps?

DevOps is a culture, set of practices, and collection of tools that enable Development and Operations teams to collaborate throughout the Software Development Lifecycle (SDLC) to deliver software faster, more reliably, and with higher quality.

Instead of working in isolated teams, developers, testers, operations, and security engineers work together with automation as the foundation.

---

## Traditional Model

```text
Developer

↓

Testing Team

↓

Operations Team

↓

Production
```

### Problems

- Slow releases
- Manual deployments
- Communication gaps
- High failure rates
- Long recovery times

---

## DevOps Model

```text
Developer

↓

CI Pipeline

↓

Automated Testing

↓

Infrastructure Provisioning

↓

Automated Deployment

↓

Monitoring

↓

Continuous Feedback
```

---

## Core DevOps Principles

### 1. Collaboration

Development, QA, Security, and Operations work together throughout the project lifecycle.

---

### 2. Automation

Automate repetitive tasks such as:

- Build
- Testing
- Infrastructure provisioning
- Deployments
- Monitoring
- Backups
- Security scans

---

### 3. Continuous Integration

Developers frequently merge code into a shared repository.

Every change is automatically:

- Built
- Tested
- Validated

---

### 4. Continuous Delivery

Applications are always in a deployable state.

Deployment to production usually requires approval.

---

### 5. Continuous Deployment

Every successful pipeline automatically deploys to production without manual approval.

---

### 6. Continuous Monitoring

Applications and infrastructure are continuously monitored using metrics, logs, and alerts.

---

### 7. Feedback

Production monitoring provides continuous feedback to developers for improvements.

---

## Benefits of DevOps

- Faster software delivery
- Reduced deployment failures
- Improved collaboration
- Higher software quality
- Faster incident recovery (Lower MTTR)
- Increased automation
- Consistent deployments
- Better scalability

---

# 2. CI/CD Concepts

## Continuous Integration (CI)

Developers frequently commit code to a shared repository.

Every commit triggers:

```text
Git Push

↓

Build

↓

Unit Tests

↓

Static Code Analysis

↓

Package Artifact
```

### Goal

Detect integration issues early.

---

## Continuous Delivery (CD)

Once CI succeeds:

```text
Build Artifact

↓

Deploy Dev

↓

Integration Tests

↓

Deploy QA

↓

Manual Approval

↓

Production Ready
```

### Goal

Keep applications ready for production deployment.

---

## Continuous Deployment

```text
Git Push

↓

CI Pipeline

↓

Tests Pass

↓

Deploy Production

↓

Smoke Tests

↓

Monitoring
```

### Goal

Automatically deploy every successful change.

---

## CI vs Continuous Delivery vs Continuous Deployment

| Feature                         | CI  | Continuous Delivery | Continuous Deployment |
| ------------------------------- | --- | ------------------- | --------------------- |
| Build                           | ✅  | ✅                  | ✅                    |
| Test                            | ✅  | ✅                  | ✅                    |
| Deploy to Test                  | ❌  | ✅                  | ✅                    |
| Manual Approval                 | ❌  | ✅                  | ❌                    |
| Automatic Production Deployment | ❌  | ❌                  | ✅                    |

---

# 3. Collaboration Between Development & Operations

DevOps removes silos between teams.

Instead of:

```text
Development

↓

Operations
```

Both teams work together.

---

## Shared Responsibilities

Developers

- Write application code
- Unit testing
- Fix bugs

Operations

- Infrastructure
- Monitoring
- Deployment
- Scaling

DevOps

- CI/CD
- Automation
- Infrastructure as Code
- Kubernetes
- Cloud
- Monitoring
- Security Integration

---

## Benefits

- Better communication
- Faster releases
- Fewer production issues
- Shared ownership
- Improved customer satisfaction

---

# 4. Benefits of Automation

Automation is the backbone of DevOps.

## Manual Process

```text
Login Server

↓

Copy Files

↓

Restart Service

↓

Verify

↓

Done
```

Problems

- Slow
- Human errors
- Inconsistent
- Difficult to scale

---

## Automated Process

```text
Git Push

↓

Pipeline

↓

Build

↓

Tests

↓

Deploy

↓

Verify

↓

Monitor
```

---

## Common Automation Areas

- Infrastructure provisioning
- Application deployment
- Docker image builds
- Kubernetes deployments
- Testing
- Security scanning
- Monitoring
- Notifications
- Backups

---

## Benefits

- Reduced manual effort
- Faster deployments
- Consistency
- Repeatability
- Improved reliability
- Better auditability

---

# 5. Cloud Computing Basics

Cloud computing provides computing resources over the internet instead of managing physical hardware.

Resources include:

- Virtual Machines
- Storage
- Databases
- Networking
- Containers
- Serverless Functions

---

## Cloud Service Models

### IaaS (Infrastructure as a Service)

Provides virtual infrastructure.

Examples

- EC2
- Virtual Networks
- Storage

---

### PaaS (Platform as a Service)

Provides managed application platforms.

Examples

- Elastic Beanstalk
- Azure App Service

---

### SaaS (Software as a Service)

Ready-to-use software.

Examples

- Gmail
- Microsoft 365
- Salesforce

---

## Benefits of Cloud

- Pay-as-you-go pricing
- High availability
- Scalability
- Global reach
- Managed services
- Faster provisioning

---

# 6. AWS Core Services

---

## EC2 (Elastic Compute Cloud)

Virtual machine service.

Use Cases

- Application hosting
- Build servers
- CI/CD runners
- Bastion hosts

---

## S3 (Simple Storage Service)

Object storage.

Use Cases

- Backups
- Static website hosting
- Terraform state
- Artifact storage
- Logs

---

## VPC (Virtual Private Cloud)

Private network inside AWS.

Components

- CIDR
- Subnets
- Route Tables
- Internet Gateway
- NAT Gateway
- Security Groups
- Network ACLs

---

## IAM (Identity and Access Management)

Controls authentication and authorization.

Components

- Users
- Groups
- Roles
- Policies

Best Practice

Grant least privilege access.

---

## RDS (Relational Database Service)

Managed relational databases.

Supported Engines

- MySQL
- PostgreSQL
- MariaDB
- SQL Server
- Oracle

Benefits

- Automated backups
- High availability
- Managed patching

---

## Lambda

Serverless compute service.

Features

- No server management
- Event-driven
- Auto-scaling
- Pay only for execution time

Common Use Cases

- File processing
- Automation
- Scheduled jobs
- API backends

---

# 7. Infrastructure as Code (IaC)

Infrastructure as Code means managing infrastructure using code instead of manual configuration.

Example

Instead of:

```text
AWS Console

↓

Create EC2

↓

Configure Security Groups

↓

Create VPC
```

Use:

```text
Terraform Code

↓

terraform apply

↓

Infrastructure Created
```

---

# 8. Why IaC Matters

Manual infrastructure causes:

- Configuration drift
- Human errors
- Inconsistent environments
- Difficult disaster recovery

IaC solves these challenges by making infrastructure repeatable and version-controlled.

---

# 9. Benefits of IaC

- Version Control
- Repeatability
- Faster Provisioning
- Consistency
- Easier Rollback
- Disaster Recovery
- Code Review
- Automation
- Reduced Manual Errors

---

## Typical Workflow

```text
Developer

↓

Modify Terraform Code

↓

Git Push

↓

CI Pipeline

↓

terraform fmt

↓

terraform validate

↓

terraform plan

↓

Approval

↓

terraform apply
```

---

# 10. Common IaC Tools

| Tool               | Description                           |
| ------------------ | ------------------------------------- |
| Terraform          | Multi-cloud Infrastructure as Code    |
| AWS CloudFormation | AWS-native IaC                        |
| Pulumi             | IaC using programming languages       |
| Azure Bicep        | Azure-native IaC                      |
| Ansible            | Configuration Management & Automation |

---

## Why Terraform is Popular

- Multi-cloud support
- Declarative syntax
- Large provider ecosystem
- Reusable modules
- State management
- Strong community support

---

# 11. Common Interview Questions

### What is DevOps?

---

### What are the main DevOps principles?

---

### Difference between Continuous Delivery and Continuous Deployment?

---

### Why is automation important?

---

### Explain the CI/CD pipeline.

---

### What is Cloud Computing?

---

### Difference between EC2 and Lambda?

---

### What is VPC?

---

### Explain IAM.

---

### Difference between Security Groups and Network ACLs?

---

### What is Infrastructure as Code?

---

### Why should infrastructure be version controlled?

---

### Why is Terraform preferred over manual provisioning?

---

### Explain Terraform workflow.

---

### What are the benefits of Infrastructure as Code?

---

# 12. 5-Minute Revision

Remember these key points:

- **DevOps** → Collaboration + Automation + Continuous Delivery
- **CI** → Build and Test on every commit
- **Continuous Delivery** → Production-ready with manual approval
- **Continuous Deployment** → Automatic deployment to production
- **Cloud Computing** → On-demand infrastructure and services
- **EC2** → Virtual Machines
- **S3** → Object Storage
- **VPC** → Private Network
- **IAM** → Authentication & Authorization
- **RDS** → Managed Relational Database
- **Lambda** → Serverless Compute
- **Infrastructure as Code** → Manage infrastructure using code
- **Terraform** → Most popular multi-cloud IaC tool
- **Automation** → Faster, reliable, repeatable deployments

---

# Final Interview Tip

For DevOps interviews, don't just define concepts—**relate them to real-world usage**.

For example, instead of saying:

> "Terraform is an IaC tool."

Say:

> "In my projects, we used Terraform modules to provision AWS infrastructure, stored the state in an S3 backend with DynamoDB locking, and executed `terraform fmt`, `validate`, `plan`, and `apply` through GitLab CI/CD pipelines. This ensured version-controlled, repeatable, and production-ready infrastructure deployments."

This demonstrates both conceptual understanding and practical experience.
