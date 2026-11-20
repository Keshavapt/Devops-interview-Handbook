# AWS Interview Handbook

> **Target Audience:** 5+ Years Experience
> **Role:** Senior DevOps / Platform Engineer
> **Interview Weightage:** ⭐⭐⭐⭐⭐

---

## Table of Contents

- [1. AWS Global Infrastructure](#1-aws-global-infrastructure)
- [2. Request Flow to Application](#2-request-flow-to-application)
- [3. VPC & Networking](#3-vpc--networking)
- [4. EC2 (Compute)](#4-ec2-compute)
- [5. IAM (Identity & Access Management)](#5-iam-identity--access-management)
- [6. S3 (Storage)](#6-s3-storage)
- [7. Load Balancers](#7-load-balancers)
- [8. Auto Scaling](#8-auto-scaling)
- [9. RDS, CloudWatch, Route53](#9-rds-cloudwatch-route53)
- [10. EBS vs EFS vs S3](#10-ebs-vs-efs-vs-s3)
- [11. Production Scenarios](#11-production-scenarios)
- [12. Interview Questions & Answers](#12-interview-questions--answers)
- [13. Oracle DevOps Experience Positioning](#13-oracle-devops-experience-positioning)
- [14. 5-Minute Quick Revision Table](#14-5-minute-quick-revision-table)

---

## 1. AWS Global Infrastructure

| Component | Description |
| --------- | ----------- |
| Region | Geographic location (e.g., ap-south-1 Mumbai) |
| Availability Zone (AZ) | Isolated data center within a region |
| Edge Location | CDN endpoint for low-latency content delivery |

### Region vs Availability Zone

| Region | Availability Zone |
| ------ | ---------------- |
| Geographical area | Data center within a region |
| Multiple AZs per region | Isolated power, cooling, networking |
| Example: ap-south-1 | Example: ap-south-1a, ap-south-1b, ap-south-1c |

---

## 2. Request Flow to Application

### Multi-AZ 3-Tier Request Flow

One of the most common senior-level architecture questions.

```text
User -> Route53 (DNS) -> CloudFront (CDN) -> WAF -> ALB -> Target Group -> EC2/EKS (App Tier) -> RDS (Data Tier)
                                                            |
                                                        Auto Scaling Group
                                                            |
                                                    Multi-AZ (3 AZs for HA)
```

**Interview Answer:** When a user accesses an application URL, DNS resolution goes through Route53, which returns the CloudFront/ALB address. Traffic passes through WAF for security filtering, then hits the Application Load Balancer. The ALB distributes traffic across healthy targets in an Auto Scaling Group spread across multiple Availability Zones. The application tier processes requests and retrieves data from RDS (Multi-AZ for high availability). This architecture ensures no single point of failure.

### High Availability Design

```text
                    az-1                    az-2                    az-3
              +-----------+           +-----------+           +-----------+
              |   Public  |           |   Public  |           |   Public  |
              | Subnet: ALB|           | Subnet: ALB|           | Subnet: ALB|
              +-----------+           +-----------+           +-----------+
                          |                   |                   |
                          v                   v                   v
              +-----------+           +-----------+           +-----------+
              |  Private  |           |  Private  |           |  Private  |
              | Subnet: App|          | Subnet: App|          | Subnet: App|
              +-----------+           +-----------+           +-----------+
---

## 3. VPC & Networking

### VPC Components

| Component | Description |
| --------- | ----------- |
| VPC | Logically isolated virtual network in AWS |
| Subnet | Logical segment of VPC (public or private) |
| Internet Gateway (IGW) | Bidirectional internet access for public subnets |
| NAT Gateway | Outbound-only internet access for private subnets |
| Route Table | Defines traffic routing rules |
| Security Group | Stateful instance-level firewall |
| NACL | Stateless subnet-level firewall |

### Public vs Private Subnet

| Public Subnet | Private Subnet |
| ------------ | -------------- |
| Has route to IGW | No direct internet route |
| Contains ALB, Bastion Host | Contains app servers, databases |
| Internet-accessible | More secure - no direct internet access |

### Security Group vs NACL

| Security Group | NACL |
| -------------- | ---- |
| Instance-level | Subnet-level |
| Stateful | Stateless |
| Allow rules only | Allow and Deny rules |
| Default deny inbound | Configurable |

### NAT Gateway vs Internet Gateway

**Internet Gateway:** Bidirectional - allows both inbound and outbound internet access for public subnets.

**NAT Gateway:** Outbound only - private subnet instances can access the internet for updates/patches but cannot be accessed from the internet.

---

## 4. EC2 (Compute)

### What is EC2?

EC2 provides virtual servers in AWS Cloud with scalable compute capacity.

### EC2 Components

| Component | Description |
| --------- | ----------- |
| AMI | Machine image template (OS + software) |
| Instance Type | CPU + Memory (t3.micro, m5.large, c5.xlarge) |
| Key Pair | SSH access certificates |
| Security Group | Virtual firewall |
| User Data | Bootstrap script |

### EC2 Instance Types

| Family | Use Case | Example |
| ------ | -------- | ------- |
| T | Burstable, general purpose | t3.micro, t3.medium |
| M | Balanced compute/memory | m5.large, m5.xlarge |
| C | Compute-optimized | c5.large, c5.xlarge |
| R | Memory-optimized | r5.large, r5.xlarge |
| I | Storage-optimized | i3.large, i3.xlarge |

---

## 5. IAM (Identity & Access Management)

IAM controls **who can access what** through authentication and authorization.

### IAM Components

- **Users:** Permanent identity for people (Developer, DevOps, Admin)
- **Groups:** Collection of users with shared permissions
- **Roles:** Temporary identity for AWS services (EC2 accessing S3)
- **Policies:** JSON documents defining allowed/denied actions

### IAM User vs Role

| IAM User | IAM Role |
| -------- | -------- |
| Permanent identity | Temporary identity |
| For people | For AWS services |
| Long-lived credentials | Auto-rotated temporary credentials |

### Least Privilege Principle

Always grant only the minimum permissions required. Never attach `AdministratorAccess` unless absolutely necessary. Use IAM Roles instead of access keys for EC2 instances needing AWS access.

---
                          |                   |                   |
---

## 6. S3 (Storage)

S3 is object storage for unstructured data with unlimited scalability.

| Feature | Description |
| ------- | ----------- |
| Storage Class | Standard, IA, Glacier, Intelligent Tiering |
| Versioning | Keep multiple versions of objects |
| Lifecycle Policies | Auto-transition objects to cheaper storage |
| Encryption | SSE-S3, SSE-KMS, SSE-C, Client-side |
| Access Control | Bucket policies, IAM policies, ACLs |

**Use cases:** Static website hosting, backup, logs, data lakes, media storage.

---

## 7. Load Balancers

| Feature | ALB (Application) | NLB (Network) |
| ------- | ----------------- | ------------- |
| Layer | Layer 7 (HTTP/HTTPS) | Layer 4 (TCP/UDP) |
| Routing | Path & host-based | High-performance TCP routing |
| SSL Termination | Yes | Pass-through supported |
| Best For | Web applications | TCP-based, high-performance workloads |
| Target Type | IP, instance, Lambda | IP, instance |

---

## 8. Auto Scaling

Auto Scaling automatically adds or removes EC2 instances based on demand.

### How it Works

```text
High CPU -> CloudWatch Alarm -> Auto Scaling Group -> Launch New EC2 -> Traffic Balanced -> CPU Normal -> Terminate Extra EC2
```

### ASG Components

| Component | Purpose |
| --------- | ------- |
| Launch Template | Instance configuration (AMI, type, key pair, SG) |
| Auto Scaling Group | Logical grouping of EC2 instances |
| Scaling Policy | Rules for when to scale in/out |
| CloudWatch Alarm | Triggers scaling actions |
| Health Check | Replaces unhealthy instances |

### Benefits

- High availability (replaces unhealthy instances)
- Cost optimization (scale in during low demand)
- Automatic scale in/out based on metrics
- Self-healing

### Scaling Policies

- **Target Tracking:** Maintain a metric at target value (e.g., CPU at 50%)
- **Step Scaling:** Add/remove instances in steps based on alarm breach
- **Simple Scaling:** Add/remove fixed number of instances
- **Scheduled Scaling:** Scale based on predictable patterns

---

## 9. RDS, CloudWatch, Route53

### RDS (Relational Database Service)

Managed database service supporting MySQL, PostgreSQL, Oracle, SQL Server, MariaDB.

| Benefit | Description |
| ------- | ----------- |
| Automated backups | Point-in-time recovery |
| Patching | Automated OS and database patching |
| Multi-AZ replication | Synchronous standby for high availability |
| Read replicas | Asynchronous read scaling |

### CloudWatch

Monitoring and alerting service for AWS resources.

| Metric | Description |
| ------ | ----------- |
| CPU Utilization | EC2/RDS percentage |
| Memory Usage | Via CloudWatch agent |
| Network | Bytes in/out |
| Disk | Read/Write IOPS |

### Route53

DNS service routing domains to AWS resources (ALB, CloudFront, EC2).

| Routing Policy | Description |
| -------------- | ----------- |
| Simple | Single resource, no health check |
| Weighted | Route based on assigned weights |
| Latency-based | Route to lowest latency region |
| Geolocation | Route based on user location |
| Failover | Route to secondary on failure |

---

## 10. EBS vs EFS vs S3

| Feature | EBS | EFS | S3 |
| ------- | --- | --- | --- |
| Type | Block Storage | Network File Storage | Object Storage |
| Access | Single EC2 instance | Multiple EC2 instances | Any number of clients |
| Performance | High performance | Shared storage | Unlimited scalability |
| AZ Scope | Single Availability Zone | Multi-AZ | Region-wide |
| Use Case | OS, databases | Shared application data | Static files, backups |
| Access Method | Mounted as disk | Mounted via NFS v4 | Accessed via API |

**Use EBS** for operating systems and databases requiring high-performance block storage attached to a single EC2 instance.

**Use EFS** for shared application data accessed by multiple EC2 instances across AZs.

**Use S3** for object storage - static files, backups, data lakes, media content.

---
                          v                   v                   v
              +-----------+           +-----------+           +-----------+
              |  Private  |           |  Private  |           |  Private  |
              | Subnet: DB |          | Subnet: DB |          | Subnet: DB |
              +-----------+           +-----------+           +-----------+

Multi-AZ deployment: ALB in public subnets, App in private subnets, DB in isolated private subnets.
```
---

## 11. Production Scenarios

### Symptom → Root Cause → Diagnostic Commands

| Symptom | Likely Root Cause | Diagnostic Checks |
| ------- | ----------------- | ----------------- |
| EC2 Unreachable | Security Group, NACL, Route Table, health, OS logs | SG rules, NACL rules, route tables, instance status |
| S3 AccessDenied | IAM policy, bucket policy, IAM permissions, SCP | IAM policies, bucket policies, SCP restrictions |
| High CPU on EC2 | Under-provisioned, traffic spike, runaway process | `top`, CloudWatch metrics, application logs, traffic patterns |
| Database Slow | Inefficient queries, missing indexes, connection exhaustion | CloudWatch RDS metrics, slow query log, connections |

### EC2 Unreachable Playbook

1. Check Security Group rules (inbound/outbound)
2. Check NACL rules (subnet-level)
3. Check route tables (IGW/NAT Gateway routes)
4. Check instance status (system status, instance status)
5. Check OS logs and application logs

### S3 AccessDenied Playbook

1. Check IAM user/role policies
2. Check S3 bucket policy
3. Check IAM permissions boundaries
4. Check SCP (Service Control Policies)
5. Check encryption settings (KMS key access)

---

## 12. Interview Questions & Answers

### Q1. Explain the request flow from URL to application.

**Answer:** User → Route53 (DNS resolution) → CloudFront (CDN edge) → WAF (security) → ALB (Layer 7 load balancing) → Target Group → EC2/EKS instances in Auto Scaling Group → Application → RDS (database). The ALB distributes traffic across healthy targets across multiple AZs for high availability.

### Q2. Public vs Private Subnet?

**Answer:** Public subnets have a route to an Internet Gateway and contain internet-facing resources (ALB, bastion hosts). Private subnets have no direct internet route and contain backend resources (app servers, databases) accessed via NAT Gateway for outbound updates.

### Q3. Security Group vs NACL?

**Answer:** Security Groups are stateful, instance-level firewalls with allow-only rules and default deny inbound. NACLs are stateless, subnet-level firewalls supporting both allow and deny rules. Security Groups evaluate first, then NACLs.

### Q4. IAM User vs Role?

**Answer:** IAM User is a permanent identity for people with long-lived credentials. IAM Role is a temporary identity for AWS services that auto-rotates credentials. Use roles for EC2 instances needing AWS service access instead of storing access keys.

### Q5. EBS vs EFS?

**Answer:** EBS is block storage attached to a single EC2 instance - use for OS volumes and databases. EFS is network file storage accessible by multiple EC2 instances across AZs - use for shared application data. EBS is single-AZ; EFS is multi-AZ.

### Q6. S3 vs EBS?

**Answer:** S3 is object storage (unlimited scalability, API access) for static files and backups. EBS is block storage (attached to EC2, high performance) for OS and databases.

### Q7. ALB vs NLB?

**Answer:** ALB is Layer 7 (HTTP/HTTPS) with path/host-based routing and SSL termination - best for web applications. NLB is Layer 4 (TCP/UDP) with high throughput and low latency - best for TCP-based or high-performance workloads.

### Q8. How do you troubleshoot high CPU on EC2?

**Answer:** Check with `top` or CloudWatch metrics to identify the process. Review application logs for runaway processes or traffic spikes. Analyze traffic patterns. Consider scaling up (larger instance) or scaling out (more instances via ASG).

---

## 13. Oracle DevOps Experience Positioning

**Interview Answer:**

At Oracle, we primarily worked with IAM, EC2, VPC, Security Groups, ALB, Auto Scaling, S3, ECR, CloudWatch, Route53, and EKS. Infrastructure was provisioned using Terraform, while deployments were handled through GitLab CI/CD. We designed multi-AZ architectures for high availability, used private subnets for databases, IAM roles for service access (no access keys), and Auto Scaling groups for cost optimization and self-healing.

---

## 14. 5-Minute Quick Revision Table

| Topic | Key Concept |
| ----- | ----------- |
| Region | Geographic location with multiple AZs |
| AZ | Isolated data center within a region |
| VPC | Logically isolated virtual network |
| IGW | Bidirectional internet access |
| NAT Gateway | Outbound-only internet for private subnets |
| Security Group | Stateful instance-level firewall |
| NACL | Stateless subnet-level firewall |
| EC2 | Virtual servers in AWS |
| ASG | Auto scale EC2 instances based on demand |
| ALB | Layer 7 load balancer for HTTP/HTTPS |
| NLB | Layer 4 load balancer for TCP/UDP |
| RDS | Managed relational database |
| CloudWatch | Monitoring and alerting |
| Route53 | DNS service |
| EBS | Block storage for single EC2 instance |
| EFS | Shared file storage across EC2 instances |
| S3 | Object storage for static files |
| IAM | Identity and access management |
| Multi-AZ | High availability across availability zones |
