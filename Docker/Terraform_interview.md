# Terraform Interview Crash Course

> **Goal:** Crack Terraform interview questions for DevOps Engineers (2–8 Years Experience)

---

# 1. What is Terraform?

## Definition

Terraform is an open-source **Infrastructure as Code (IaC)** tool developed by HashiCorp that allows you to provision, update, and manage infrastructure using declarative configuration files.

Instead of creating infrastructure manually through a cloud console, Terraform automates the process using code.

---

## Why Terraform?

Without Terraform

```
AWS Console

↓

Create EC2

↓

Create VPC

↓

Create Security Group

↓

Create IAM

↓

Repeat for every environment
```

Manual, error-prone and difficult to reproduce.

With Terraform

```
terraform apply

↓

Entire Infrastructure Created
```

Infrastructure becomes version-controlled and repeatable.

---

## Real-world Example

Instead of manually creating:

- VPC
- Subnets
- Internet Gateway
- Route Tables
- EC2
- Security Groups
- Load Balancer

One Terraform command creates everything.

```bash
terraform apply
```

---

# 2. Why Terraform?

Terraform provides

- Infrastructure as Code
- Automation
- Version Control
- Repeatability
- Idempotency
- Multi-cloud Support
- Faster Provisioning
- Collaboration

---

# 3. Terraform Architecture

```
Developer

↓

Terraform CLI

↓

Terraform Core

↓

Provider

↓

AWS / Azure / GCP / OCI

↓

Infrastructure
```

---

## Components

### Terraform Core

Responsible for

- Reading configuration
- Creating execution plan
- Managing state
- Dependency graph

---

### Providers

Providers communicate with cloud platforms.

Examples

```
AWS

Azure

Google Cloud

Oracle Cloud

Kubernetes

GitHub
```

---

### Resources

Resources represent infrastructure.

Example

```hcl
resource "aws_instance" "web" {

}
```

Every cloud object is represented as a resource.

---

# 4. Terraform Workflow

```
Write Code

↓

terraform init

↓

terraform validate

↓

terraform plan

↓

terraform apply

↓

Infrastructure Created
```

Destroy

```
terraform destroy
```

---

# 5. Important Terraform Files

## main.tf

Contains infrastructure resources.

---

## variables.tf

Contains variable definitions.

---

## terraform.tfvars

Stores actual values for variables.

---

## outputs.tf

Displays useful outputs.

Example

```
EC2 Public IP

Load Balancer DNS

VPC ID
```

---

## provider.tf

Defines cloud provider.

Example

```hcl
provider "aws" {
  region = "ap-south-1"
}
```

---

# 6. Terraform State

## What is Terraform State?

Terraform keeps track of infrastructure using a **State File**.

```
terraform.tfstate
```

Without state,

Terraform doesn't know what resources already exist.

---

## Why State?

Suppose

```
EC2

VPC

Subnet

ALB
```

Already exist.

Terraform reads the state file to compare:

Desired Infrastructure

vs

Current Infrastructure

---

## Interview Question

Why is Terraform State important?

Answer

It maps Terraform configuration to real infrastructure, allowing Terraform to detect changes and perform incremental updates instead of recreating everything.

---

# Local State vs Remote State

## Local

```
terraform.tfstate
```

Stored locally.

Not suitable for teams.

---

## Remote

Stored in

- AWS S3
- Azure Storage
- GCS
- Terraform Cloud

Often combined with DynamoDB (AWS) for state locking.

Production always uses Remote State.

---

# 7. Terraform Commands

Initialize

```bash
terraform init
```

Format

```bash
terraform fmt
```

Validate

```bash
terraform validate
```

Plan

```bash
terraform plan
```

Apply

```bash
terraform apply
```

Destroy

```bash
terraform destroy
```

Show State

```bash
terraform show
```

List Resources

```bash
terraform state list
```

---

# 8. Variables

Variables make code reusable.

Example

```hcl
variable "instance_type" {
  default = "t3.micro"
}
```

Use

```hcl
instance_type = var.instance_type
```

---

# 9. Outputs

Outputs display useful values.

Example

```hcl
output "public_ip" {
  value = aws_instance.web.public_ip
}
```

Output

```
13.xx.xx.xx
```

---

# 10. Modules

Modules are reusable Terraform code.

Example

```
VPC Module

↓

EC2 Module

↓

ALB Module
```

Benefits

- Reusability
- Standardization
- Easy Maintenance

---

# 11. Lifecycle

Useful lifecycle options

```hcl
lifecycle {
  create_before_destroy = true
}
```

Other options

- prevent_destroy
- ignore_changes

---

# 12. Data Sources

Resources create infrastructure.

Data Sources read existing infrastructure.

Example

```hcl
data "aws_vpc" "default" {

}
```

Interview Question

Resource vs Data Source?

Resource

Creates infrastructure.

Data Source

Reads existing infrastructure.

---

# 13. Provisioners

Examples

```hcl
local-exec

remote-exec
```

Used for post-deployment tasks.

Interview Tip

Provisioners should be avoided when possible.

Use configuration management tools like Ansible instead.

---

# 14. Terraform CI/CD Flow

```
Developer

↓

Git Push

↓

GitLab CI

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

↓

Infrastructure Updated
```

---

# 15. Best Practices

✅ Remote State

✅ State Locking

✅ Modules

✅ Variables

✅ Separate Dev/QA/Prod

✅ Version Pinning

✅ Small Reusable Modules

✅ Never Commit Secrets

---

# Interview Questions

### What is Terraform?

Infrastructure as Code tool.

---

### Why Terraform?

Automates infrastructure creation using code.

---

### What is Infrastructure as Code?

Managing infrastructure through code instead of manual configuration.

---

### Terraform vs CloudFormation?

Terraform

- Multi-cloud
- Open Source

CloudFormation

- AWS only

---

### What is Terraform State?

Maps Terraform configuration to real infrastructure.

---

### Why Remote State?

Enables collaboration and centralized state management.

---

### Why use DynamoDB with S3?

Provides **state locking** to prevent multiple users from modifying the same infrastructure simultaneously.

---

### Difference between Resource and Module?

Resource

Single infrastructure object.

Module

Collection of reusable resources.

---

### Difference between Resource and Data Source?

Resource

Creates infrastructure.

Data Source

Reads existing infrastructure.

---

### terraform plan vs terraform apply?

Plan

Shows proposed changes.

Apply

Executes those changes.

---

### terraform init?

Downloads providers and initializes the working directory.

---

### terraform validate?

Checks Terraform configuration syntax.

---

### terraform fmt?

Formats Terraform code according to standard style.

---

### Why use Variables?

To make Terraform code reusable across environments.

---

### What are Outputs?

Display values such as public IPs, DNS names, or resource IDs after deployment.

---

### What are Modules?

Reusable building blocks for Terraform configurations.

---

# 5-Minute Revision

- Terraform = Infrastructure as Code
- Provider = AWS/Azure/GCP Plugin
- Resource = Infrastructure Object
- Module = Reusable Code
- State = Infrastructure Mapping
- init = Initialize
- fmt = Format
- validate = Syntax Check
- plan = Preview Changes
- apply = Create/Update Infrastructure
- destroy = Delete Infrastructure
- Variables = Reusable Values
- Outputs = Display Values
- Data Source = Read Existing Infrastructure
- Remote State = Team Collaboration
- DynamoDB = State Locking

---

# Advanced & Experience-Based Interview Questions

## Q1. Explain the complete Terraform workflow.

**Answer:**

The typical Terraform workflow is:

```
Write Configuration

↓

terraform init

↓

terraform fmt

↓

terraform validate

↓

terraform plan

↓

Review Changes

↓

terraform apply

↓

Infrastructure Created/Updated

↓

terraform destroy (if required)
```

- **terraform init**: Initializes the working directory and downloads the required providers and modules.
- **terraform fmt**: Formats the Terraform code according to standard conventions.
- **terraform validate**: Validates the syntax and configuration.
- **terraform plan**: Shows the execution plan without making any changes.
- **terraform apply**: Applies the planned changes to create or update infrastructure.
- **terraform destroy**: Removes all managed infrastructure.

---

## Q2. What is Terraform State, and why is it important?

**Answer:**

Terraform State is a mapping between the Terraform configuration and the actual infrastructure.

Terraform stores this information in a **terraform.tfstate** file.

The state file enables Terraform to:

- Track existing resources.
- Detect configuration drift.
- Determine what needs to be created, modified, or destroyed.
- Manage dependencies efficiently.

Without the state file, Terraform cannot accurately manage existing infrastructure.

---

## Q3. How do you manage Terraform State in a team environment?

**Answer:**

In production, we never use a local state file.

A common approach is:

- Store the state file in an **AWS S3 bucket**.
- Use **DynamoDB** for state locking.
- Enable versioning on the S3 bucket for recovery.
- Restrict access using IAM policies.

Benefits:

- Shared state across the team.
- Prevents simultaneous updates.
- Centralized and secure storage.
- State version history.

Example:

```
Developer

↓

GitLab Pipeline

↓

S3 Backend

↓

DynamoDB Lock

↓

AWS Resources
```

---

## Q4. What happens if someone manually changes an AWS resource created by Terraform?

**Answer:**

This is called **Configuration Drift** or **State Drift**.

Example:

Terraform creates:

```
EC2 Instance
Type = t3.micro
```

Someone manually changes it in the AWS Console to:

```
t3.large
```

The next `terraform plan` detects the difference between the desired configuration and the actual infrastructure.

Terraform will propose changes to bring the infrastructure back to the desired state unless the configuration has been updated.

---

## Q5. How do you organize Terraform code for multiple environments?

**Answer:**

A common structure is:

```
terraform/

├── modules/
│   ├── vpc/
│   ├── ec2/
│   └── alb/
│
├── environments/
│   ├── dev/
│   ├── qa/
│   ├── stage/
│   └── prod/
```

Each environment has:

- Different variable values.
- Separate state files.
- Independent deployments.

This improves maintainability and reduces the risk of affecting production unintentionally.

---

## Q6. How do you organize Terraform Modules?

**Answer:**

Modules are reusable building blocks.

Example:

```
Root Module

↓

VPC Module

↓

Subnet Module

↓

EC2 Module

↓

ALB Module

↓

RDS Module
```

Benefits:

- Reusability.
- Standardization.
- Easier maintenance.
- Reduced code duplication.

---

## Q7. How do you secure secrets in Terraform?

**Answer:**

Secrets should never be hardcoded into Terraform code or committed to Git.

Best practices include:

- AWS Secrets Manager.
- HashiCorp Vault.
- Environment variables.
- CI/CD secret management (GitLab CI Variables, GitHub Secrets).

Sensitive values should be marked as `sensitive` where applicable and excluded from version control.

---

## Q8. How do you import existing infrastructure into Terraform?

**Answer:**

Terraform can start managing resources that already exist using the `terraform import` command.

Example:

```bash
terraform import aws_instance.web i-0123456789abcdef0
```

Steps:

1. Define the resource in the Terraform configuration.
2. Run `terraform import`.
3. Verify using `terraform plan`.
4. Update the configuration if needed to match the imported resource.

---

## Q9. Describe how you've used Terraform in your CI/CD pipeline.

**Sample Answer:**

In our GitLab CI pipeline:

1. Developers push code to GitLab.
2. The pipeline runs:
   - `terraform fmt`
   - `terraform validate`
   - `terraform plan`
3. The plan is reviewed.
4. After approval, `terraform apply` is executed.
5. The state is stored remotely in an S3 backend with DynamoDB state locking.

This ensures infrastructure changes are reviewed, version-controlled, and applied consistently.

---

## Q10. What challenges have you faced while using Terraform?

**Sample Answer:**

Some common challenges include:

- State file conflicts.
- Manual changes causing state drift.
- Provider version compatibility issues.
- Managing multiple environments.
- Handling secrets securely.

These were addressed by:

- Using remote state with locking.
- Enforcing code reviews.
- Pinning provider versions.
- Using reusable modules.
- Integrating Terraform into CI/CD pipelines.

---

# Interview Tips

- Always mention **Infrastructure as Code (IaC)** early in your answers.
- Explain **why** a feature is used, not just **what** it is.
- When discussing team usage, mention **Remote State (S3)** and **State Locking (DynamoDB)**.
- Emphasize **modules**, **version control**, and **CI/CD integration**.
- If asked about real-world experience, describe the workflow you followed rather than just listing Terraform commands.
