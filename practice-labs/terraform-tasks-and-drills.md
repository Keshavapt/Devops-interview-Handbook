### Task 5: Use for_each vs count

**Task:** Create multiple EC2 instances using for_each.

**Hint:**
```hcl
variable "instance_types" {
  type = map(string)
  default = {
    dev  = "t3.micro"
    prod = "t3.large"
  }
}

resource "aws_instance" "web" {
  for_each = var.instance_types

  ami           = var.ami_id
  instance_type = each.value
  tags = {
    Name = each.key
  }
}
```

**Question:** count vs for_each - when to use each?

**Answer:** count works well for identical resources; for_each is preferred when resources require unique configurations (using meaningful keys).

---

### Task 6: Configure Remote State & DynamoDB Locking

**Task:** Configure S3 backend with DynamoDB locking.

**Hint:**
```hcl
terraform {
  backend "s3" {
    bucket         = "my-terraform-state"
    key            = "prod/terraform.tfstate"
    region         = "us-east-1"
    dynamodb_table = "terraform-locks"
    encrypt        = true
  }
}
```

**Question:** Why use DynamoDB with S3?

**Answer:** DynamoDB provides state locking, preventing multiple users from applying Terraform changes simultaneously and protecting the integrity of the state file.

---

### Task 7: Use a Data Source

**Task:** Reference an existing security group.

**Hint:**
```hcl
data "aws_security_group" "existing" {
  name = "existing-sg"
}

resource "aws_instance" "web" {
  ami                    = var.ami_id
  instance_type          = var.instance_type
  security_groups        = [data.aws_security_group.existing.id]
}
```

**Question:** Data source vs resource?

**Answer:** Data sources read existing infrastructure (without managing it); resources create and manage new infrastructure. Use data sources to reference resources Terraform did not create.

---

### Task 8: Use a Module

**Task:** Create a VPC module structure.

**Hint:**
```
modules/
  vpc/
    main.tf
    variables.tf
    outputs.tf
```

**Root:**
```hcl
module "vpc" {
  source = "./modules/vpc"
  cidr   = "10.0.0.0/16"
}
```

**Question:** Why use modules?

**Answer:** Modules are reusable building blocks that improve reusability, standardization, maintainability, and reduce code duplication.
---

## Part 3: Terraform Troubleshooting Drills

### Drill 1: terraform plan shows "No changes"

**Scenario:** You ran `terraform plan` and it says "No changes. Your infrastructure matches the configuration."

**Possible Causes:**
- Configuration unchanged from last apply
- Remote state is up to date
- Manual changes were made to infrastructure

**Resolution:**
1. Check `terraform state list`
2. Review `terraform plan` output for changes
3. Check if someone manually modified resources
4. Run `terraform refresh` if needed

---

### Drill 2: terraform apply fails with state locking

**Scenario:** `terraform apply` fails with "Error acquiring the state lock."

**Possible Causes:**
- Another process is already running terraform
- Stale lock from crashed process
- DynamoDB table misconfigured

**Resolution:**
1. Verify no other terraform is running: `ps aux | grep terraform`
2. If stale lock: use `terraform force-unlock <LOCK_ID>`
3. Check DynamoDB table exists and is accessible
4. Verify S3 bucket exists and is accessible

---

### Drill 3: Configuration drift detected

**Scenario:** `terraform plan` shows resources that don't match state.

**Possible Causes:**
- Manual console changes
- Out-of-band updates (automation not using Terraform)
- Accidental deletion/creation of resources

**Resolution:**
1. `terraform plan` to see exact drift
2. Update Terraform config to match reality
3. Or run `terraform apply` to bring real infrastructure in line with config

---

### Drill 4: terraform destroy fails

**Scenario:** `terraform destroy` fails mid-way.

**Possible Causes:**
- Dependency issues
- Resource already deleted manually
- Provider API error

**Resolution:**
1. Review the error message
2. Check `terraform state list`
3. Address dependency issues first
4. Re-run `terraform destroy`

---

### Drill 5: State file contains secrets

**Scenario:** You discover that `terraform.tfstate` contains plaintext passwords.

**Possible Causes:**
- Resource generates password at creation
- State file stored locally
- State file not encrypted

**Resolution:**
1. Use `sensitive = true` on variables/outputs
2. Move backend to S3 with server-side encryption
3. Rotate leaked credentials immediately
4. Revoke/complain about state file access

**Best Practice:** State files contain sensitive data. Always:
- Use remote backend (S3)
- Enable encryption at rest
- Enable versioning for backups
---

## Part 4: Answers, Hints & Solutions

### Task 1: Provider Configuration

**Solution:**
```hcl
provider "aws" {
  region = var.aws_region
}

variable "aws_region" {
  description = "AWS Region"
  type        = string
  default     = "us-east-1"
}
```

**Why variables?**
- Code is reusable across environments
- Different environments use different regions
- Easy to override via tfvars

---

### Task 2: EC2 Resource

**Solution:**
```hcl
resource "aws_instance" "web" {
  ami           = var.ami_id
  instance_type = var.instance_type

  tags = {
    Name = "web-server"
  }

  vpc_security_group_ids = [aws_security_group.web.id]
  subnet_id              = aws_subnet.public.id

  key_name = var.key_name
}
```

---

### Task 3: VPC

**Solution:**
```hcl
resource "aws_vpc" "main" {
  cidr_block           = "10.0.0.0/16"
  enable_dns_hostnames = true
  tags = { Name = "main-vpc" }
}

resource "aws_subnet" "public" {
  vpc_id     = aws_vpc.main.id
  cidr_block = "10.0.1.0/24"
  tags = { Name = "public-subnet" }
}

resource "aws_subnet" "private" {
  vpc_id     = aws_vpc.main.id
  cidr_block = "10.0.2.0/24"
  tags = { Name = "private-subnet" }
}
```

---

### Task 4: Security Groups

**Solution:**
```hcl
resource "aws_security_group" "web" {
  name = "web-sg"
  vpc_id = aws_vpc.main.id

  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  ingress {
    from_port   = 443
    to_port     = 443
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}
```

---

### Task 5: for_each Example

**Solution:**
---

## Part 5: Interview Best Practices

### During Coding Rounds

**Always explain your reasoning:**

> "I'll first run `terraform fmt` to ensure consistent formatting, then `terraform validate` to catch syntax errors. Next, I'll review the execution plan with `terraform plan` before applying changes. I'll replace hardcoded values with variables, reference resources instead of IDs, use module outputs for dependencies, and ensure the backend uses an S3 bucket with DynamoDB locking for collaborative, production-ready state management."

### Production Best Practices Checklist

- Always use remote state (S3 backend)
- Enable DynamoDB state locking
- Store Terraform code in Git
- Use modules for reusable infrastructure
- Replace hardcoded values with variables
- Use terraform.tfvars for environment-specific values
- Run `terraform fmt` before committing code
- Validate using `terraform validate`
- Review changes with `terraform plan` before applying
- Protect production branches with code reviews
- Never store secrets in Terraform code
- Use IAM roles and secret management services
- Apply the Principle of Least Privilege for IAM
- Tag all cloud resources consistently

### Interview Questions to Prepare

1. Why use remote state (S3)?
2. Why use DynamoDB with S3?
3. What is terraform import?
4. How do you handle configuration drift?
5. Provisioners vs Ansible?
6. count vs for_each?
7. Data source vs resource?

### Key Commands Cheat Sheet

```bash
# Format & Validate
terraform fmt
terraform validate

# Plan & Apply
terraform plan
terraform apply
terraform destroy

# State & Backend
terraform init
terraform state list
terraform import
terraform output
terraform force-unlock

# CI/CD
terraform fmt
terraform validate
terraform plan
# (manual approval)
terraform apply
```

---

## Interview Question: How do you handle configuration drift?

**Answer:**
1. `terraform plan` shows the drift
2. I identify what changed externally (manual console changes, out-of-band updates)
3. Either update Terraform config to match reality, or run `terraform apply` to bring real infrastructure back in line with config

---

## Interview Question: What is terraform import?

**Answer:** Terraform can start managing resources that already exist using the `terraform import` command:

```bash
terraform import aws_instance.web i-0123456789abcdef0
```

**Steps:**
1. Define the resource in Terraform configuration
2. Run `terraform import`
3. Verify using `terraform plan`
4. Update config if needed to match imported resource

---

## Interview Question: Provisioners vs Ansible?

**Answer:** Terraform provisioners run during resource creation and are tied to the resource lifecycle. Ansible runs independently and is the preferred tool for configuration management. Best practice is to use Terraform for provisioning and Ansible for configuration management.

---

## Final Interview Tip

In Terraform coding interviews, explain your reasoning as you work.

**Example approach:**
1. First run `terraform fmt` for consistent formatting
2. Then `terraform validate` to catch syntax errors
3. Review the execution plan with `terraform plan` before applying
4. Replace hardcoded values with variables
5. Reference resources instead of IDs
6. Use module outputs for dependencies
7. Ensure backend uses S3 with DynamoDB locking for state management

This demonstrates not just Terraform syntax knowledge but also a production-focused engineering mindset.
```hcl
variable "instance_types" {
  type = map(string)
  default = {
    dev  = "t3.micro"
    prod = "t3.large"
  }
}

resource "aws_instance" "web" {
  for_each = var.instance_types

  ami           = var.ami_id
  instance_type = each.value
  tags = {
    Name = each.key
  }
}
```

---

### Task 6: Remote State Configuration

**Solution:**
```hcl
terraform {
  backend "s3" {
    bucket         = "my-terraform-state"
    key            = "prod/terraform.tfstate"
    region         = "us-east-1"
    dynamodb_table = "terraform-locks"
    encrypt        = true
  }
}
```

---

### Task 7: Data Source

**Solution:**
```hcl
data "aws_security_group" "existing" {
  name = "existing-sg"

  tags = {
    Environment = "production"
  }
}

resource "aws_instance" "web" {
  ami                    = var.ami_id
  instance_type          = var.instance_type
  vpc_security_group_ids = [data.aws_security_group.existing.id]
}
```

---

### Task 8: Module Usage

**Solution:**
```hcl
# modules/vpc/main.tf
resource "aws_vpc" "main" {
  cidr_block = var.cidr
}

# modules/vpc/variables.tf
variable "cidr" {
  type = string
}

# Root module
module "vpc" {
  source = "./modules/vpc"
  cidr   = "10.0.0.0/16"
}
```

---

### Drill 1: No Changes Solution

**Diagnosis:**
```bash
terraform plan
terraform state list
```

**Resolution:**
1. Check for manual changes
2. Verify `terraform.tfstate` matches config
3. Run `terraform refresh` if state is stale

---

### Drill 2: State Locking Solution

**Diagnosis:**
```bash
ps aux | grep terraform
terraform force-unlock <lock-id>
```

**Resolution:**
1. Check for running terraform processes
2. Use `terraform force-unlock <lock-id>` for stale locks
3. Verify DynamoDB table configuration

---

### Drill 3: Drift Solution

**Diagnosis:**
```bash
terraform plan
```

**Resolution:**
1. Review drift in `terraform plan`
2. Update config to match reality (or apply changes)
3. Document the drift for RCA

---

### Drill 4: Destroy Failure Solution

**Diagnosis:**
```bash
terraform destroy
terraform state list
```

**Resolution:**
1. Review error messages
2. Check resource dependencies
3. Address dependency issues first
4. Re-run destroy

---

### Drill 5: Secrets in State Solution

**Diagnosis:**
```bash
cat terraform.tfstate | grep -i password
```

**Resolution:**
1. Mark secrets as `sensitive = true`
2. Use S3 with SSE encryption
3. Rotate leaked credentials
4. Secure state file access