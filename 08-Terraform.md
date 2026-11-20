# Terraform Interview Handbook

> **Target Audience:** 5+ Years Experience
> **Role:** Senior DevOps / Platform Engineer
> **Interview Weightage:** ⭐⭐⭐⭐⭐

---

## Table of Contents

- [1. What is Terraform?](#1-what-is-terraform)
- [2. Why Terraform?](#2-why-terraform)
- [3. Infrastructure as Code (IaC)](#3-infrastructure-as-code-iac)
- [4. Terraform Architecture](#4-terraform-architecture)
- [5. Provider](#5-provider)
- [6. Terraform Workflow](#6-terraform-workflow)
- [7. Core Commands](#7-core-commands)
- [8. State Management](#8-state-management)
- [9. Remote Backend & State Locking](#9-remote-backend--state-locking)
- [10. Modules](#10-modules)
- [11. Variables & Outputs](#11-variables--outputs)
- [12. Multi-Environment Directory Structure](#12-multi-environment-directory-structure)
- [13. Terraform in CI/CD](#13-terraform-in-cicd)
- [14. terraform import, Drift & Provisioners](#14-terraform-import-drift--provisioners)
- [15. Common Interview Scenarios](#15-common-interview-scenarios)
- [16. Honest Interview Positioning](#16-honest-interview-positioning)
- [17. Most Asked Interview Questions](#17-most-asked-interview-questions)
- [18. 5-Minute Quick Revision Table](#18-5-minute-quick-revision-table)

---

# What is Terraform?

Terraform is an Infrastructure as Code (IaC) tool that allows infrastructure to be defined, versioned, and managed using configuration files rather than manually creating resources through cloud consoles.

Interview Answer:

Terraform enables infrastructure to be treated like application code. Instead of manually creating VPCs, EC2 instances, databases, and Kubernetes clusters, we define them in code and Terraform automatically provisions and manages them.

---

# Why Terraform?

Traditional Infrastructure:

``` 
Login AWS
Create VPC
Create EC2
Create Security Groups
Create Load Balancer
```

Problems:

- Manual effort
- Human errors
- No version control
- Difficult rollback

---

Terraform Approach:

``` 
terraform apply
```

Infrastructure is created automatically.

Interview Answer:

Terraform provides consistency, repeatability, automation, version control, and easier disaster recovery compared to manual infrastructure provisioning.

---

# Infrastructure as Code (IaC)

Infrastructure as Code means infrastructure is managed through code files stored in version control systems.

Interview Answer:

Just like developers manage application code in Git repositories, DevOps teams manage infrastructure definitions using Terraform configuration files.

---

# Terraform Architecture

``` 
Terraform Code
      ↓
Provider
      ↓
Cloud APIs
      ↓
Infrastructure
```

Example:

``` 
Terraform
      ↓
AWS Provider
      ↓
AWS Resources
```

---

# Provider

Providers allow Terraform to communicate with external platforms.

Examples:

``` 
AWS
Azure
OCI
Kubernetes
GitHub
Cloudflare
```

Example:

```hcl 
provider "aws" {
 region = "us-east-1"
}
```

Interview Answer:

Providers act as translators between Terraform and the target platform APIs.

---

# Terraform Workflow

One of the most common interview questions.

``` 
terraform init
terraform plan
terraform apply
terraform destroy
```

---

# terraform init

Purpose:

Initialize the working directory.

What Happens:

- Downloads providers
- Downloads modules
- Prepares backend

Interview Answer:

terraform init is usually the first command executed in a new Terraform project and prepares the environment for future operations.

---

# terraform plan

Purpose:

Preview changes.

Example:

```bash 
terraform plan
```

Output:

``` 
+ Create EC2
+ Create Security Group
```

Interview Answer:

terraform plan allows teams to review infrastructure changes before applying them.

---

# terraform apply

Purpose:

Create or modify infrastructure.

Example:

```bash 
terraform apply
```

Interview Answer:

terraform apply compares desired state with actual state and performs the required changes to achieve the desired configuration.

---

# terraform destroy

Purpose:

Delete infrastructure.

Example:

```bash 
terraform destroy
```

Interview Answer:

terraform destroy removes all resources managed by Terraform within the current state.

---

# What Happens During terraform apply?

This is a favorite interview question.

Interview Answer:

Terraform first reads configuration files, loads providers, retrieves the current infrastructure state, compares actual resources with desired resources, generates an execution plan, and then performs the required create, update, or delete operations through cloud provider APIs.

---

# State File

One of the most important Terraform concepts.

File:

``` 
terraform.tfstate
```

Purpose:

Stores resource metadata.

Interview Answer:

Terraform uses the state file to track infrastructure resources and understand what it currently manages. Without state information, Terraform cannot accurately determine infrastructure changes.

---

# Why Is State Important?

Example:

``` 
EC2 Instance
ID
IP Address
Metadata
```

All stored in state.

---

# Local State

Default Behavior:

``` 
terraform.tfstate
```

stored locally.

Problems:

- No collaboration
- Risk of corruption
- No locking
- No backup

---

# Remote Backend

Stores state centrally.

Examples:

``` 
AWS S3
Terraform Cloud
Azure Storage
```

Interview Answer:

Production environments should use remote backends because multiple engineers and CI/CD systems may interact with the same infrastructure.

---

# State Locking

Problem:

Two engineers run:

```bash 
terraform apply
```

simultaneously.

Possible Result:

``` 
State Corruption
```

---

Solution:

State Locking.

AWS Example:

``` 
S3
 +
DynamoDB
```

Interview Answer:

State locking prevents concurrent infrastructure modifications and protects state consistency.

---

# Modules

Modules are reusable Terraform components.

Example:

Instead of creating EC2 infrastructure repeatedly:

``` 
EC2
Security Group
IAM Role
```

Create one module and reuse it.

---

Module Structure:

``` 
modules/
└── ec2
    ├── main.tf
    ├── variables.tf
    └── outputs.tf
```

---

Interview Answer:

Modules improve reusability, standardization, maintainability, and reduce code duplication.

---

# Root Module vs Child Module

Root Module:

``` 
Main Project
```

Child Module:

``` 
Reusable Component
```

Interview Answer:

The root module orchestrates infrastructure while child modules encapsulate reusable logic.

---

# variables.tf

Purpose:

Input variables.

Example:

```hcl 
variable "instance_type" {
 default = "t3.micro"
}
```

Interview Answer:

Variables make Terraform code reusable across multiple environments.

---

# outputs.tf

Purpose:

Expose resource values.

Example:

```hcl 
output "instance_ip" {
 value = aws_instance.web.public_ip
}
```

Interview Answer:

Outputs allow resources created by Terraform to be consumed by users, pipelines, or other modules.

---

# main.tf

Purpose:

Contains actual infrastructure definitions.

Interview Answer:

main.tf is usually the primary file where resources, modules, and infrastructure logic are defined.

---

# Terraform Data Sources

Purpose:

Read existing infrastructure.

Example:

```hcl 
data "aws_vpc" "default" {
 default = true
}
```

Interview Answer:

Data sources allow Terraform to reference resources it did not create.

---

# Resource Block

Creates infrastructure.

Example:

```hcl 
resource "aws_instance" "web" {
}
```

Interview Answer:

Resource blocks define the actual infrastructure components Terraform manages.

---

# count

Creates multiple similar resources.

Example:

```hcl 
count = 3
```

Creates:

``` 
EC2-1
EC2-2
EC2-3
```

---

# for_each

Creates resources from collections.

Example:

```hcl 
for_each = {
 dev = "t3.micro"
 prod = "t3.large"
}
```

Interview Answer:

for_each provides more flexibility than count because resources are created using meaningful keys.

---

# count vs for_each

Interview Answer:

count works well for identical resources, while for_each is preferred when resources require unique configurations.

---

# Terraform Lifecycle

Terraform compares:

``` 
Desired State
      vs
Actual State
```

and performs reconciliation.

---

# Lifecycle Block

Example:

```hcl 
lifecycle {
 prevent_destroy = true
}
```

Interview Answer:

Lifecycle settings help control how Terraform manages resources and protect critical infrastructure from accidental changes.

---

# Environment Management

Typical Structure:

``` 
dev
qa
prod
```

Example:

``` 
environments/
├── dev
├── qa
└── prod
```

Interview Answer:

Each environment should maintain separate state files and variable configurations to prevent accidental cross-environment changes.

---

# Terraform in CI/CD

Typical Flow:

``` 
Git Commit
      ↓
Pipeline
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

Interview Answer:

Terraform pipelines generally separate planning from applying changes and often require approval before production modifications.

---

# Common Terraform Commands

```bash 
terraform init
terraform fmt
terraform validate
terraform plan
terraform apply
terraform destroy
terraform output
terraform state list
```

---

# terraform import

Import existing infrastructure into Terraform management.

```bash
# Define the resource in config first, then:
terraform import aws_instance.web i-0123456789abcdef0
```

**Steps:**
1. Define the resource in Terraform configuration
2. Run `terraform import`
3. Verify using `terraform plan`
4. Update the configuration if needed to match the imported resource

---

# Configuration Drift

Drift occurs when real-world infrastructure differs from Terraform state (manual console changes, out-of-band updates).

**Detection:** `terraform plan` shows differences between config/state and real infrastructure.

**Remediation:** Update Terraform config to match reality, or run `terraform apply` to bring real infrastructure back in line with config.

---

# Provisioners vs Ansible

| Terraform Provisioners | Ansible |
| ---------------------- | ------- |
| Run during resource creation | Run independently of Terraform |
| Tied to resource lifecycle | Standalone configuration management |
| Use when no better option exists | Preferred for config management |
| `file`, `remote-exec`, `local-exec` | Playbooks, roles, modules |

**Best practice:** Avoid provisioners when possible. Use Terraform for provisioning, Ansible for configuration management.

---

# Common Interview Scenarios

## Terraform Apply Failed

Interview Answer:

I would first review the Terraform error output, verify provider authentication, check state consistency, validate resource dependencies, and confirm cloud service quotas or permissions.

---

## State File Deleted

Interview Answer:

If remote state backups exist, I would restore the state. Without state information, Terraform may attempt to recreate existing infrastructure and manual recovery may be required.

---

## Two Engineers Run Apply Together

Interview Answer:

This is why state locking exists. Production environments should always use remote state with locking enabled.

---

## Lost Generated Password

Question:

Terraform generated an RDS password and you forgot it.

Interview Answer:

Terraform stores generated values in the state file. If state access exists, the password may still be retrievable. This is one reason state files must be secured because they can contain sensitive information.

---

# Honest Interview Positioning

For Your Profile

Interview Answer:

My production experience is primarily OCI-focused. For Terraform, I have completed hands-on labs and personal projects covering providers, modules, state management, remote backends, variables, outputs, and infrastructure provisioning workflows. While I have not yet managed Terraform in a production environment, I understand its architecture and operational practices.

---

# Most Asked Interview Questions

1. What is Terraform?
2. Why Terraform?
3. What is IaC?
4. Explain Terraform workflow.
5. What happens during terraform apply?
6. What is a provider?
7. What is state file?
8. Why use remote backend?
9. What is state locking?
10. S3 + DynamoDB architecture?
11. What is a module?
12. Root vs child module?
13. variables.tf?
14. outputs.tf?
15. main.tf?
16. count vs for_each?
17. Data source vs resource?
18. Terraform in CI/CD?
19. How do you manage environments?
20. How do you troubleshoot Terraform failures?

---

# Terraform Topics To Focus On

``` 
Terraform Workflow
State File
Remote Backend
State Locking
Modules
Variables
Outputs
Data Sources
Resources
Count
For_Each
CI/CD Integration
Environment Management
```

These topics alone cover nearly 80% of Terraform interview discussions for DevOps Engineer and Platform Engineer roles.

---

## 5-Minute Quick Revision Table

| Topic | Key Point |
| ----- | --------- |
| Terraform | Infrastructure as Code tool |
| IaC | Infrastructure managed via code |
| Workflow | init → validate → plan → apply |
| State File | Source of truth for resources |
| Remote Backend | Centralized state (S3, GCS, etc.) |
| State Locking | Prevents concurrent applies (DynamoDB) |
| terraform import | Bring existing resources under management |
| Drift | Real infra differs from state/config |
| Provisioners | Avoid; use Ansible for config mgmt |
| Modules | Reusable infrastructure components |
| Multi-env | Separate state + tfvars per environment |
| OCI Positioning | Production OCI; hands-on Terraform labs |
| Sensitive | Mark secrets as sensitive = true |
| Backend | Never use local state in teams |
| Providers | AWS, Azure, OCI, Kubernetes, etc. |
| Plan | Always review before apply |
| CI/CD | fmt → validate → plan → approval → apply |
