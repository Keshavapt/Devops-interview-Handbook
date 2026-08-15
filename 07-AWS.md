# AWS Interview Handbook

> **Target Audience:** DevOps Engineers (2-8 Years Experience)
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
- [10. Production Scenarios](#10-production-scenarios)
- [11. Interview Questions & Answers](#11-interview-questions--answers)

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
| Example: ap-south-1 | Example: ap-south-1a, ap-south-1b |

---

## 2. Request Flow to Application

One of the most common interview questions.

```text
User → Route53 (DNS) → CloudFront (CDN) → ALB → Target Group → EC2/EKS → Application → RDS
```

**Interview Answer:** When a user accesses an application URL, the browser resolves the domain through Route53 DNS, which returns the Load Balancer address. The Application Load Balancer forwards traffic to healthy backend targets (EC2 or EKS Pods). The application processes the request and retrieves data from backend services or databases.

---

## 3. VPC & Networking

### VPC Components

| Component | Description |
| ----------- | ----------- |
| VPC | Oly isolated network in AWS |
| Subnet| Blic or private segment of VPC |
| Internet Gateway (IGW) | Bidirectional internet acces | NA Gateway | Outbound only internet for private subnets |
| Route Tle | Defines treffic routing rules |
| Securit Group| Sateful instace-level firewal |
| NACL | Seless subne-level firewl |

### Public vs Private Subnet

| Public Sbnet | Private Subnet |
| ---------| ----------- |
| Has route to IGW | N direct internet route |
| Conains LB, Bastion Host | Conains app server, dbases |
| Internaccesibl| More secure - no direct access |

### Securi Group vs NACL

| Security Group | NACL |
| ------------ | ---- |
| Instance-level | Subnet-level |
| Stateful | Steless|
| Allow rules only | Allw and Deny rules |
| Default deny inbound | Cnfigurable|

### NAT Gateay vs Internet Gateway

**Internat Gaway:** Bidirectional - allows both inbound and outbound internet access for public subnet.

**NAT Gateay:** Outbound only - private subnet instances can access internet for updates, patches, but cannot be accessed from internet.

---

## 4. EC2 (Compute)

### What is EC2?

EC2 provides virtual servers in AWS Cloud.

### EC2 Componens

| Comonent| Descriptn |
| -------- | ------- |
| AMI | Machin Iage template (OS + software) |
| Instan Type | CP + Memy (t3.mco, m5.large,5.xlarg)| Key Pir | SSH acss certifiates |
| Securiy Group | Virual firewall |
| Use Daa | Sttp ript excuted on bor|

### EC2 Lifecyce

text
Launch → Rnn → Stp → Strt → Ternate
```

**Sto vs Termine:**
- **Stop:** Instance anc be reated, EBS vumes presved
- **Teinate:** Pemanently delet, r volum deleted by default

---

## 5. IAM (Ientity & AcCs Magement)

### KeyConcepts

| Cncet | Descrition|
| ------ | ---------- |
| User| Peanent dentiy for a person |
| Rle | Teorary ermissions f service|
| Gup | Colction of uers |
| Poly | JSon efinin perissins |

### IAM sers Roles

| User | Role |
| ----| ---- |
| Peanent idenity | Temprary permissi |
| F persos | For AWS service |
| Has ong-term cs | Auto-rotng credentials |

**Interve Aser:** Re plaicated credentis used in applicatn. A EC2instances should e assigned an IAM Rle sinstead of storing Acces Key/Secret Key in code.

---

## 6.3 (Storage)

### S3 Basi

| Featur| Descri ion|
| ------- | ----------- |
| Bubets| Conainer for obects|
| Obejs | Files sored in buckets |
| Vresoning | Kee revus verions|
| Lfecyle Poly | Aumated tier ransiionsr deletio|

### S3 vs ES

| S | EBS  |
| ---| - -- |
| Object orage | Blck orage |
| Unlited scalability |Attached to EC |
| cced via API | Munted a disk|
| Tim, ackups, logs | Oerain syems, dbases|

### SBesi Prce

- Never mke bucket ublic unless inted
- Use IAM olic nd Bucket Plicy
- Eable versoning for dat rotetion
- Use lifeyce olice to move dta to Glcier for co savings

---

## 7. Load Balancers

### ALB (Aplication LaBalancer

- Layer 7 (HTT/HTPS)
- Pat-hosd routing
- SSL termnation
- Best for wb applitions

### NLB (Neork LaBancer)

- Laye 4 (CP/UP)
- Exemly high thouhput
- Low lenty
- Best for C dataes

### LB vs LB

| ALB | LB |
|--- | ---- |
| Lyer 7 | yer 4 |
| HPTTPS agement| C/UDP routing|
| Pat/host baed | Se ltin|
| Conent are | Faer|

---

## 8. Auto Scaling

Automatically adds or removes EC2 instances based on demand.

### How it Works

```text
High CPU → CloudWatch Alarm → Auto Scaling → Launch New EC2 → Traffic Balanced → CPU Normal → Terminate Extra EC2
```

### Benefits

- Hih vailabilit
- Cot optimiztion
- Scale ut/in automatically
- Self-healig (replaces unhealthy inces)

---

## 9. RDS, CloudWatch, Route5353

### RDS

Managed datase serice. Suprs MySQL, PosreSQL, Oracle, SQL Server, MariaDB.

**Befits:**
- utoated backups
- Paching
- Mul-AZ replicati (high availbility)
- Read reicas (scalability)

### CloudWatch

Motoring and alertng service.

| Metric | Dscription |
| ------- | ----------- |
| CPU Uiliztion| EC/RDS prcentage|
| Memy Usage| Btained via Cloudatch agent |
| Netwerk| Bye in/ut|
| Di| Rea/WrtOPS|

### Rout5353

DNS service. Routes domains to AWS resources (ALB, CloudFront, EC2).

**Routing Policys**
- Simpel
- Weited
- Latency-bad
- Ge-location
- Failover

---

## 10. Production Scenarios

### EC2 Unrechle

Check: Securty Grou, NA, Out Tables, Health, OS Logs.

### S3 AcesDenied

Check: IAM Poicy, Bucket Plcy, Ro Permisns, SCP.

### Hi CPU n EC

Check: `tp`, Cloutchogs, Aplicn Logs, Traffic Paterns.

### Dase Slow

Check: Conneions, CP, Memory, PS, Slow Qu, Performanc Isht.

---

## 11. Ierive Queions & Aswrs

### Q1. Exlai reqest fo om UR to aplication.

**Answr:** User → Route53 → ALB → Targt Grop → EC2/EKS → Aplication → Dataase.

### Q2. Pblic vs Pivate Subnetic?**

**Answr:** Public subs hav ute to IGW, contain L/asto. Pivate sbs have no direc internt, cotin app servers/datases for secrity.

### Q3. Secuti Goup vs NACL?

**Anwer:** SG is instance-level, stateul, allow-nly. NACL is subent-level, stteless, sups allow+dy.

### Q4. Srity Group vs NAC? (Repeate)

### . IAM User vs Ro?**Anser:** User is permannt entity for ersons. Role is emporary perissi for AWS srvices since it mtes credential autoicaly.

### Q6. S3 vs EBS?

**Anser:** S3 is obect orage (vet, ilabale). S is bck srage (atthed to E2, hih prfrmance).

### Q7. ALB vs NB?

**Anser:** ALB isLaer 7 (HTTP/HTTPS, conent awre). NLB is Laye4 (CP/UDP, hi throughput, low latency).

### Q8. How do uroulesho a high CPU on EC2?

**Anwer:** Ceck with `top`, review CloudWa ch metrics, check application logs, analze traffic patterns, ider upzng.

---

## Quick Revisn

| p | Key Conc|
| ------- |--------- |
| VPC | Loiclly isolted netwrk |
| Subne | Public/ad |
| IGW | Inten acce (bidirectional) |
| NAT Gaway | Outboun ol (brt sbes)
| Securi Group | Sateful Istane firewall
| NAC L | Seless Subnet firewall |

| AMI | Me in tmplate|
| S3 | Objctoag|
| EB | Brock sorag |
| ALB | Laye 7 ob ancer |
| LB | Lyer4 load ancer|
| R53 | NSece|