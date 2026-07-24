# Coding Round Practice (Terraform & Kubernetes)

> In most DevOps interviews, you'll receive partially completed Terraform or Kubernetes code. The interviewer evaluates your understanding, debugging ability, and best practices rather than memorization.

---

# Terraform Practice Questions

## Question 1 - Missing AMI

Complete the missing values.

```hcl
resource "aws_instance" "web" {
  ami           = ____________
  instance_type = "t2.micro"

  tags = {
    Name = "web-server"
  }
}
```

Expected

- Choose a valid AMI ID or variable.

---

## Question 2 - Missing Variable

```hcl
resource "aws_instance" "web" {
  ami           = var.ami
  instance_type = ____________
}
```

Complete it using a variable.

---

## Question 3 - Fix the Syntax

```hcl
resource "aws_s3_bucket" "logs" {
bucket = "company-logs"

tags = {
Name = logs
}
}
```

Find the mistakes.

---

## Question 4 - Output EC2 Public IP

Complete the output.

```hcl
output "public_ip" {
  value = ____________
}
```

---

## Question 5 - Security Group

Complete the ingress rule.

```hcl
ingress {

from_port = ____

to_port = ____

protocol = "tcp"

cidr_blocks = ["0.0.0.0/0"]

}
```

---

## Question 6 - Missing Provider

```hcl
___________ {

region = "us-east-1"

}
```

---

## Question 7 - Terraform Workflow

Arrange in order.

- apply
- validate
- init
- plan
- fmt

Expected

```
fmt

↓

init

↓

validate

↓

plan

↓

apply
```

---

## Question 8 - State File

Where should production Terraform state be stored?

Answer

- S3 Backend
- DynamoDB Locking

---

## Kubernetes Practice Questions

## Question 1 - Missing Image

```yaml
apiVersion: apps/v1

kind: Deployment

metadata:
  name: nginx

spec:
  replicas: 2

  selector:
    matchLabels:
      app: nginx

  template:
    metadata:
      labels:
        app: nginx

    spec:
      containers:
        - name: nginx

          image: ____________
```

---

## Question 2 - Increase Replicas

Current

```yaml
replicas: 1
```

Requirement

Deploy 5 Pods.

---

## Question 3 - Expose Port

```yaml
ports:
  - containerPort: _______
```

Expose Nginx.

---

## Question 4 - Missing Labels

```yaml
selector:

matchLabels:

__________
```

Why should labels match?

---

## Question 5 - Service Type

Application should be accessible outside Kubernetes.

Current

```yaml
type: ClusterIP
```

What should it become?

---

## Question 6 - Rolling Update

Which command performs a rolling restart?

---

## Question 7 - View Logs

Which command displays Pod logs?

---

## Question 8 - Debug CrashLoopBackOff

Your Pod shows

```
CrashLoopBackOff
```

What would you check first?

Expected

- kubectl describe pod
- kubectl logs
- Events
- Readiness/Liveness
- Image
- Environment Variables

---

# CI/CD Practice

## Question 1

Pipeline is failing during Build.

Where will you check first?

- Pipeline Logs
- Build Logs
- Dependency Errors

---

## Question 2

Docker build succeeds.

Deployment fails.

What will you check?

- Image Tag
- Registry
- Kubernetes Events
- Deployment Logs
- Image Pull Errors

---

## Question 3

Pipeline should deploy only from the main branch.

Complete:

```yaml
only:
  - __________
```

---

## Linux Practice

## Question 1

Find all files larger than 1 GB.

---

## Question 2

Count ERROR entries.

---

## Question 3

Display top 5 CPU-consuming processes.

---

## Question 4

Restart nginx.

---

## Question 5

Find disk usage.

---

# Python Practice

## Question 1

Read a JSON file.

What library would you use?

---

## Question 2

Call a REST API.

Which library?

---

## Question 3

Execute

```
kubectl get pods
```

from Python.

Which module?

---

## Question 4

Read an environment variable.

---

## Question 5

List EC2 instances.

Which AWS SDK?

---

# Scenario Questions

## Scenario 1

Deployment succeeds.

Pods never become Ready.

What would you investigate?

---

## Scenario 2

Terraform apply fails because state is locked.

How would you resolve it?

---

## Scenario 3

Docker image builds successfully.

Kubernetes cannot pull it.

Possible causes?

---

## Scenario 4

A developer accidentally pushes directly to production.

How would you prevent this?

---

## Scenario 5

A pipeline takes 40 minutes.

How would you optimize it?

---

# What Interviewers Evaluate

They are **not** checking whether you remember every command.

They evaluate whether you can:

- Read existing code
- Identify mistakes
- Complete missing blocks
- Follow best practices
- Explain your reasoning
- Troubleshoot failures
- Suggest improvements

# DevOps Coding Practice

## Terraform & Kubernetes Interview Questions

**Difficulty:** Intermediate (4–6 Years DevOps)

**Instructions**

- Do not Google immediately.
- Solve each question yourself.
- Explain your reasoning.
- Mention best practices wherever applicable.
- Assume production-grade environments.

---

# Terraform Practice (25 Questions)

## Question 1 – Complete the EC2 Resource

Fill in the missing values.

```hcl
resource "aws_instance" "web" {
  ami           = ____________
  instance_type = ____________

  tags = {
    Name = "web-server"
  }
}
```

---

## Question 2 – Create Variables

Replace hardcoded values with variables.

```hcl
resource "aws_instance" "web" {
  ami           = "ami-123456"
  instance_type = "t2.micro"
}
```

---

## Question 3 – Fix the Syntax Errors

```hcl
resource "aws_s3_bucket" "logs" {

bucket = "company-logs"

tags = {
Name = logs
}
}
```

Find every issue.

---

## Question 4 – Output the EC2 Public IP

Complete the output block.

```hcl
output "public_ip" {
  value = ____________
}
```

---

## Question 5 – Security Group

Allow HTTP traffic only.

Complete the ingress block.

---

## Question 6 – Missing Provider Block

Write the provider configuration.

---

## Question 7 – Add AWS Region Variable

Instead of hardcoding:

```hcl
region = "us-east-1"
```

use a variable.

---

## Question 8 – Create an S3 Bucket

Write Terraform to create:

```
company-backup-bucket
```

Include tags.

---

## Question 9 – Add Remote Backend

Configure Terraform to store state remotely.

---

## Question 10 – Enable State Locking

Which AWS service should be used?

Show the configuration.

---

## Question 11 – Create a VPC

CIDR

```
10.0.0.0/16
```

---

## Question 12 – Create Public Subnet

CIDR

```
10.0.1.0/24
```

---

## Question 13 – Internet Gateway

Attach an Internet Gateway to the VPC.

---

## Question 14 – Route Table

Create a default route to the Internet.

---

## Question 15 – Associate Route Table

Associate the subnet with the route table.

---

## Question 16 – Launch EC2 in Public Subnet

Use the subnet created above.

---

## Question 17 – IAM Role

Attach an IAM Role to EC2.

---

## Question 18 – User Data

Automatically install nginx during EC2 launch.

---

## Question 19 – Count

Launch three EC2 instances.

---

## Question 20 – for_each

Create three S3 buckets using for_each.

---

## Question 21 – Modules

Convert your EC2 resource into a reusable Terraform module.

---

## Question 22 – Terraform Plan

Explain what this command does.

```
terraform plan
```

---

## Question 23 – Terraform Apply Failed

State file is locked.

How would you troubleshoot?

---

## Question 24 – Destroy Prevention

Prevent accidental deletion of an S3 bucket.

---

## Question 25 – Production Review

Review this Terraform project.

Suggest improvements for:

- Variables
- Modules
- Backend
- State
- Security
- Naming
- Outputs

---

# Kubernetes Practice (25 Questions)

## Question 1 – Complete Deployment

Fill in the missing values.

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: nginx

spec:
  replicas: ____

  selector:
    matchLabels:
      app: ______

  template:
    metadata:
      labels:
        app: ______

    spec:
      containers:
        - name: nginx

          image: ______
```

---

## Question 2 – Scale Deployment

Current replicas:

```
1
```

Update to:

```
5
```

---

## Question 3 – Expose Port

Expose nginx container.

---

## Question 4 – Create Service

Expose Deployment internally.

---

## Question 5 – Change Service Type

Current:

```
ClusterIP
```

Application should be publicly accessible.

---

## Question 6 – ConfigMap

Store application configuration.

---

## Question 7 – Secret

Store database password securely.

---

## Question 8 – Mount ConfigMap

Mount ConfigMap inside a Pod.

---

## Question 9 – Environment Variables

Pass environment variables into the container.

---

## Question 10 – Resource Limits

Add:

- CPU request
- CPU limit
- Memory request
- Memory limit

---

## Question 11 – Readiness Probe

Configure readiness probe.

---

## Question 12 – Liveness Probe

Configure liveness probe.

---

## Question 13 – Rolling Update

Configure rolling update strategy.

---

## Question 14 – Namespace

Deploy into namespace:

```
production
```

---

## Question 15 – Ingress

Expose application using an Ingress resource.

---

## Question 16 – Persistent Volume

Create a Persistent Volume.

---

## Question 17 – Persistent Volume Claim

Claim storage from the PV.

---

## Question 18 – Mount Storage

Attach PVC to Deployment.

---

## Question 19 – CrashLoopBackOff

A Pod continuously restarts.

Explain your troubleshooting steps.

---

## Question 20 – ImagePullBackOff

Container image cannot be pulled.

List possible causes.

---

## Question 21 – Pending Pod

Pod remains Pending.

Explain how you would investigate.

---

## Question 22 – Node Not Ready

One node becomes NotReady.

What checks would you perform?

---

## Question 23 – Rolling Rollback

Rollback to previous Deployment version.

---

## Question 24 – Production Review

Review this Deployment.

Suggest improvements for:

- Security
- Resources
- Labels
- Probes
- Image
- Secrets
- ConfigMaps

---

## Question 25 – End-to-End Deployment

Design Kubernetes resources for this application.

Requirements:

- 3 replicas
- Rolling updates
- ConfigMap
- Secret
- Persistent storage
- Service
- Ingress
- Resource limits
- Health checks
- Production-ready deployment

---

# Bonus Challenge

You receive a broken Terraform project and a broken Kubernetes Deployment.

Without running the code:

- Identify syntax errors.
- Identify logical errors.
- Identify security issues.
- Identify production best practice improvements.
- Explain how you would test before deploying.

```

---

## Recommendation

I would actually split this into **two interview workbooks**:

1. **Terraform Interview Workbook (50 Questions)** – starting from basic syntax to production-grade architecture.
2. **Kubernetes Interview Workbook (50 Questions)** – covering manifests, debugging, Helm, networking, storage, security, and production scenarios.

That would give you **100 progressively harder interview-style questions**, very similar to what you'll encounter in companies hiring mid-level to senior DevOps/Platform Engineers.
```
