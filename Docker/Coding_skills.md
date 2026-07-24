# Coding Skills for DevOps Interviews

> **Goal:** Prepare for scripting, automation, and practical coding questions commonly asked in DevOps interviews (4–6 Years Experience).

---

# Table of Contents

1. Introduction
2. Python vs Bash
3. Bash Fundamentals
4. Python Fundamentals
5. Linux Automation Using Bash
6. Infrastructure Automation
7. CI/CD Automation
8. Infrastructure as Code Automation
9. REST API Automation
10. JSON & YAML Handling
11. File Processing
12. Log Analysis
13. Monitoring & Alerting Automation
14. AWS Automation (boto3)
15. Kubernetes Automation
16. Git Automation
17. Real-world Automation Examples
18. Explain Your CI/CD Pipeline
19. Explain Your Terraform Implementation
20. Explain an Automation You Built
21. Common Python Interview Questions
22. Common Bash Interview Questions
23. Scenario-Based Interview Questions
24. Hands-on Coding Problems
25. 5-Minute Revision
26. Interview Tips

---

# 1. Introduction

Modern DevOps Engineers are expected to automate repetitive tasks, improve deployment speed, and reduce manual effort using scripting and Infrastructure as Code.

Interviewers generally do **not** expect a DevOps Engineer to solve complex Data Structures & Algorithms problems. Instead, they look for practical scripting skills and the ability to automate real-world infrastructure and deployment tasks.

Typical areas include:

- Linux automation
- Cloud automation
- CI/CD pipeline scripting
- Infrastructure provisioning
- API integrations
- Log analysis
- Monitoring automation
- Deployment automation

---

# What Interviewers Expect

You should be able to:

- Read and understand Bash scripts.
- Write small Python automation scripts.
- Parse JSON or YAML files.
- Call REST APIs.
- Automate AWS or Kubernetes tasks.
- Explain automation you built in previous projects.
- Walk through your CI/CD pipeline.
- Explain Infrastructure as Code implementations.

---

# 2. Python vs Bash

Choosing the right scripting language is important.

| Bash                       | Python                |
| -------------------------- | --------------------- |
| Linux automation           | Complex automation    |
| File operations            | API integrations      |
| Service management         | AWS automation        |
| Cron jobs                  | Kubernetes automation |
| Deployment scripts         | Data processing       |
| Quick administrative tasks | Business logic        |

---

## When to Use Bash

Bash is best suited for Linux administration and lightweight automation.

Examples:

- Starting and stopping services
- Log rotation
- File management
- Deployment scripts
- Cron jobs
- Server maintenance
- Backup scripts

Example

```
Restart Nginx

↓

systemctl restart nginx
```

---

## When to Use Python

Python is better when automation involves external systems or complex logic.

Examples:

- AWS automation (boto3)
- Kubernetes automation
- REST API calls
- Report generation
- Reading JSON/YAML
- Monitoring scripts
- Cloud automation

Example

```
Python Script

↓

Read AWS Resources

↓

Generate Report

↓

Email Team
```

---

## Interview Question

### Why would you choose Python over Bash?

**Sample Answer**

Python is preferred when automation requires complex logic, API integrations, cloud SDKs like boto3, JSON/YAML parsing, or better code maintainability. Bash is ideal for quick Linux administration and shell-based automation.

---

## Interview Question

### When would you choose Bash?

**Sample Answer**

I use Bash for Linux-specific tasks such as service management, deployment scripts, file operations, backups, and cron jobs because it is lightweight and available on almost every Linux system.

---

# 3. Bash Fundamentals

Every DevOps Engineer should be comfortable with common Linux commands.

---

## Navigation

```bash
pwd
ls -la
cd
mkdir
rmdir
```

---

## File Operations

```bash
cp
mv
rm
touch
cat
less
head
tail
```

---

## Searching

```bash
find
locate
grep
```

Example

```bash
grep "ERROR" application.log
```

---

## Text Processing

```bash
awk
sed
cut
sort
uniq
tr
wc
```

Example

Count errors

```bash
grep ERROR app.log | wc -l
```

---

## File Permissions

```bash
chmod
chown
umask
```

Interview Question

Difference between chmod and chown?

- chmod changes permissions.
- chown changes ownership.

---

## Process Management

```bash
ps
top
htop
kill
killall
```

Example

```bash
ps -ef | grep java
```

---

## Disk Usage

```bash
df -h
du -sh
```

Interview Question

Difference?

- df shows filesystem usage.
- du shows directory/file usage.

---

## Service Management

```bash
systemctl start nginx

systemctl stop nginx

systemctl restart nginx

systemctl status nginx
```

---

## Networking

```bash
curl
wget
ping
netstat
ss
scp
ssh
```

Interview Question

Difference between curl and wget?

- curl is mainly used for API requests and data transfer.
- wget is mainly used to download files.

---

## Archives

```bash
tar
gzip
gunzip
zip
unzip
```

---

## Common Bash Interview Questions

### Find all files larger than 500 MB.

```bash
find / -size +500M
```

---

### Count ERROR entries in a log file.

```bash
grep ERROR app.log | wc -l
```

---

### Show the last 100 lines of a log.

```bash
tail -100 app.log
```

---

### Search recursively for a word.

```bash
grep -r "Database Connection Failed" .
```

---

### Find top CPU-consuming processes.

```bash
ps aux --sort=-%cpu | head
```

---

### Debug a Bash script.

```bash
bash -x deploy.sh
```

---

# 4. Python Fundamentals

Interviewers generally expect basic scripting knowledge rather than software development expertise.

---

## Topics to Know

- Variables
- Loops
- Functions
- Lists
- Dictionaries
- Exception Handling
- Reading Files
- Writing Files
- Environment Variables
- JSON
- YAML
- REST APIs
- subprocess module

---

## Common Python Libraries

| Library    | Purpose                       |
| ---------- | ----------------------------- |
| os         | Operating system interactions |
| sys        | Command-line arguments        |
| subprocess | Execute shell commands        |
| json       | JSON parsing                  |
| yaml       | YAML parsing                  |
| requests   | REST API calls                |
| boto3      | AWS automation                |
| pathlib    | File handling                 |
| logging    | Application logging           |

---

## Example Use Cases

### Read Configuration File

```
Python

↓

Read YAML

↓

Deploy Application
```

---

### Execute Linux Command

```
Python

↓

subprocess

↓

kubectl get pods
```

---

### Call REST API

```
Python

↓

requests

↓

GitLab API

↓

Pipeline Status
```

---

### Parse JSON Response

```
REST API

↓

JSON

↓

Python

↓

Extract Required Fields
```

---

## Exception Handling

Always handle failures gracefully.

Examples

- API unavailable
- File missing
- Invalid JSON
- Network timeout
- Permission denied

Interviewers often ask how your scripts handle failures rather than focusing on syntax.

---

## Interview Questions

### Why use Python in DevOps?

Python simplifies automation involving cloud SDKs, REST APIs, configuration files, monitoring, and reporting.

---

### Which Python libraries have you used?

Common answers:

- requests
- boto3
- json
- yaml
- os
- subprocess
- logging

---

### What is subprocess used for?

It allows Python scripts to execute Linux commands such as:

- kubectl
- docker
- terraform
- git

---

### Have you worked with JSON?

Yes.

Examples include:

- API responses
- Terraform outputs
- Kubernetes manifests
- AWS SDK responses

---

### Have you worked with YAML?

Yes.

Examples include:

- Kubernetes manifests
- Docker Compose
- GitLab CI pipelines
- Ansible playbooks

---

# 5. Linux Automation Using Bash

Automation is one of the core responsibilities of a DevOps Engineer.

Instead of performing repetitive tasks manually, scripts are used to ensure consistency and save time.

---

## Common Automation Tasks

- Backup automation
- Log cleanup
- User creation
- Service restart
- Health checks
- Docker cleanup
- Certificate monitoring
- Cron jobs
- File synchronization

---

## Example 1 - Log Cleanup

```
Server

↓

Find Logs Older Than 30 Days

↓

Compress

↓

Archive

↓

Delete Old Files
```

---

## Example 2 - Backup Automation

```
Database

↓

Backup

↓

Compress

↓

Upload to S3

↓

Verify

↓

Notify Team
```

---

## Example 3 - Service Monitoring

```
Check Service Status

↓

Running?

↓

Yes

↓

Exit

↓

No

↓

Restart Service

↓

Send Alert
```

---

## Example 4 - Docker Cleanup

```
Unused Images

↓

Unused Containers

↓

Unused Volumes

↓

Remove

↓

Free Disk Space
```

---

## Example 5 - SSL Certificate Monitoring

```
Read Certificate

↓

Check Expiry Date

↓

Less Than 30 Days?

↓

Send Email

↓

Slack Notification
```

---

## Scheduling Automation

Automation is commonly scheduled using Cron.

Examples

- Daily backups
- Weekly cleanup
- SSL monitoring
- Disk usage reports
- Log rotation

---

## Interview Question

### Tell me about a Bash automation you created.

**Sample Answer**

I created automation scripts to perform routine Linux administration tasks such as log cleanup, service monitoring, backup verification, and deployment support. These scripts reduced manual effort, improved consistency, and were scheduled using cron for regular execution.

---

## Best Practices

- Keep scripts modular.
- Use meaningful variable names.
- Handle failures gracefully.
- Log important events.
- Avoid hardcoding credentials.
- Validate inputs before execution.
- Test scripts in non-production environments.
- Document the script's purpose and usage.

---

# 6. Infrastructure Automation

Infrastructure automation enables teams to provision, modify, and manage infrastructure using code instead of manual operations.

Benefits

- Faster deployments
- Repeatable infrastructure
- Version controlled
- Reduced human errors
- Easier disaster recovery
- Consistent environments

---

## Traditional Approach

```
Login AWS Console

↓

Create VPC

↓

Create Subnets

↓

Create EC2

↓

Configure Security Groups

↓

Deploy Application
```

Problems

- Time consuming
- Error prone
- Difficult to reproduce
- No version control

---

## Automated Approach

```
Git Commit

↓

Terraform

↓

Create Infrastructure

↓

Validate

↓

Deploy Application
```

---

## Common Infrastructure Automation Tasks

- Create VPC
- Create EC2
- Create Security Groups
- Create IAM Roles
- Create Load Balancer
- Create S3 Buckets
- Configure Networking
- Provision Kubernetes Clusters
- DNS Management

---

## Typical Workflow

```
Developer

↓

Modify Terraform Code

↓

Git Push

↓

Pipeline Trigger

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

Infrastructure Ready
```

---

## Interview Question

### Why automate infrastructure?

**Sample Answer**

Infrastructure automation ensures consistency, repeatability, and faster deployments. Instead of manually creating resources, Infrastructure as Code allows changes to be version-controlled, reviewed, and automatically deployed through CI/CD pipelines.

---

# 7. CI/CD Automation

One of the primary responsibilities of a DevOps Engineer is automating software delivery.

Instead of manually building and deploying applications, CI/CD pipelines perform these tasks automatically.

---

## Traditional Deployment

```
Developer

↓

Build Application

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
- Error-prone
- No standardization
- Difficult rollback

---

## Automated Deployment

```
Developer

↓

Git Push

↓

Pipeline

↓

Build

↓

Unit Test

↓

Code Analysis

↓

Security Scan

↓

Docker Build

↓

Push Image

↓

Deploy

↓

Smoke Test

↓

Monitoring
```

---

## CI/CD Automation Responsibilities

Typical automation includes

- Build automation
- Testing automation
- Docker image creation
- Image scanning
- Artifact publishing
- Kubernetes deployment
- Rollbacks
- Notifications
- Environment promotion

---

## Production Deployment Flow

```
Git Push

↓

GitLab Pipeline

↓

Build

↓

JUnit Tests

↓

SonarQube

↓

Trivy Scan

↓

Docker Build

↓

Push Image

↓

Deploy Dev

↓

Integration Tests

↓

Deploy QA

↓

Approval

↓

Deploy Production

↓

Health Check

↓

Monitoring
```

---

## Benefits

- Faster releases
- Consistent deployments
- Reduced downtime
- Easier rollback
- Improved quality
- Automated validation

---

## Interview Question

### How did you improve deployment speed?

**Sample Answer**

We automated our deployments using GitLab CI/CD. The pipeline handled application builds, unit testing, Docker image creation, vulnerability scanning, image publishing, Kubernetes deployment, and post-deployment validation. This reduced manual deployment time and improved deployment consistency.

---

# 8. Infrastructure as Code (IaC) Automation

Infrastructure as Code allows infrastructure to be managed using code.

Instead of clicking through cloud consoles, infrastructure is defined declaratively.

---

## Common IaC Tools

- Terraform
- AWS CloudFormation
- Pulumi
- Azure Bicep

Terraform is the most commonly used tool.

---

## Terraform Workflow

```
Developer

↓

Write Terraform Code

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

Infrastructure Created
```

---

## Components

Terraform typically provisions

- VPC
- EC2
- Security Groups
- IAM
- ALB
- Route Tables
- NAT Gateway
- EKS
- S3
- RDS

---

## State Management

Production environments generally use

```
Terraform State

↓

Amazon S3

↓

State Locking

↓

DynamoDB
```

Benefits

- Shared state
- Prevent concurrent updates
- Team collaboration
- Reliable deployments

---

## Best Practices

- Store state remotely.
- Use reusable modules.
- Separate environments.
- Use variables.
- Review plans before applying.
- Protect production state files.

---

## Interview Question

### Why use Terraform instead of manually creating AWS resources?

**Sample Answer**

Terraform provides repeatable, version-controlled infrastructure. It supports automation, code reviews, environment consistency, and simplifies disaster recovery.

---

# 9. REST API Automation

Modern DevOps engineers frequently automate tasks using REST APIs.

Almost every DevOps tool provides REST APIs.

Examples

- GitLab
- GitHub
- Jira
- Jenkins
- Kubernetes
- AWS
- Slack
- ServiceNow

---

## Common Workflow

```
Python Script

↓

REST API

↓

Receive JSON

↓

Parse Response

↓

Generate Report

↓

Notify Team
```

---

## Common Operations

GET

Retrieve information.

Example

- List pipelines
- List EC2 instances
- Fetch tickets

---

POST

Create resources.

Example

- Trigger pipeline
- Create issue
- Start deployment

---

PUT

Update resources.

Example

- Update deployment
- Modify ticket
- Update user

---

DELETE

Remove resources.

Example

- Delete resource
- Remove pipeline
- Delete deployment

---

## Python Library

Most API automation uses

```
requests
```

---

## Interview Question

### Have you worked with REST APIs?

**Sample Answer**

Yes. I have used REST APIs to automate CI/CD tasks, retrieve pipeline status, integrate cloud services, generate reports, and communicate with external systems using Python.

---

# 10. JSON & YAML Handling

Configuration files are commonly stored as JSON or YAML.

A DevOps Engineer should understand both formats.

---

## JSON

Common Uses

- API Responses
- AWS SDK
- Terraform Outputs
- Configuration Files

Example Workflow

```
REST API

↓

JSON Response

↓

Python

↓

Extract Required Fields
```

---

## YAML

Common Uses

- Kubernetes Manifests
- Docker Compose
- GitLab CI
- Ansible Playbooks

Example

```
deployment.yaml

↓

kubectl apply

↓

Application Deployed
```

---

## Common Tasks

- Read file
- Parse values
- Modify configuration
- Generate reports
- Validate content

---

## Interview Question

### Where have you worked with YAML?

Typical answer

- Kubernetes manifests
- Docker Compose
- GitLab CI/CD
- Helm Charts
- Ansible Playbooks

---

# 11. File Processing

Automation frequently requires processing files.

Examples

- CSV Reports
- Log Files
- Configuration Files
- Backup Files
- JSON Reports
- YAML Files

---

## Typical Workflow

```
Read File

↓

Extract Data

↓

Validate

↓

Generate Output

↓

Archive

↓

Notify Team
```

---

## Common Operations

- Read file
- Write file
- Append content
- Search text
- Replace values
- Generate reports

---

## Real Examples

- Read server inventory
- Process deployment logs
- Generate health reports
- Read Terraform outputs
- Parse Kubernetes manifests

---

## Interview Question

### Have you automated report generation?

**Sample Answer**

Yes. Automation scripts collected data from infrastructure, parsed the required information, generated reports in CSV or JSON format, and shared them through email or messaging platforms.

---

# 12. Log Analysis

Log analysis is one of the most common automation tasks in DevOps.

---

## Typical Workflow

```
Application Logs

↓

Search ERROR

↓

Extract Events

↓

Generate Summary

↓

Send Alert
```

---

## Common Tasks

- Find ERROR entries
- Count failures
- Detect exceptions
- Identify failed services
- Generate reports
- Archive logs

---

## Linux Commands

Search errors

```bash
grep ERROR application.log
```

Count errors

```bash
grep ERROR application.log | wc -l
```

Last 100 lines

```bash
tail -100 application.log
```

Search recursively

```bash
grep -r "Connection refused" .
```

---

## Python Automation

Python scripts commonly

- Read logs
- Extract exceptions
- Count occurrences
- Detect anomalies
- Generate reports
- Send notifications

---

## Monitoring Workflow

```
Read Logs

↓

Find Critical Errors

↓

Threshold Exceeded?

↓

Yes

↓

Slack Notification

↓

Email Team

↓

Create Incident
```

---

## Interview Question

### How would you automate log monitoring?

**Sample Answer**

I would develop a Python or Bash script to periodically scan application logs, identify critical errors using pattern matching, generate summaries, and send alerts through Slack or email when predefined thresholds are exceeded. For production systems, this functionality is typically handled using centralized logging solutions such as ELK or Grafana Loki combined with alerting.

---

## Best Practices

- Automate repetitive tasks.
- Validate script inputs.
- Handle exceptions gracefully.
- Maintain detailed logs.
- Avoid hardcoding credentials.
- Use environment variables or secret management.
- Keep automation idempotent.
- Version-control all scripts.
- Test in lower environments before production.

---

# 13. Monitoring & Alerting Automation

Monitoring automation ensures applications and infrastructure remain healthy by continuously collecting metrics, analyzing them against thresholds, and notifying the appropriate teams when issues occur.

Instead of manually checking dashboards, monitoring systems automatically detect anomalies and trigger alerts.

---

## Monitoring Workflow

```text
Application

↓

Collect Metrics

↓

Store Metrics

↓

Evaluate Rules

↓

Threshold Exceeded?

↓

Yes

↓

Send Alert

↓

Engineer Investigation
```

---

## Common Monitoring Tools

| Tool         | Purpose                    |
| ------------ | -------------------------- |
| Prometheus   | Metrics Collection         |
| Grafana      | Dashboards & Visualization |
| Alertmanager | Alert Routing              |
| CloudWatch   | AWS Monitoring             |
| Grafana Loki | Log Aggregation            |
| ELK Stack    | Log Analysis               |
| Datadog      | SaaS Monitoring            |
| New Relic    | APM                        |

---

## Common Metrics

Infrastructure

- CPU Usage
- Memory Usage
- Disk Usage
- Network Usage
- Filesystem Space

Application

- Response Time
- Error Rate
- Request Count
- Throughput
- Availability

Kubernetes

- Pod Status
- Node Health
- Restart Count
- Resource Usage

---

## Alerting Workflow

```text
Prometheus

↓

Alert Rule

↓

Alertmanager

↓

Slack

↓

Email

↓

PagerDuty

↓

Engineer
```

---

## Real-world Automation

Example

```
CPU > 85%

↓

Prometheus Alert

↓

Slack Notification

↓

Engineer Investigation
```

---

## Disk Monitoring

```
Disk Usage

↓

Above 90%?

↓

Send Alert

↓

Run Cleanup Script

↓

Notify Team
```

---

## Interview Question

### How have you automated monitoring?

**Sample Answer**

We used Prometheus to collect metrics and Alertmanager to send notifications through Slack and email whenever predefined thresholds were crossed. Grafana dashboards provided visualization for application and infrastructure health.

---

# 14. AWS Automation (boto3)

boto3 is the official AWS SDK for Python.

It enables Python scripts to create, modify, and manage AWS resources programmatically.

---

## Common AWS Services Automated

- EC2
- S3
- IAM
- Lambda
- CloudWatch
- SNS
- SQS
- Secrets Manager
- RDS

---

## Typical Workflow

```text
Python Script

↓

boto3

↓

AWS API

↓

Create Resource

↓

Receive Response
```

---

## Common Automation Tasks

- Launch EC2 Instances
- Stop Idle Instances
- Generate EC2 Inventory
- Upload Files to S3
- Download Backups
- Rotate Secrets
- Generate Cloud Reports
- Read CloudWatch Metrics

---

## Real Example

```
Every Night

↓

List EC2 Instances

↓

Collect CPU Utilization

↓

Generate CSV

↓

Email Operations Team
```

---

## Benefits

- Faster provisioning
- Repeatable automation
- Better reporting
- Integration with CI/CD
- Cost optimization

---

## Interview Question

### Have you used boto3?

**Sample Answer**

Yes. I have used boto3 for automating AWS operations such as retrieving EC2 information, interacting with S3 buckets, collecting infrastructure reports, and integrating AWS operations into automation scripts.

---

# 15. Kubernetes Automation

Modern Kubernetes environments rely heavily on automation.

Instead of manually managing clusters, automation performs deployments, scaling, monitoring, and rollbacks.

---

## Typical Deployment Flow

```text
Git Push

↓

CI Pipeline

↓

Docker Image

↓

Container Registry

↓

kubectl / Helm

↓

Kubernetes Cluster

↓

Rolling Update
```

---

## Common Automation Tasks

- Deploy Applications
- Scale Deployments
- Restart Pods
- Read Pod Logs
- Check Pod Health
- Rollback Deployments
- Upgrade Helm Releases

---

## Example

```
Pipeline

↓

kubectl apply

↓

Deployment Updated

↓

Pods Created

↓

Health Checks

↓

Success
```

---

## Health Verification

```
Deployment

↓

Pods Running?

↓

Ready?

↓

Service Available?

↓

Deployment Successful
```

---

## Common kubectl Commands

```bash
kubectl get pods

kubectl describe pod

kubectl logs

kubectl rollout status

kubectl rollout restart

kubectl rollout undo

kubectl scale deployment

kubectl get events
```

---

## Interview Question

### How do you automate Kubernetes deployments?

**Sample Answer**

Our GitLab pipeline built the Docker image, pushed it to Amazon ECR, updated the Kubernetes deployment using kubectl or Helm, verified rollout status, and executed smoke tests after deployment.

---

# 16. Git Automation

Git is central to every DevOps workflow.

Automation ensures consistent branching, validation, and deployments.

---

## Typical Workflow

```text
Developer

↓

Git Commit

↓

Push

↓

Merge Request

↓

Pipeline

↓

Deployment
```

---

## Common Git Automation

- Branch Protection
- Merge Request Validation
- Pipeline Trigger
- Automatic Version Tagging
- Release Creation
- Changelog Generation

---

## Branch Strategy Example

```
feature

↓

Merge Request

↓

develop

↓

Testing

↓

main

↓

Production
```

---

## Interview Question

### How does Git integrate with CI/CD?

**Sample Answer**

Every Git push automatically triggers the CI pipeline. Merge Requests execute validation jobs before code is merged, ensuring quality and preventing broken code from reaching production.

---

# 17. Real-world Automation Examples

Interviewers frequently ask:

> Tell me about an automation you built.

Prepare multiple examples.

---

## Example 1 — Deployment Automation

Manual Process

```
Build

↓

Docker

↓

Push

↓

Deploy

↓

Verify
```

Automated Process

```
Git Push

↓

Pipeline

↓

Complete Deployment
```

Benefits

- Reduced deployment time
- Fewer manual errors
- Consistent releases

---

## Example 2 — Server Health Monitoring

```
Collect Metrics

↓

Generate Report

↓

Threshold Exceeded?

↓

Slack Alert

↓

Email Team
```

Benefits

- Faster incident response
- Reduced downtime

---

## Example 3 — Docker Cleanup

```
Unused Images

↓

Unused Containers

↓

Unused Volumes

↓

Cleanup

↓

Free Disk Space
```

---

## Example 4 — SSL Certificate Monitoring

```
Read Certificate

↓

Days Remaining

↓

Less Than 30 Days?

↓

Notify Team
```

---

## Example 5 — Backup Automation

```
Database

↓

Backup

↓

Compress

↓

Upload to S3

↓

Verification

↓

Notification
```

---

## Example 6 — Cost Optimization

```
List EC2 Instances

↓

Idle?

↓

Generate Report

↓

Notify Team
```

---

# 18. Explain Your CI/CD Pipeline

This is one of the most frequently asked interview questions.

---

## Sample Answer

> In my previous project, developers pushed code to GitLab repositories. Every Merge Request triggered the GitLab CI/CD pipeline. The pipeline checked out the source code, built the application using Maven, executed JUnit test cases, and performed static code analysis using SonarQube. Docker images were then built and scanned using Trivy before being pushed to Amazon ECR. Kubernetes deployments were updated using kubectl, performing rolling updates with zero downtime. After deployment, smoke tests verified the application, and Prometheus and Grafana continuously monitored the environment. If any stage failed, the pipeline stopped immediately, preventing faulty deployments.

---

## Pipeline Flow

```text
Developer

↓

Git Push

↓

Checkout

↓

Build

↓

Unit Tests

↓

SonarQube

↓

Security Scan

↓

Docker Build

↓

Image Scan

↓

Push to ECR

↓

Deploy Dev

↓

Integration Tests

↓

Deploy QA

↓

Approval

↓

Deploy Production

↓

Smoke Tests

↓

Monitoring
```

---

## Interview Question

### Which stage is most critical?

**Sample Answer**

Every stage is important, but automated testing and security scanning are especially critical because they prevent defective or vulnerable code from reaching production.

---

# 19. Explain Your Terraform Implementation

Another common interview topic.

---

## Sample Answer

> We managed our AWS infrastructure using Terraform. The project was divided into reusable modules for networking, compute, IAM, and load balancing. Terraform state was stored remotely in an S3 bucket with DynamoDB used for state locking. Infrastructure changes were deployed through GitLab CI/CD, where the pipeline executed terraform fmt, validate, plan, and after manual approval, terraform apply. This ensured all infrastructure changes were version-controlled, reviewed, and reproducible.

---

## Terraform Workflow

```text
Developer

↓

Terraform Code

↓

Git Push

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

↓

Infrastructure Ready
```

---

## Best Practices

- Modularize infrastructure
- Store remote state
- Enable state locking
- Separate environments
- Review terraform plan
- Never hardcode secrets
- Protect production state

---

# 20. Explain an Automation You Built

This is one of the most common behavioral questions in DevOps interviews.

Interviewers want to understand:

- Your problem-solving ability
- Your automation mindset
- Your technical decisions
- The impact of your work

Use the **STAR Method**:

- Situation
- Task
- Action
- Result

---

## Example 1 – Deployment Automation

### Situation

Deployments were performed manually by the operations team.

Each deployment required:

- Building the application
- Creating Docker images
- Pushing images to the registry
- Updating Kubernetes deployments
- Verifying application health

The entire process took around 30–40 minutes and often resulted in manual errors.

---

### Task

Automate the deployment process to improve speed and reliability.

---

### Action

Implemented GitLab CI/CD pipeline.

Pipeline included:

```
Git Push

↓

Checkout Code

↓

Build

↓

Unit Tests

↓

SonarQube

↓

Docker Build

↓

Trivy Scan

↓

Push Image

↓

Deploy Kubernetes

↓

Smoke Tests
```

---

### Result

- Deployment time reduced significantly
- Manual effort eliminated
- Consistent deployments
- Faster rollback
- Improved developer productivity

---

## Example 2 – Infrastructure Automation

Situation

Infrastructure provisioning was done manually using AWS Console.

Task

Automate AWS resource provisioning.

Action

Implemented Terraform modules for:

- VPC
- EC2
- IAM
- ALB
- Security Groups

Integrated Terraform into GitLab CI/CD.

Result

- Infrastructure provisioning became repeatable
- Reduced provisioning time
- Infrastructure stored in Git
- Easier disaster recovery

---

## Example 3 – Monitoring Automation

Situation

Engineers manually monitored production servers.

Task

Automate infrastructure monitoring.

Action

Configured:

- Prometheus
- Grafana
- Alertmanager

Alerts were sent to Slack and Email.

Result

- Faster incident detection
- Reduced MTTR
- Improved uptime

---

## Example 4 – Docker Cleanup Automation

Situation

Docker images accumulated on servers causing disk space issues.

Task

Automate cleanup.

Action

Developed a scheduled Bash script to remove:

- Dangling images
- Stopped containers
- Unused volumes

Result

- Reduced disk usage
- Improved server stability

---

# 21. Common Python Interview Questions

---

## Why Python?

Python is widely used for automation because of its readability, rich ecosystem, and cloud SDK support.

---

## Which Python libraries have you used?

Examples

- requests
- boto3
- json
- yaml
- os
- subprocess
- pathlib
- logging

---

## What is subprocess?

Allows Python scripts to execute Linux commands.

Examples

- kubectl
- docker
- git
- terraform

---

## What is boto3?

Official AWS SDK for Python.

Used for:

- EC2
- S3
- IAM
- CloudWatch
- Lambda

---

## How do you call REST APIs?

Using the requests library.

---

## Have you worked with JSON?

Yes.

Examples

- API responses
- AWS outputs
- Terraform outputs

---

## Have you worked with YAML?

Yes.

Examples

- Kubernetes
- Docker Compose
- GitLab CI
- Helm Charts

---

## What is exception handling?

Exception handling allows scripts to gracefully recover from runtime failures instead of terminating unexpectedly.

---

## Difference between List and Dictionary?

List

Ordered collection.

Dictionary

Key-value collection.

---

## When would you choose Python instead of Bash?

Choose Python when:

- API integrations
- AWS automation
- Kubernetes automation
- Complex business logic
- Data processing

---

# 22. Common Bash Interview Questions

---

## Why Bash?

Bash is lightweight and ideal for Linux automation.

---

## Difference between Bash and Shell?

Shell is the command interpreter.

Bash is one implementation of a shell.

---

## Difference between Bash and Python?

| Bash             | Python             |
| ---------------- | ------------------ |
| Linux automation | Complex automation |
| Shell commands   | APIs               |
| Quick scripts    | Large applications |

---

## How do you debug a Bash script?

```bash
bash -x script.sh
```

---

## How do you schedule a script?

Using Cron.

---

## How do you pass variables?

Environment Variables

or

Command-line arguments

---

## Difference between grep, awk and sed?

grep

Search text.

awk

Process columns and structured text.

sed

Modify text.

---

## Difference between df and du?

df

Filesystem usage.

du

Directory usage.

---

## Common Linux Commands

Know the purpose of:

```bash
grep
find
awk
sed
cut
sort
uniq
head
tail
curl
wget
chmod
chown
systemctl
journalctl
tar
zip
unzip
```

---

# 23. Scenario-Based Interview Questions

These questions evaluate your troubleshooting and automation approach.

---

## Scenario 1

Disk usage reaches 95%.

Approach

```
Check Disk

↓

Find Large Files

↓

Archive Logs

↓

Delete Old Files

↓

Notify Team
```

---

## Scenario 2

Application deployment failed.

Approach

```
Pipeline Logs

↓

Build Logs

↓

Docker Build

↓

Deployment Logs

↓

Kubernetes Events

↓

Fix Issue

↓

Redeploy
```

---

## Scenario 3

A Kubernetes Pod keeps restarting.

Approach

```
kubectl describe pod

↓

kubectl logs

↓

Check Readiness Probe

↓

Check Liveness Probe

↓

Check Events

↓

Fix Root Cause
```

---

## Scenario 4

An EC2 instance is unreachable.

Checklist

- Security Groups
- NACL
- Route Tables
- Internet Gateway
- SSH
- EC2 Status
- Disk Space

---

## Scenario 5

Pipeline suddenly starts failing.

Investigate

- Recent commits
- Build logs
- Test failures
- SonarQube
- Security scans
- Docker build
- Registry
- Deployment logs

---

## Scenario 6

High CPU usage.

Approach

```
Check Processes

↓

Application Logs

↓

Resource Usage

↓

Scaling Required?

↓

Investigate Root Cause
```

---

## Scenario 7

SSL certificate expires tomorrow.

Approach

- Renew certificate
- Validate installation
- Restart services
- Verify HTTPS
- Update monitoring

---

## Scenario 8

Docker host runs out of disk space.

Actions

- Remove unused images
- Remove stopped containers
- Remove unused volumes
- Archive logs
- Investigate storage growth

---

# 24. Hands-on Coding Problems

These are common coding exercises in DevOps interviews.

---

## Bash

### Problem 1

Find the largest files.

Expected Approach

- Use find
- Sort results
- Display largest files

---

### Problem 2

Find ERROR entries.

Expected Approach

- grep
- Count occurrences
- Generate report

---

### Problem 3

Check if a service is running.

Expected Approach

- systemctl status
- Restart if stopped
- Log the action

---

### Problem 4

Archive logs older than 30 days.

Expected Approach

- Find old logs
- Compress
- Move to archive
- Delete originals

---

### Problem 5

Display top CPU-consuming processes.

Expected Approach

- Read process list
- Sort by CPU
- Display top results

---

## Python

### Problem 1

Read a JSON file.

Expected Approach

- Load JSON
- Parse values
- Display required fields

---

### Problem 2

Read a YAML file.

Expected Approach

- Parse YAML
- Extract configuration

---

### Problem 3

Call a REST API.

Expected Approach

- Send GET request
- Validate response
- Parse JSON
- Display results

---

### Problem 4

List EC2 instances.

Expected Approach

- boto3
- Describe instances
- Print names and status

---

### Problem 5

Check disk usage.

Expected Approach

- Read filesystem usage
- Compare threshold
- Generate alert

---

### Problem 6

Parse log files.

Expected Approach

- Read logs
- Count ERROR entries
- Generate summary

---

# 25. 5-Minute Revision

Remember:

- Bash → Linux automation
- Python → Cloud automation
- boto3 → AWS SDK
- requests → REST APIs
- subprocess → Execute shell commands
- JSON → API responses
- YAML → Kubernetes & CI/CD
- Terraform → Infrastructure automation
- GitLab CI → Deployment automation
- Prometheus → Monitoring
- Grafana → Visualization
- Alertmanager → Notifications
- Kubernetes → Container orchestration

---

# 26. Final Interview Tips

## Technical

- Think before writing code.
- Explain your approach first.
- Choose the right tool (Bash vs Python).
- Mention error handling.
- Consider logging and monitoring.
- Discuss scalability and maintainability.

---

## Behavioral

When discussing your automation work, always explain:

- Why the automation was needed
- What challenges existed
- What solution you implemented
- Technologies used
- Results achieved
- Business impact

---

## Most Common Interview Questions

Be prepared to confidently answer:

1. Explain your CI/CD pipeline.
2. Walk me through your Terraform implementation.
3. Tell me about an automation you built.
4. Bash vs Python.
5. How do you automate AWS infrastructure?
6. How do you automate Kubernetes deployments?
7. How do you troubleshoot a failed deployment?
8. How do you monitor production systems?
9. How do you handle secrets securely?
10. What improvements have you made in your previous project?

---

# Final Takeaway

For a **6-year DevOps Engineer**, interviewers are not evaluating whether you can memorize Python syntax or Linux commands. They want to understand how you approach automation, troubleshoot production issues, and build reliable, scalable systems.

A strong answer should demonstrate:

- Practical automation experience
- Structured problem-solving
- Familiarity with cloud platforms, Kubernetes, CI/CD, and Infrastructure as Code
- The ability to explain real-world projects and their business impact

Focus on **how you think, automate, and solve problems** rather than just listing tools or commands. That is what distinguishes an experienced DevOps Engineer from someone who has only theoretical knowledge.

---
