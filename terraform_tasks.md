# Terraform Tasks (Hands-on & Interview Guide)

> **Interview Focus:**  
> In Terraform coding rounds, interviewers rarely ask you to write infrastructure from scratch. Instead, you'll receive partially completed code and be asked to complete, debug, optimize, or explain it. The emphasis is on understanding Infrastructure as Code (IaC), modular design, idempotency, and production best practices.

---

# Table of Contents

1. Read & Understand Existing Code
2. Configure Providers
3. Create VPC
4. Create Subnets
5. Configure Security Groups
6. Use Variables
7. Use tfvars Files
8. Resource References
9. Module Outputs
10. Configure Remote State
11. DynamoDB State Locking
12. Debug Common Terraform Issues
13. Idempotency
14. Modularity
15. Common Hands-on Tasks
16. Common Interview Questions
17. Production Best Practices

---

# 1. Read & Understand Existing Code

Interviewers often provide a partially completed project.

Typical Structure

```text
terraform/

├── main.tf
├── variables.tf
├── outputs.tf
├── provider.tf
├── terraform.tfvars
├── backend.tf
├── modules/
│   ├── vpc/
│   ├── ec2/
│   └── security-group/
```

Your tasks may include:

- Complete missing resources
- Fix syntax errors
- Add variables
- Connect modules
- Improve code quality

---

# Interview Task

Review a Terraform project and identify:

- Missing resources
- Incorrect references
- Hardcoded values
- Missing variables
- Security issues

---

# 2. Configure Providers

Terraform communicates with cloud providers through providers.

Example

```hcl
provider "aws" {
  region = var.aws_region
}
```

Initialize Provider

```bash
terraform init
```

Verify

```bash
terraform providers
```

---

# Interview Question

### Why do we use Providers?

### Answer

Providers allow Terraform to communicate with cloud platforms like AWS, Azure, and GCP, exposing resources and services that Terraform can manage.

---

# 3. Configure VPC

Typical Components

- VPC
- Public Subnets
- Private Subnets
- Internet Gateway
- NAT Gateway
- Route Tables
- Route Table Associations

Typical Flow

```text
VPC

↓

Subnets

↓

Internet Gateway

↓

Route Table

↓

EC2
```

---

# Interview Task

Complete a VPC configuration by adding:

- CIDR
- Public Subnets
- Route Tables
- Internet Gateway

---

# 4. Configure Subnets

Types

### Public Subnet

- Internet access
- Bastion Hosts
- Load Balancers

---

### Private Subnet

- Databases
- Backend Applications
- Internal Services

---

Interview Task

Create:

- Two Public Subnets
- Two Private Subnets

---

# 5. Configure Security Groups

Security Groups act as virtual firewalls.

Example Rules

Ingress

- HTTP (80)
- HTTPS (443)
- SSH (22)

Egress

- Allow outbound traffic

---

Interview Task

Allow:

- HTTP
- HTTPS

Restrict SSH access to a specific IP instead of `0.0.0.0/0`.

---

# 6. Use Variables

Avoid hardcoding values.

Example

Instead of

```hcl
instance_type = "t3.micro"
```

Use

```hcl
instance_type = var.instance_type
```

Declare Variable

```hcl
variable "instance_type" {
  type = string
}
```

---

Interview Task

Replace all hardcoded values with variables.

---

# 7. Use tfvars Files

Store environment-specific values.

Example

```hcl
aws_region = "us-east-1"

instance_type = "t3.micro"
```

Apply

```bash
terraform apply \
-var-file=terraform.tfvars
```

Benefits

- Environment separation
- Cleaner code
- Reusability

---

Interview Question

### Why use tfvars?

### Answer

It separates configuration values from Terraform code, making the infrastructure reusable across multiple environments.

---

# 8. Resource References

Terraform resources often depend on one another.

Example

Reference a VPC ID

```hcl
vpc_id = aws_vpc.main.id
```

Reference a Subnet

```hcl
subnet_id = aws_subnet.public.id
```

Reference Security Group

```hcl
vpc_security_group_ids = [
  aws_security_group.web.id
]
```

---

Interview Task

Replace hardcoded IDs with resource references.

---

# 9. Module Outputs

Modules expose values using outputs.

Example

Module

```hcl
output "vpc_id" {
  value = aws_vpc.main.id
}
```

Use Output

```hcl
module.vpc.vpc_id
```

Benefits

- Loose coupling
- Reusability
- Cleaner architecture

---

Interview Task

Connect the EC2 module to the VPC module using outputs.

---

# 10. Configure Remote State

Never store state locally in production.

Example Backend

```hcl
terraform {
  backend "s3" {
    bucket = "company-terraform-state"
    key    = "network/terraform.tfstate"
    region = "us-east-1"
  }
}
```

Initialize

```bash
terraform init
```

Benefits

- Team collaboration
- Version control
- Centralized state

---

Interview Question

### Why use remote state?

### Answer

Remote state enables team collaboration, centralized infrastructure management, backup, and prevents inconsistencies caused by local state files.

---

# 11. DynamoDB State Locking

Purpose

Prevent multiple users from modifying infrastructure simultaneously.

Architecture

```text
Terraform

↓

S3 Backend

↓

DynamoDB Lock

↓

AWS Infrastructure
```

Benefits

- Prevents concurrent updates
- Protects state consistency

---

Interview Question

### Why is state locking important?

### Answer

State locking prevents simultaneous Terraform operations that could corrupt the state file or create inconsistent infrastructure.

---

# 12. Debug Common Terraform Issues

### Syntax Errors

Validate

```bash
terraform validate
```

---

### Formatting Issues

```bash
terraform fmt
```

---

### Initialization Errors

```bash
terraform init
```

---

### Plan Before Apply

```bash
terraform plan
```

---

### Refresh State

```bash
terraform refresh
```

---

### Inspect State

```bash
terraform state list
```

---

### Common Problems

- Missing variables
- Incorrect references
- Circular dependencies
- Backend configuration errors
- Invalid provider configuration
- Resource drift
- Permission issues
- Locked state

---

# 13. Idempotency

Terraform is declarative and idempotent.

Meaning

Running

```bash
terraform apply
```

multiple times should produce the same infrastructure if no configuration changes have been made.

Example

```text
terraform apply

↓

Infrastructure Created

↓

terraform apply

↓

No Changes
```

Benefits

- Predictable deployments
- Safe automation
- Consistent infrastructure

---

Interview Question

### What is Idempotency?

### Answer

Idempotency means applying the same Terraform configuration multiple times results in the same infrastructure state without creating duplicate resources.

---

# 14. Modularity

Instead of placing everything in one file:

```text
main.tf
```

Split into modules.

Example

```text
modules/

vpc/

ec2/

alb/

security-group/

iam/
```

Benefits

- Reusability
- Easier maintenance
- Team collaboration
- Standardization

---

Interview Question

### Why use Terraform Modules?

### Answer

Modules promote reusable, maintainable, and standardized infrastructure components that can be shared across environments and projects.

---

# 15. Common Hands-on Interview Tasks

### Task 1

Complete Provider configuration.

---

### Task 2

Create a VPC.

---

### Task 3

Add Public and Private Subnets.

---

### Task 4

Create Security Groups.

---

### Task 5

Launch an EC2 instance.

---

### Task 6

Replace hardcoded values with variables.

---

### Task 7

Create and use a `terraform.tfvars` file.

---

### Task 8

Reference resources instead of hardcoded IDs.

---

### Task 9

Connect two modules using outputs.

---

### Task 10

Configure an S3 backend for remote state.

---

### Task 11

Enable DynamoDB state locking.

---

### Task 12

Run:

```bash
terraform fmt
terraform validate
terraform plan
```

Explain the purpose of each command.

---

### Task 13

Debug a failed `terraform apply`.

---

### Task 14

Identify and remove duplicate resources.

---

### Task 15

Review a Terraform project and recommend production improvements.

---

# 16. Common Interview Questions with Answers

### Q1. Why use Terraform instead of manually creating AWS resources?

**Answer:**  
Terraform provides version-controlled, repeatable, and automated infrastructure provisioning. It reduces manual errors, supports code reviews, and integrates with CI/CD pipelines.

---

### Q2. What is the difference between `terraform plan` and `terraform apply`?

**Answer:**

- **terraform plan** → Shows the proposed infrastructure changes without making any modifications.
- **terraform apply** → Executes the planned changes and updates the infrastructure.

---

### Q3. Why should you use Variables?

**Answer:**  
Variables improve reusability, avoid hardcoding, and make it easy to manage different environments such as development, testing, and production.

---

### Q4. What is the purpose of `terraform.tfvars`?

**Answer:**  
It stores environment-specific variable values separately from the Terraform code, allowing the same codebase to be reused across multiple environments.

---

### Q5. Why use Modules?

**Answer:**  
Modules improve code reuse, simplify maintenance, standardize infrastructure, and reduce duplication.

---

### Q6. What is Terraform State?

**Answer:**  
Terraform State is a file that records the current infrastructure managed by Terraform. It maps Terraform resources to real cloud resources and helps Terraform determine what changes are needed.

---

### Q7. Why use Remote State?

**Answer:**  
Remote state allows teams to collaborate safely, centralizes state storage, enables backups, and avoids inconsistencies caused by local state files.

---

### Q8. Why use DynamoDB with S3?

**Answer:**  
DynamoDB provides state locking, preventing multiple users from applying Terraform changes simultaneously and protecting the integrity of the state file.

---

### Q9. What is Idempotency?

**Answer:**  
Idempotency means repeatedly applying the same Terraform configuration produces the same infrastructure state without creating duplicate resources.

---

### Q10. How would you debug a failed Terraform deployment?

**Answer:**

1. Run `terraform validate` to check syntax.
2. Run `terraform fmt` for formatting.
3. Run `terraform plan` to review proposed changes.
4. Check provider configuration and credentials.
5. Inspect Terraform state with `terraform state list`.
6. Review error messages and cloud provider logs.
7. Resolve any dependency or permission issues.

---

# 17. Production Best Practices

- Always use **remote state** (S3 backend).
- Enable **DynamoDB state locking**.
- Store Terraform code in **Git**.
- Use **modules** for reusable infrastructure.
- Replace hardcoded values with **variables**.
- Use **terraform.tfvars** for environment-specific values.
- Run `terraform fmt` before committing code.
- Validate using `terraform validate`.
- Review changes with `terraform plan` before applying.
- Protect production branches with code reviews.
- Never store secrets in Terraform code.
- Use IAM roles and secret management services.
- Integrate Terraform into CI/CD pipelines.
- Apply the **Principle of Least Privilege** for IAM permissions.
- Tag all cloud resources consistently for governance and cost management.

---

# Final Interview Tip

In Terraform coding interviews, explain your reasoning as you work.

For example:

> "I'll first run `terraform fmt` to ensure consistent formatting, then `terraform validate` to catch syntax errors. Next, I'll review the execution plan with `terraform plan` before applying changes. I'll replace hardcoded values with variables, reference resources instead of IDs, use module outputs for dependencies, and ensure the backend uses an S3 bucket with DynamoDB locking for collaborative, production-ready state management."

This demonstrates not just Terraform syntax knowledge but also a production-focused engineering mindset.

## Task 1 – Complete Provider Configuration

### Interview Task

Complete the provider configuration using variables.

---

### Expected Solution

```hcl
provider "aws" {
  region = var.aws_region
}
```

Variable

```hcl
variable "aws_region" {
  description = "AWS Region"
  type        = string
}
```

terraform.tfvars

```hcl
aws_region = "us-east-1"
```

---

### Why?

- Avoid hardcoding values.
- Support multiple environments.
- Improve reusability.

---

### Possible Interview Questions

Q: Why shouldn't we hardcode the region?

A:
Different environments may use different AWS regions. Variables make the code reusable and easier to maintain.

---

Q: Where would you define production values?

A:

terraform.tfvars

or

environment-specific tfvars files.

---

Q: What happens if no value is provided?

A:

Terraform prompts for input unless a default value is defined.
