# 07-AWS.md

# AWS Interview Handbook

---

# AWS Overview

Amazon Web Services (AWS) is a cloud platform providing on-demand infrastructure, networking, storage, security, databases, monitoring, and application services.

Interview Answer:

In a DevOps environment, AWS provides the infrastructure layer while CI/CD pipelines, Kubernetes, monitoring, and automation tools run on top of it. Most interview questions focus on how services interact rather than individual service definitions.

---

# How a Request Reaches an Application

This is one of the most common interview questions.

Question:

What happens when a user enters:

```text id="k3ttgn"
www.company.com
```

---

Flow:

```text id="4gx1gl"
User
 ↓
DNS (Route53)
 ↓
Application Load Balancer
 ↓
Target Group
 ↓
EC2 / EKS Pods
 ↓
Application
 ↓
Database
```

Interview Answer:

When a user accesses an application URL, the browser first resolves the domain through DNS. Route53 returns the Load Balancer address. The request reaches the Application Load Balancer, which forwards traffic to healthy backend targets such as EC2 instances or Kubernetes Pods. The application processes the request and retrieves data from backend services or databases before returning a response.

---

# AWS Global Infrastructure

AWS consists of:

```text id="8pxy5t"
Region
  ↓
Availability Zone
  ↓
Data Center
```

Example:

```text id="42ec1m"
ap-south-1
   ↓
ap-south-1a
ap-south-1b
ap-south-1c
```

---

# Region vs Availability Zone

Interview Answer:

A Region is a geographical location containing multiple Availability Zones. An Availability Zone is an isolated data center designed to provide fault tolerance and high availability.

---

# VPC

A VPC is a logically isolated network in AWS where resources are deployed.

Interview Answer:

A VPC allows organizations to control IP ranges, routing, security boundaries, internet access, and communication between cloud resources.

---

# Public vs Private Subnet

Public Subnet:

```text id="5r3n4v"
Internet Accessible
```

Private Subnet:

```text id="58m3k7"
No Direct Internet Access
```

Interview Answer:

Public subnets contain internet-facing resources such as Load Balancers and Bastion Hosts. Private subnets contain backend applications and databases that should not be exposed directly to the internet.

---

# Internet Gateway

An Internet Gateway allows communication between a VPC and the public internet.

Interview Answer:

Without an Internet Gateway, resources inside the VPC cannot communicate directly with external networks.

---

# NAT Gateway

A NAT Gateway allows private resources to access the internet without exposing them publicly.

Example:

```text id="7i7qf6"
Private EC2
      ↓
NAT Gateway
      ↓
Internet
```

Interview Answer:

NAT Gateways are commonly used when private instances need to download updates, install packages, or access external APIs while remaining inaccessible from the internet.

---

# NAT Gateway vs Internet Gateway

Interview Answer:

An Internet Gateway provides bidirectional internet access, while a NAT Gateway only allows outbound internet access for private resources.

---

# Route Tables

Route Tables determine where network traffic should be forwarded.

Example:

```text id="fyp7wn"
0.0.0.0/0 → IGW
```

means internet traffic goes through the Internet Gateway.

Interview Answer:

Every subnet is associated with a Route Table that defines how packets travel inside and outside the VPC.

---

# Security Groups

Security Groups act as virtual firewalls attached to resources.

Interview Answer:

Security Groups are stateful, meaning return traffic is automatically allowed if the original request was permitted.

Example:

```text id="8hq5xv"
Allow:
443 HTTPS
80 HTTP
22 SSH
```

---

# Network ACL

Network ACLs operate at subnet level.

Interview Answer:

Network ACLs are stateless, meaning inbound and outbound rules must be explicitly defined.

---

# Security Group vs NACL

| Security Group    | NACL                  |
| ----------------- | --------------------- |
| Instance Level    | Subnet Level          |
| Stateful          | Stateless             |
| Allow Rules Only  | Allow and Deny Rules  |
| Easier Management | More Granular Control |

---

# EC2

EC2 provides virtual machines in AWS.

Interview Answer:

EC2 instances are used for hosting applications, running automation workloads, CI/CD runners, databases, and backend services.

---

# EC2 Lifecycle

```text id="mjlwmk"
Launch
 ↓
Running
 ↓
Stop
 ↓
Start
 ↓
Terminate
```

---

# IAM

IAM controls authentication and authorization in AWS.

Interview Answer:

IAM defines who can access AWS resources and what actions they are allowed to perform.

---

# IAM User vs Role

User:

```text id="tm0gx0"
Human Identity
```

Role:

```text id="9tqjza"
Temporary AWS Permission
```

Interview Answer:

Users are typically assigned to people, while Roles are assigned to services such as EC2, Lambda, or EKS workloads.

---

# Why Use IAM Roles?

Bad Practice:

```text id="c2rf3q"
Hardcoded Credentials
```

Good Practice:

```text id="5ml0a4"
IAM Role
```

Interview Answer:

IAM Roles eliminate the need to store long-term credentials inside applications.

---

# S3

S3 is AWS object storage.

Common Use Cases:

```text id="r4j8km"
Logs
Backups
Artifacts
Images
Static Websites
```

Interview Answer:

S3 provides highly durable and scalable object storage and is commonly used for backups, artifacts, logs, and Terraform state files.

---

# S3 Versioning

Versioning preserves previous object versions.

Interview Answer:

Versioning protects against accidental deletion and allows rollback to earlier object versions.

---

# S3 Lifecycle Policies

Example:

```text id="q2w54g"
Move to Glacier after 30 days
Delete after 365 days
```

Interview Answer:

Lifecycle policies automate storage cost optimization.

---

# EBS

EBS provides block storage for EC2.

Interview Answer:

EBS behaves similarly to a hard disk attached to a virtual machine and is commonly used for operating systems and application data.

---

# S3 vs EBS

| S3                 | EBS                        |
| ------------------ | -------------------------- |
| Object Storage     | Block Storage              |
| Highly Scalable    | Attached to EC2            |
| Accessible via API | Mounted as Disk            |
| Used for Files     | Used for Operating Systems |

---

# Load Balancers

Load Balancers distribute traffic across multiple targets.

Benefits:

- High Availability
- Fault Tolerance
- Scalability

---

# ALB

Application Load Balancer operates at Layer 7.

Interview Answer:

ALBs understand HTTP and HTTPS traffic and can route requests based on URLs, hostnames, and HTTP headers.

Example:

```text id="z5shob"
/api → backend
/ → frontend
```

---

# NLB

Network Load Balancer operates at Layer 4.

Interview Answer:

NLBs are optimized for extremely high throughput and low latency workloads.

---

# ALB vs NLB

| ALB           | NLB            |
| ------------- | -------------- |
| Layer 7       | Layer 4        |
| HTTP/HTTPS    | TCP/UDP        |
| Content Aware | Faster         |
| Path Routing  | Simple Routing |

---

# Auto Scaling

Auto Scaling automatically adjusts capacity.

Example:

```text id="gnz9rk"
High CPU
 ↓
Add Servers
```

Interview Answer:

Auto Scaling improves application availability while reducing infrastructure costs.

---

# CloudWatch

CloudWatch provides monitoring and alerting.

Metrics:

```text id="3y5crv"
CPU
Memory
Network
Disk
```

Interview Answer:

CloudWatch helps monitor resource health and automatically trigger alarms when thresholds are exceeded.

---

# RDS

RDS is AWS managed database service.

Examples:

```text id="mksyxv"
MySQL
PostgreSQL
Oracle
SQL Server
```

Interview Answer:

RDS automates backups, patching, replication, and maintenance tasks, reducing operational overhead.

---

# RDS Troubleshooting

Question:

Application is slow.

Interview Answer:

I would review database CPU utilization, memory usage, active connections, slow query logs, IOPS, storage latency, and performance insights to identify bottlenecks.

---

# Route53

Route53 provides DNS services.

Example:

```text id="m7rwdo"
www.company.com
```

resolves to:

```text id="9rmdx4"
ALB Endpoint
```

---

# VPC Peering

VPC Peering enables communication between VPCs.

Interview Answer:

VPC Peering creates a direct private connection between VPCs without requiring internet access.

---

# Transit Gateway

Transit Gateway simplifies large-scale network connectivity.

Instead of:

```text id="hgr99k"
VPC1 ↔ VPC2
VPC1 ↔ VPC3
VPC2 ↔ VPC3
```

Use:

```text id="l4s2ga"
Transit Gateway
      ↓
All VPCs
```

---

# AWS Security Best Practices

Interview Answer:

AWS environments should implement least privilege IAM policies, MFA enforcement, encryption at rest and in transit, private networking, security monitoring, centralized logging, and regular vulnerability assessments.

---

# Cost Optimization

Question:

How do you reduce AWS costs?

Interview Answer:

Cost optimization can be achieved through right-sizing resources, Auto Scaling, Reserved Instances, Savings Plans, storage lifecycle policies, eliminating idle infrastructure, and continuously monitoring usage patterns.

---

# Common Production Scenarios

## EC2 Unreachable

Check:

```text id="c9b7u4"
Security Groups
NACL
Route Tables
Instance Health
OS Logs
```

---

## S3 Access Denied

Check:

```text id="x3l3ns"
IAM Policy
Bucket Policy
Role Permissions
SCP
Permission Boundary
```

---

## High CPU on EC2

Check:

```text id="ql13ah"
top
CloudWatch
Application Logs
Traffic Patterns
```

---

## Database Slow

Check:

```text id="8m5rtg"
Connections
CPU
Memory
IOPS
Slow Queries
```

---

# AWS Topics You Must Know For Interviews

Focus Areas:

```text id="98qluv"
VPC
Subnets
Route Tables
Internet Gateway
NAT Gateway
Security Groups
NACL
IAM
EC2
S3
RDS
ALB
Auto Scaling
CloudWatch
Route53
```

---

# Honest Interview Positioning

For Your Profile:

Interview Answer:

My production experience has primarily been on OCI. For AWS, I have completed Cloud Practitioner level training, hands-on labs, and personal projects involving networking, IAM, compute, storage, monitoring, and deployment concepts. While I have not managed AWS production environments directly, I understand the architecture, services, and operational workflows and can quickly adapt to AWS-based projects.

---

# Top Interview Questions

1. Explain request flow from URL to application.
2. VPC architecture.
3. Public vs Private subnet.
4. Internet Gateway vs NAT Gateway.
5. Security Group vs NACL.
6. IAM User vs Role.
7. S3 vs EBS.
8. ALB vs NLB.
9. Auto Scaling.
10. CloudWatch.
11. Route53.
12. RDS troubleshooting.
13. Transit Gateway.
14. VPC Peering.
15. Cost Optimization.
16. EC2 troubleshooting.
17. S3 access denied troubleshooting.
18. High availability architecture.
19. Multi-AZ deployments.
20. AWS security best practices.
