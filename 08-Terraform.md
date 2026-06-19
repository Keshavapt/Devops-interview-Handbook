# 08-Terraform.md

# Terraform Interview Handbook

---

# What is Terraform?

Terraform is an Infrastructure as Code (IaC) tool that allows infrastructure to be defined, versioned, and managed using configuration files rather than manually creating resources through cloud consoles.

Interview Answer:

Terraform enables infrastructure to be treated like application code. Instead of manually creating VPCs, EC2 instances, databases, and Kubernetes clusters, we define them in code and Terraform automatically provisions and manages them.

---

# Why Terraform?

Traditional Infrastructure:

```text id="e1a8pw"
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

```text id="7yec9w"
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

```text id="1a4rqp"
Terraform Code
      ↓
Provider
      ↓
Cloud APIs
      ↓
Infrastructure
```

Example:

```text id="lf8myd"
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

```text id="gpn4qu"
AWS
Azure
OCI
Kubernetes
GitHub
Cloudflare
```

Example:

```terraform id="2jqv7n"
provider "aws" {
 region = "us-east-1"
}
```

Interview Answer:

Providers act as translators between Terraform and the target platform APIs.

---

# Terraform Workflow

One of the most common interview questions.

```text id="9t7z6y"
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

```bash id="f3mpd2"
terraform plan
```

Output:

```text id="8lvcqt"
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

```bash id="o8gn9m"
terraform apply
```

Interview Answer:

terraform apply compares desired state with actual state and performs the required changes to achieve the desired configuration.

---

# terraform destroy

Purpose:

Delete infrastructure.

Example:

```bash id="uymx0s"
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

```text id="4v0d2z"
terraform.tfstate
```

Purpose:

Stores resource metadata.

Interview Answer:

Terraform uses the state file to track infrastructure resources and understand what it currently manages. Without state information, Terraform cannot accurately determine infrastructure changes.

---

# Why Is State Important?

Example:

```text id="hgj7ow"
EC2 Instance
ID
IP Address
Metadata
```

All stored in state.

---

# Local State

Default Behavior:

```text id="pjq7jk"
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

```text id="vqk2e8"
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

```bash id="f3oh7n"
terraform apply
```

simultaneously.

Possible Result:

```text id="u1eguv"
State Corruption
```

---

Solution:

State Locking.

AWS Example:

```text id="4mxjlwm"
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

```text id="mnr9we"
EC2
Security Group
IAM Role
```

Create one module and reuse it.

---

Module Structure:

```text id="oj0v8o"
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

```text id="mz71pp"
Main Project
```

Child Module:

```text id="p5w8xm"
Reusable Component
```

Interview Answer:

The root module orchestrates infrastructure while child modules encapsulate reusable logic.

---

# variables.tf

Purpose:

Input variables.

Example:

```terraform id="7yz2r4"
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

```terraform id="g0x4sv"
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

```terraform id="g48l9f"
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

```terraform id="9v9hj4"
resource "aws_instance" "web" {
}
```

Interview Answer:

Resource blocks define the actual infrastructure components Terraform manages.

---

# count

Creates multiple similar resources.

Example:

```terraform id="m4lrbo"
count = 3
```

Creates:

```text id="i9gc4h"
EC2-1
EC2-2
EC2-3
```

---

# for_each

Creates resources from collections.

Example:

```terraform id="p1a3zr"
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

```text id="m5w1az"
Desired State
      vs
Actual State
```

and performs reconciliation.

---

# Lifecycle Block

Example:

```terraform id="mqrjfr"
lifecycle {
 prevent_destroy = true
}
```

Interview Answer:

Lifecycle settings help control how Terraform manages resources and protect critical infrastructure from accidental changes.

---

# Environment Management

Typical Structure:

```text id="iwg1h6"
dev
qa
prod
```

Example:

```text id="drbh8e"
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

```text id="h3r9lg"
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

```bash id="jjy73u"
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

```text id="5a4tib"
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
