# 1. AWS Global Infrastructure

AWS provides cloud infrastructure across the world.

It consists of:

- Regions
- Availability Zones (AZs)
- Edge Locations

---

## Region

A Region is a geographical location where AWS has one or more data centers.

Examples

- Mumbai (ap-south-1)
- Singapore
- Frankfurt
- Virginia

Each Region is isolated from other Regions.

---

## Availability Zone (AZ)

Each Region contains multiple Availability Zones.

Example

```
Mumbai Region

↓

AZ-a

AZ-b

AZ-c
```

Each AZ is an independent data center with:

- Separate Power
- Separate Cooling
- Separate Networking

---

Why Multiple AZs?

High Availability.

If one AZ fails,

Application continues from another AZ.

---

## Edge Locations

Used by

- CloudFront
- Route53

Purpose

Deliver content closer to users with low latency.

---

# Interview Question

Difference between Region and Availability Zone?

Region

Geographical location.

Availability Zone

One or more isolated data centers inside a Region.

---

# 2. IAM (Identity and Access Management)

IAM controls

**Who can access What.**

Authentication + Authorization.

---

## IAM Components

- Users
- Groups
- Roles
- Policies

---

### IAM User

Represents one person.

Example

Developer

DevOps

Admin

---

### IAM Group

Collection of users.

Example

Developers Group

↓

Developer1

Developer2

Developer3

Assign permissions once to the group.

---

### IAM Policy

JSON document defining permissions.

Example

Allow

- EC2
- S3

Deny

- IAM Delete

---

### IAM Role

Temporary permissions.

Commonly used by AWS Services.

Example

EC2

↓

IAM Role

↓

Access S3

No Access Keys required.

---

## Authentication vs Authorization

Authentication

Who are you?

Authorization

What are you allowed to do?

---

## Least Privilege Principle

Always grant only the minimum permissions required.

Never attach AdministratorAccess unless absolutely necessary.

---

## Interview Questions

### IAM User vs Role?

User

Permanent identity.

Role

Temporary identity.

---

### Why use IAM Roles instead of Access Keys?

Roles provide temporary credentials, rotate automatically, and eliminate the need to store long-lived access keys.

---

### What is an IAM Policy?

A JSON document that defines allowed or denied actions on AWS resources.

---

### What is the Principle of Least Privilege?

Grant only the permissions required to perform a task—nothing more.

---

# Quick Revision

- Region = Geographic Location
- AZ = Data Center
- IAM = Authentication + Authorization
- User = Person
- Group = Collection of Users
- Role = Temporary Permissions
- Policy = JSON Permissions
- Least Privilege = Best Practice

---

# 3. Amazon EC2 (Elastic Compute Cloud)

## What is EC2?

Amazon EC2 is a virtual server in AWS that allows you to run applications in the cloud.

Think of it as renting a Linux or Windows machine on demand.

---

## Why EC2?

Instead of buying physical servers:

```
Purchase Server

↓

Rack & Stack

↓

Install OS

↓

Configure Network

↓

Deploy Application
```

With AWS:

```
Launch EC2 Instance

↓

SSH into Instance

↓

Deploy Application
```

Provisioning takes only a few minutes.

---

## EC2 Components

### AMI (Amazon Machine Image)

An AMI is a template used to launch an EC2 instance.

It includes:

- Operating System
- Installed Software
- Configuration

Examples:

- Amazon Linux
- Ubuntu
- Red Hat
- Windows Server

---

### Instance Types

Instance types define CPU, Memory, Storage, and Network capacity.

Examples:

| Family | Use Case                  |
| ------ | ------------------------- |
| t3     | General Purpose           |
| m5     | Balanced Compute & Memory |
| c5     | Compute Optimized         |
| r5     | Memory Optimized          |

---

### Key Pair

Used for secure SSH access to Linux instances.

```bash
ssh -i my-key.pem ec2-user@<public-ip>
```

Private key stays with the user.

Public key is stored on the EC2 instance.

---

### Security Group

Acts as a virtual firewall for an EC2 instance.

Example:

| Port | Protocol | Purpose        |
| ---- | -------- | -------------- |
| 22   | SSH      | Remote Login   |
| 80   | HTTP     | Website        |
| 443  | HTTPS    | Secure Website |

Security Groups are **stateful**.

If inbound traffic is allowed, the response is automatically allowed.

---

### User Data

User Data is a script that runs automatically when an EC2 instance starts for the first time.

Example:

```bash
#!/bin/bash
yum update -y
yum install docker -y
systemctl start docker
```

Common use cases:

- Install packages
- Configure software
- Pull Docker images
- Start services

---

# EC2 Lifecycle

```
Launch

↓

Running

↓

Stopped

↓

Running

↓

Terminated
```

A terminated instance cannot be restarted.

---

# Interview Questions

### What is an AMI?

A reusable template used to launch EC2 instances.

---

### What is User Data?

A startup script executed automatically when an EC2 instance boots for the first time.

---

### Can you change the instance type?

Yes.

The instance must usually be stopped before changing its type.

---

### Difference between Stop and Terminate?

Stop:

- Instance can be started again.
- EBS volumes are preserved.

Terminate:

- Instance is permanently deleted.
- Root volume is deleted by default unless configured otherwise.

---

# 4. Amazon VPC (Virtual Private Cloud)

## What is a VPC?

A VPC is a logically isolated virtual network in AWS where you launch AWS resources.

It gives you complete control over:

- IP Address Range
- Subnets
- Route Tables
- Internet Access
- Security

---

## VPC Architecture

```
                VPC (10.0.0.0/16)

        -------------------------------

        Public Subnet

        10.0.1.0/24

          EC2 (Web)

              |

      Internet Gateway

              |

           Internet

        -------------------------------

        Private Subnet

        10.0.2.0/24

          App Server

          Database

              |

         NAT Gateway

              |

      Internet Gateway
```

---

## Public Subnet

A subnet that has a route to the Internet Gateway.

Used for:

- Web Servers
- Bastion Hosts
- Load Balancers

---

## Private Subnet

No direct route to the Internet.

Used for:

- Databases
- Backend APIs
- Internal Applications

More secure than public subnets.

---

## Internet Gateway (IGW)

Allows resources in a public subnet to communicate with the Internet.

Without an IGW, public instances cannot access the Internet.

---

## NAT Gateway

Allows instances in private subnets to access the Internet **without allowing inbound Internet traffic**.

Example:

Private EC2

↓

NAT Gateway

↓

Internet Gateway

↓

Internet

Common use cases:

- Download software updates
- Access package repositories
- Pull Docker images

---

## Route Table

A Route Table determines where network traffic should be sent.

Example:

```
Destination       Target

0.0.0.0/0         Internet Gateway
```

Public Subnet:

Has a default route to the Internet Gateway.

Private Subnet:

Has a default route to the NAT Gateway.

---

# Security Group vs NACL

| Security Group          | NACL               |
| ----------------------- | ------------------ |
| Instance Level          | Subnet Level       |
| Stateful                | Stateless          |
| Allow Rules Only        | Allow & Deny Rules |
| Default: Deny Inbound   | Configurable       |
| Default: Allow Outbound | Configurable       |

---

## Stateful vs Stateless

### Security Group (Stateful)

If inbound SSH is allowed:

```
Laptop

↓

EC2

↓

Response Automatically Allowed
```

No outbound rule is required for the return traffic.

---

### NACL (Stateless)

Both inbound and outbound rules must explicitly allow the traffic.

---

# Interview Questions

### What is the difference between a Public and Private Subnet?

Public Subnet:

Has a route to the Internet Gateway.

Private Subnet:

Does not have a direct route to the Internet.

---

### Why do we need a NAT Gateway?

To allow outbound Internet access for resources in private subnets while preventing inbound Internet access.

---

### Why are databases deployed in private subnets?

For security.

Databases should not be directly accessible from the Internet.

---

### Security Group vs NACL?

Security Group:

- Instance level
- Stateful
- Allow rules only

NACL:

- Subnet level
- Stateless
- Supports both allow and deny rules

---

# Production Architecture

```
                Internet

                    |

              Application Load Balancer

                    |

        ------------------------------

        Public Subnet

        ALB

        Bastion Host

        ------------------------------

        Private Subnet

        Application Servers

        Kubernetes Worker Nodes

        ------------------------------

        Private Database Subnet

        Amazon RDS

        ------------------------------
```

This design provides:

- High Availability
- Security
- Scalability
- Separation of concerns

---

# 5-Minute Revision

- EC2 = Virtual Server
- AMI = Machine Image
- Instance Type = CPU + Memory
- Key Pair = SSH Access
- Security Group = Stateful Firewall
- User Data = Startup Script
- VPC = Private Network
- Public Subnet = Internet Access
- Private Subnet = No Direct Internet
- Internet Gateway = Internet Connectivity
- NAT Gateway = Outbound Internet for Private Subnets
- Route Table = Traffic Routing
- Security Group = Instance Level
- NACL = Subnet Level

---

# Advanced & Experience-Based AWS Interview Questions

## Q1. Explain a highly available architecture for a web application on AWS.

**Answer:**

A typical production architecture is:

```
Users

↓

Route53

↓

Application Load Balancer (ALB)

↓

Auto Scaling Group

↓

EC2 Instances (Private Subnets)

↓

Amazon RDS (Multi-AZ)

↓

Amazon S3 (Static Files & Backups)
```

**Key Points:**

- Route53 provides DNS resolution.
- ALB distributes traffic across healthy EC2 instances.
- Auto Scaling adds/removes EC2 instances based on demand.
- RDS Multi-AZ ensures database high availability.
- S3 stores static content and backups.

---

## Q2. Explain the request flow from a browser to an EC2 instance.

**Answer:**

```
User

↓

Route53

↓

Application Load Balancer

↓

Target Group

↓

EC2 Instance

↓

Application

↓

Response

↓

User
```

If the application accesses a database:

```
EC2

↓

Amazon RDS
```

---

## Q3. Why should databases be placed in a Private Subnet?

**Answer:**

Databases should never be directly exposed to the Internet.

Benefits:

- Improved security.
- Accessible only from application servers.
- Reduced attack surface.
- Easier compliance with security best practices.

Typical Architecture:

```
Internet

↓

ALB

↓

Application EC2

↓

RDS (Private Subnet)
```

---

## Q4. Your EC2 instance cannot access the Internet. How would you troubleshoot?

**Answer:**

Check the following in order:

1. Is the EC2 instance in the correct subnet?
2. Does the subnet have the correct Route Table?
3. Is there an Internet Gateway (public subnet) or NAT Gateway (private subnet)?
4. Is the Security Group allowing outbound traffic?
5. Is the Network ACL allowing traffic?
6. Does the instance have a Public IP (if in a public subnet)?

This structured approach is what interviewers look for.

---

## Q5. Your website is inaccessible. How would you troubleshoot?

**Answer:**

Check:

- EC2 instance status.
- Application/service running.
- Security Group rules.
- Network ACL rules.
- Route Tables.
- Load Balancer health checks.
- Target Group registration.
- DNS (Route53).
- CloudWatch metrics and logs.

Start troubleshooting from the user-facing layer and work inward.

---

## Q6. Security Group vs Network ACL?

**Answer:**

| Security Group                      | Network ACL                               |
| ----------------------------------- | ----------------------------------------- |
| Instance Level                      | Subnet Level                              |
| Stateful                            | Stateless                                 |
| Allow Rules Only                    | Allow & Deny Rules                        |
| Automatically Allows Return Traffic | Return Traffic Must Be Explicitly Allowed |

**Use Security Groups for application-level access control and NACLs for subnet-level protection.**

---

## Q7. Why use an IAM Role instead of Access Keys on EC2?

**Answer:**

IAM Roles provide temporary credentials that are automatically rotated.

Advantages:

- No hardcoded credentials.
- Improved security.
- Easier management.
- Recommended AWS best practice.

Example:

```
EC2

↓

IAM Role

↓

S3 Access
```

---

## Q8. Explain the difference between an Internet Gateway and a NAT Gateway.

**Internet Gateway (IGW)**

- Enables inbound and outbound Internet access.
- Used with Public Subnets.

**NAT Gateway**

- Allows outbound Internet access only.
- Used by resources in Private Subnets.

---

## Q9. What is an Auto Scaling Group (ASG)?

**Answer:**

An Auto Scaling Group automatically adjusts the number of EC2 instances based on demand.

Benefits:

- High Availability.
- Cost Optimization.
- Automatic Scaling.
- Self-Healing.

Example:

```
CPU > 70%

↓

Launch New EC2

↓

Traffic Balanced

↓

CPU Normal

↓

Terminate Extra EC2
```

---

## Q10. What is the difference between ALB and NLB?

| ALB                       | NLB                          |
| ------------------------- | ---------------------------- |
| Layer 7 (HTTP/HTTPS)      | Layer 4 (TCP/UDP)            |
| Path & Host-Based Routing | High Performance TCP Routing |
| SSL Termination           | Pass-through Supported       |
| Web Applications          | Low-Latency Applications     |

Use ALB for most web applications and NLB for TCP-based or high-performance workloads.

---

## Q11. EBS vs EFS?

| EBS                   | EFS                    |
| --------------------- | ---------------------- |
| Block Storage         | Network File Storage   |
| Single EC2 Instance   | Multiple EC2 Instances |
| High Performance      | Shared Storage         |
| One Availability Zone | Multi-AZ               |

Use EBS for operating systems and databases, and EFS for shared application data.

---

## Q12. S3 vs EBS?

| S3                    | EBS                          |
| --------------------- | ---------------------------- |
| Object Storage        | Block Storage                |
| Unlimited Scalability | Attached to EC2              |
| Static Files          | Operating System & Databases |
| Accessed via API      | Mounted as a Disk            |

---

## Q13. How do you securely store application secrets?

**Answer:**

Do not hardcode secrets.

Use:

- AWS Secrets Manager.
- AWS Systems Manager Parameter Store.
- IAM Roles.
- KMS for encryption.

---

## Q14. How have you used AWS in your CI/CD pipeline?

**Sample Answer:**

Our pipeline:

```
Developer

↓

GitLab CI

↓

Docker Build

↓

Push Image to Amazon ECR

↓

Update Kubernetes Deployment

↓

Amazon EKS

↓

Application Updated
```

Infrastructure changes are managed separately using Terraform.

---

## Q15. What AWS services have you used in your projects?

**Sample Answer:**

"In my projects, I've primarily worked with IAM, EC2, VPC, Security Groups, ALB, Auto Scaling, S3, ECR, CloudWatch, Route53, and EKS. Infrastructure provisioning was automated using Terraform, while deployments were handled through GitLab CI/CD."

---

# Interview Tips

- Draw architecture diagrams whenever possible.
- Explain the request flow clearly.
- Mention **High Availability**, **Security**, **Scalability**, and **Cost Optimization** in architecture questions.
- Use AWS best practices such as IAM Roles, Multi-AZ deployments, private subnets for databases, and Infrastructure as Code.
- When troubleshooting, follow a systematic approach instead of guessing.

---

# 5 Most Common AWS Questions

1. Explain your AWS architecture.
2. Security Group vs Network ACL.
3. Public vs Private Subnet.
4. How does an Application Load Balancer work?
5. How do you troubleshoot an EC2 instance that is unreachable?

If you can answer these confidently, you're well prepared for most DevOps interviews.
