# Terraform Tasks (Hands-on & Interview Guide)

> **Interview Focus:** In Terraform coding rounds, interviewers rarely ask you to write infrastructure from scratch. Instead, you'll receive partially completed code and be asked to complete, debug, optimize, or explain it. The emphasis is on understanding Infrastructure as Code (IaC), modular design, idempotency, and production best practices.

--

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
--

--

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

--

# Interview Task

Review a Terraform project and identify:

- Missing resources
- Incorrect references
- Hardcoded values
- Missing variables
- Security issues

--

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

--

# Interview Question

### Why do we use Providers?

### Answer

Providers allow Terraform to communicate with cloud platforms like AWS, Azure, and GCP, exposing resources and services that Terraform can manage.

--

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

Create a VPC configuration by adding:

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

### Private Subnet

- Databases
- Backend Applications
- Internal Services

---

# Interview Task

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

# Interview Task

Allow:

- ...

--

# 5. Use Variables

Terraform variables allow you to parameterize your configuration.

Example

```hcl
variable "aws_region" {
  description = "AWS Region"
  type        = string
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

--

# Interview Question

### Why do we use Providers?

### Answer

Providers allow Terraform to communicate with cloud platforms like AWS, Azure, and GCP, exposing resources and services that Terraform can manage.

--

---

# 6. Use tfvars Files

Terraform uses .tfvars files to set values for variables.

Example

```hcl
variable "aws_region" {
  description = "AWS Region"
  type        = string
}

terraform.tfvars

aws_region = "us-east-1"
```

---

# 8. Resource References

Resource references allow you to use resources in one module in another module.

Example

```hcl
resource "aws_instance" "web" {
  instance_id = aws_instance.example.id
}
```

---

# Interview Question

### Why do we use Resource References?

### Answer

Resource references allow you to reuse resources across modules and configurations.

--

---

# 9. Module Outputs

Module outputs allow you to export values from a module for use in other modules.

Example

```hcl
output "vpc_cidr" {
  value = module.vpc.vpc_cidr
}
```

---

# Interview Question

### What are module outputs used for?

### Answer

Module outputs allow you to share configuration values between modules, such as VPC CIDR blocks, subnet IDs, and security group IDs.

--

---

# 10. Configure Remote State

Terraform state stores information about the infrastructure Terraform manages.

Example

```hcl
terraform {
  backend "s3" {
    bucket         = "my-tf-state"
    key            = "prod/terraform.tfstate"
    region         = "us-east-1"
  }
}
```

---

# Interview Question

### Why use remote state?

### Answer

Remote state allows team collaboration, enables state locking, and prevents inconsistencies caused by local state files.

--

---

# 11. DynamoDB State Locking

Terraform state locking prevents multiple users from applying Terraform changes simultaneously and protects the integrity of the state file.

Example

```hcl
terraform {
  backend "s3" {
    bucket         = "my-tf-state"
    key            = "prod/terraform.tfstate"
    region         = "us-east-1"
    dynamodb_table = "tf-locks"
  }
}
```

---

# Interview Question

### Why use DynamoDB with S3?

### Answer

DynamoDB provides state locking, preventing multiple users from applying Terraform changes simultaneously and protecting the integrity of the state file.

--

---

# 12. Debug Common Terraform Issues

Common Terraform issues and how to debug them.

--

---

# 13. Idempotency

Idempotency means repeatedly applying the same Terraform configuration produces the same infrastructure state without creating duplicate resources.

--

---

# 14. Modularity

Modularity improves reusability, standardization, and maintainability of Terraform code.

--

---

# 15. Common Hands-on Tasks

## Task 1 – Read & Understand Existing Code

Interviewers often provide a partially completed project.

Your tasks may include:

- Complete missing resources
- Fix syntax errors
- Add variables
- Connect modules
- Improve code quality

--

## Task 2 – Configure Providers

Complete the provider configuration using variables.

--

## Task 3 – Create VPC

Create a VPC configuration by adding CIDR, public subnets, and route tables.

--

## Task 4 – Configure Security Groups

Create security groups allowing HTTP/HTTPS ingress and allowing outbound traffic.

--

## Task 5 – Use Variables

Complete the variable configuration using variables and tfvars files.

--

## Task 6 – Use tfvars Files

Use terraform.tfvars for environment-specific values.

--

## Task 7 – Resource References

Use resource references to refer to other resources in your configuration.

--

## Task 8 – Configure Remote State

Configure Terraform to use a remote backend for state storage.

--

## Task 8 – Configure Module Outputs

Use module outputs to extract values from child modules.

--

---

# 16. Common Interview Questions

1. What is Terraform?
2. Why Terraform?
3. What is IaC?
3. Explain Terraform workflow.
4. What happens during terraform apply?
5. What is a provider?
6. What is state file?
7. Why use remote backend?
8. What is state locking?
9. S3 + DynamoDB architecture?
11. What is a module?
12. Root vs child module?
13. variables.tf?
14. outputs.tf?
15. main.tf?
15. count vs for_each?
16. Data source vs resource?
17. Terraform in CI/CD?
18. How do you manage environments?
19. How do you troubleshoot Terraform failures?

--

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
- Apply the **Principle of Least Privilege** for IAM permissions.
- Tag all cloud resources consistently for governance and cost management.

--

These topics alone cover nearly 80% of Terraform interview discussions for DevOps Engineer and Platform Engineer roles.
