# DevOps Interview Handbook

> **Target Audience:** 5+ Years Experience
> **Role:** Senior DevOps / Platform Engineer

---

## Master Index

| # | File | Focus |
|---|------|-------|
| 00 | [00-Core-DevOps-Fundamentals.md](00-Core-DevOps-Fundamentals.md) | DevOps Fundamentals |
| 01 | [01-Linux.md](01-Linux.md) | Linux Fundamentals & Troubleshooting |
| 02 | [02-Git-GitLab.md](02-Git-GitLab.md) | Git & GitLab CI/CD |
| 03 | [03-Docker.md](03-Docker.md) | Docker & Containerization |
| 04 | [04-Kubernetes.md](04-Kubernetes.md) | Kubernetes Architecture & Production |
| 05 | [05-CICD-GitOps.md](05-CICD-GitOps.md) | CI/CD Pipeline & GitOps (ArgoCD) |
| 06 | [06-DevSecOps.md](06-DevSecOps.md) | DevSecOps & Security Tools |
| 07 | [07-AWS.md](07-AWS.md) | AWS Architecture & Services |
| 08 | [08-Terraform.md](08-Terraform.md) | Terraform & Infrastructure as Code |
| 09 | [09-Monitoring-Observability.md](09-Monitoring-Observability.md) | Monitoring, Logs & Alerting |
| 10 | [10-Scripting-Automation.md](10-Scripting-Automation.md) | Bash, Python, boto3 & Automation |
| 11 | [11-Production-Scenarios.md](11-Production-Scenarios.md) | Production Troubleshooting & RCA |
| 12 | [12-System-Design.md](12-System-Design.md) | Cloud-Native AI Platform Design Discussion |
| 13 | [13-HR-Behavioral.md](13-HR-Behavioral.md) | Behavioral Interview Questions |
| Core | [Core_DevOps_Fundamentals.md](Core_DevOps_Fundamentals.md) | DevOps Fundamentals (rename note) |
| Labs | [practice-labs/](practice-labs/) | Coding Drills & Interview Workbook |

---

## practice-labs/ Walkthrough

| Workbook | Description |
| --- | --- |
| [kubernetes-tasks-and-drills.md](practice-labs/kubernetes-tasks-and-drills.md) | Kubernetes manifest completion, troubleshooting drills, answers & hints |
| [terraform-tasks-and-drills.md](practice-labs/terraform-tasks-and-drills.md) | Terraform coding rounds, debugging, production best practices |

---

## 15-Minute Pre-Interview Rapid Revision

### Linux & Troubleshooting
- `chmod 755`, `chown user:group` — File permissions
- `ps -ef`, `top`, `kill PID` (15=graceful, 9=force) — Process management
- `systemctl start/stop/status/restart` — Service management
- `df -h`, `du -sh` — Disk usage
- `ss -tulpn`, `ip addr`, `ping` — Networking
- `free -h`, `ps aux --sort=-%mem` — Memory
- `top`, `uptime`, `ps aux --sort=-%cpu` — CPU
- `tail -f`, `grep ERROR`, `journalctl` — Logs
- `find /var/log -type f -mtime +30 -delete` — Cleanup

### Docker & Containers
- `docker run -d -p 8080:80 --name app nginx` — Run a container
- `docker logs -f <container>` — View logs
- `docker exec -it <container> bash` — Enter container
- `docker build -t app:1.0 .` — Build image
- `docker system prune -a` — Cleanup

### Kubernetes
- `kubectl get pods -o wide` — List pods
- `kubectl describe pod <name>` — Detailed state
- `kubectl logs <pod> --previous` — Previous container logs
- `kubectl rollout undo deployment <name>` — Rollback
- `kubectl autoscale deployment <name> --min=3 --max=10 --cpu-percent=70` — HPA
- `kubectl debug <pod> -it --image=nicolaka/netshoot` — Distroless debugging

### Observability
- `kubectl top nodes / top pods` — Resource usage
- `kubectl get events --sort-by=.lastTimestamp` — Cluster events
- `kubectl rollout status deploy <name>` — Rollout check
- Prometheus scrapes metrics, Grafana visualizes, Alertmanager pages

### AWS
- `Route53` — DNS, `CloudWatch` — Monitoring
- `ALB` (L7) vs `NLB` (L4) — Load balancing
- `ASG` — Auto Scaling Group (Multi-AZ)
- `EBS` (block, single EC2) vs `EFS` (file, multi-EC2) vs `S3` (object, unlimited)
- `IAM Role` — Temporary for services, `IAM User` — Permanent for people

### Terraform
- `terraform init → validate → plan → apply`
- `terraform import <resource> <id>` — Bring existing infra under management
- `remote state (S3) + dynamodb (locking)`
- `terraform fmt → validate → plan → approval → apply` (CI/CD)

### Git & CI/CD
- `git add . → git commit -m → git push origin main`
- 16-Stage Pipeline: Checkout → Build → Unit Test → Quality → Security → Package → Docker Build → Scan → Push → Deploy Dev → Integration Test → Deploy QA → Performance Test → Manual Approval → Deploy Production → Smoke Test → Monitor
- `kubectl rollout undo deployment <name>` — Rollback

---
