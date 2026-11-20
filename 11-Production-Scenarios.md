# 11-Production-Scenarios.md

# Production Troubleshooting & Incident Response Handbook

---

# Introduction

This is the most important section for mid-level and senior DevOps interviews.

Most interviewers are not interested in definitions.

Instead of asking:

```text id="a1u2v3"
What is Kubernetes?
```

they will ask:

```text id="b2u3v4"
Application is down.
What will you do?
```

The goal is to demonstrate:

- Troubleshooting mindset
- Structured investigation
- Production experience
- RCA methodology
- Communication skills

---

# Golden Rule

Never jump directly to conclusions.

Always follow:

```text id="c3u4v5"
Detect
 ↓
Assess Impact
 ↓
Collect Evidence
 ↓
Isolate Problem
 ↓
Fix
 ↓
Validate
 ↓
RCA
```

---

# Universal Troubleshooting Framework

Whenever any production issue occurs:

```text id="d4u5v6"
1. What is broken?
2. When did it start?
3. What changed?
4. Who is affected?
5. What metrics changed?
6. What logs show?
7. What is the root cause?
```

Interview Answer:

I first determine impact and severity, then correlate metrics, logs, recent deployments, infrastructure health, and dependency status before applying a fix.

---

# Scenario 1

# Application Is Down

Question:

Users report application outage.

---

Investigation Flow

```text id="e5u6v7"
Application
 ↓
Pods
 ↓
Services
 ↓
Ingress
 ↓
Load Balancer
 ↓
DNS
```

---

Steps

Check application endpoint:

```bash id="f6u7v8"
curl application-url
```

Check pods:

```bash id="g7u8v9"
kubectl get pods
```

Check logs:

```bash id="h8u9v0"
kubectl logs pod-name
```

Check ingress:

```bash id="i9u0v1"
kubectl get ingress
```

---

Interview Answer:

I would verify whether the application itself is unavailable or if traffic routing components such as Ingress, Services, DNS, or Load Balancers are causing the outage.

---

# Scenario 2

# Pod CrashLoopBackOff

Question:

Pods continuously restart.

---

Investigation

```bash id="j0u1v2"
kubectl describe pod
```

```bash id="k1u2v3"
kubectl logs pod-name
```

Check:

```text id="l2u3v4"
Application Errors
Missing Secrets
ConfigMap Issues
Database Connectivity
Memory Limits
```

---

Interview Answer:

CrashLoopBackOff typically indicates the container starts successfully but crashes shortly afterward due to application failures, configuration issues, dependency failures, or insufficient resources.

---

# Scenario 3

# Pod Pending

Question:

Pod remains pending for several minutes.

---

Investigation

```bash id="m3u4v5"
kubectl describe pod
```

Common Causes:

```text id="n4u5v6"
Insufficient CPU
Insufficient Memory
PVC Issues
Taints
Node Selector Mismatch
```

---

Interview Answer:

Pending status usually indicates scheduling problems where Kubernetes cannot find a suitable node that satisfies resource or placement requirements.

---

# Scenario 4

# High CPU Usage

Question:

CPU suddenly jumps to 95%.

---

Investigation

Linux:

```bash id="o5u6v7"
top
```

Kubernetes:

```bash id="p6u7v8"
kubectl top pods
```

Cloud:

```text id="q7u8v9"
CloudWatch
Grafana
Prometheus
```

---

Check:

```text id="r8u9v0"
Traffic Spike
Infinite Loops
Recent Deployment
Background Jobs
```

---

Interview Answer:

I would compare current traffic patterns with historical baselines and identify which process or container is consuming CPU resources before determining remediation.

---

# Scenario 5

# High Memory Usage

Question:

Application becomes slow and memory reaches 95%.

---

Investigation

```bash id="s9u0v1"
free -h
```

```bash id="t0u1v2"
kubectl top pods
```

---

Possible Causes:

```text id="u1u2v3"
Memory Leak
Large Cache
Unreleased Objects
Application Bug
```

---

Interview Answer:

Gradually increasing memory usage often indicates memory leaks, while sudden spikes may indicate traffic surges or inefficient processing.

---

# Scenario 6

# Disk Full

Question:

Production server reaches 100% disk usage.

---

Investigation

```bash id="v2u3v4"
df -h
```

```bash id="w3u4v5"
du -sh /*
```

---

Common Causes:

```text id="x4u5v6"
Logs
Backups
Docker Images
Temp Files
```

---

Interview Answer:

The first priority is identifying the largest consumers of storage, then determining whether cleanup, log rotation, or storage expansion is required.

---

# Scenario 7

# Application Slow

Question:

Users report slow response times.

---

Investigation Flow

```text id="y5u6v7"
Application
 ↓
Database
 ↓
Network
 ↓
Infrastructure
```

---

Check:

```text id="z6u7v8"
CPU
Memory
Latency
Database Queries
Dependencies
```

---

Interview Answer:

Application slowness often originates from database bottlenecks, external service dependencies, inefficient queries, or infrastructure saturation.

---

# Scenario 8

# Database Slow

Question:

RDS response times increased.

---

Check:

```text id="a7u8v9"
CPU
Connections
Memory
IOPS
Latency
Slow Queries
```

---

Interview Answer:

I would identify whether the bottleneck is resource related or query related before deciding between optimization and scaling.

---

# Scenario 9

# Deployment Failed

Question:

Production deployment failed.

---

Investigation

Pipeline:

```text id="b8u9v0"
Build
Test
Deploy
```

---

Check:

```text id="c9u0v1"
Pipeline Logs
Deployment Events
Image Availability
Secrets
ConfigMaps
```

---

Interview Answer:

I first determine whether the issue originated during build, image creation, deployment, or application startup.

---

# Scenario 10

# Recent Release Broke Production

Question:

Users report failures immediately after deployment.

---

Investigation

```text id="d0u1v2"
Deployment Time
 ↓
Error Increase
 ↓
Rollback Decision
```

---

Interview Answer:

If customer impact is significant, I prioritize rollback to restore service quickly and then perform detailed investigation separately.

---

# Scenario 11

# Ingress Not Routing Traffic

Investigation

```bash id="e1u2v3"
kubectl get ingress
```

```bash id="f2u3v4"
kubectl describe ingress
```

---

Check:

```text id="g3u4v5"
Host Rules
TLS
Backend Services
Endpoints
```

---

Interview Answer:

Ingress issues are often caused by incorrect routing rules, unhealthy backend services, or certificate misconfigurations.

---

# Scenario 12

# DNS Issue

Question:

Application inaccessible via URL but works via IP.

---

Investigation

```bash id="h4u5v6"
nslookup domain
```

```bash id="i5u6v7"
dig domain
```

---

Interview Answer:

This usually indicates DNS misconfiguration, propagation issues, or incorrect load balancer mappings.

---

# Scenario 13

# AWS EC2 Unreachable

Check:

```text id="j6u7v8"
Security Group
NACL
Route Table
OS Status
```

---

Interview Answer:

I would first determine whether the issue is network-related or operating-system-related before deeper investigation.

---

# Scenario 14

# S3 Access Denied

Check:

```text id="k7u8v9"
IAM Policy
Bucket Policy
Role Permissions
```

---

Interview Answer:

Most S3 access issues originate from permission conflicts between IAM policies and bucket policies.

---

# Scenario 15

# Container Image Pull Failure

Error:

```text id="l8u9v0"
ImagePullBackOff
```

---

Check:

```text id="m9u0v1"
Registry Access
Image Name
Credentials
Network
```

---

Interview Answer:

Image pull failures usually occur because the image does not exist, credentials are invalid, or network connectivity is unavailable.

---

# Scenario 16

# Monitoring Alert Triggered

Question:

CPU Alert Received at 2 AM.

---

Approach

```text id="n0u1v2"
Validate Alert
 ↓
Determine Impact
 ↓
Collect Metrics
 ↓
Investigate Logs
 ↓
Fix
```

---

Interview Answer:

I never assume the alert itself is the problem. I first verify whether the alert reflects a genuine production issue.

---

# Scenario 17

# Secret Accidentally Committed

Question:

AWS key committed to Git repository.

---

Actions

```text id="o1u2v3"
Revoke Key
Create New Key
Update Systems
Remove Secret
```

---

Interview Answer:

Credential rotation should happen immediately because removing the key from Git history alone does not eliminate exposure risk.

---

# Scenario 18

# Kubernetes Node Not Ready

Investigation

```bash id="p2u3v4"
kubectl get nodes
```

```bash id="q3u4v5"
kubectl describe node
```

---

Check:

```text id="r4u5v6"
Kubelet
Disk
Network
Resources
```

---

Interview Answer:

Node readiness issues usually originate from kubelet failures, resource exhaustion, networking issues, or underlying infrastructure problems.

---

# Incident Severity

Typical Classification

P1

```text id="s5u6v7"
Production Down
Revenue Impact
```

P2

```text id="t6u7v8"
Major Feature Impact
```

P3

```text id="u7u8v9"
Limited User Impact
```

P4

```text id="v8u9v0"
Minor Issue
```

---

# Root Cause Analysis

Every incident should answer:

```text id="w9u0v1"
What Happened?
Why?
Impact?
Fix?
Prevention?
```

---

# Strong RCA Example

Question:

Tell me about a production incident.

Interview Answer:

A production onboarding process started failing for specific customer records. Investigation showed records containing special characters were causing ingestion failures. We reviewed logs, identified the parsing issue, implemented a fix, validated onboarding success, and introduced additional input validation to prevent recurrence.

This is directly aligned with your Oracle experience and is a strong STAR-format answer.

---

# Top Production Interview Questions

1. Application down. What will you do?
2. High CPU troubleshooting.
3. High memory troubleshooting.
4. Disk full troubleshooting.
5. Pod CrashLoopBackOff.
6. Pod Pending.
7. Application slow.
8. Database slow.
9. Deployment failure.
10. Rollback strategy.
11. Ingress issues.
12. DNS issues.
13. EC2 unreachable.
14. S3 access denied.
15. ImagePullBackOff.
16. Monitoring alert investigation.
17. Secret leakage response.
18. Node NotReady.
19. RCA process.
20. Tell me about a production incident.

---

# Final Interview Rule

Never answer:

```text id="x0u1v2"
I would restart everything.
```

Always answer:

```text id="y1u2v3"
I would first gather evidence through metrics, logs, events, infrastructure health checks, and recent change history before taking corrective action.
```

That answer immediately differentiates a production engineer from someone who has only worked in labs.
