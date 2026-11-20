# Kubernetes Interview Handbook

> **Target Audience:** 5+ Years Experience
> **Role:** Senior DevOps / Platform Engineer
> **Interview Weightage:** ⭐⭐⭐⭐⭐

---

## Table of Contents

- [1. What is Kubernetes?](#1-what-is-kubernetes)
- [2. Kubernetes Architecture & Mental Models](#2-kubernetes-architecture--mental-models)
- [3. Core Workloads & Controllers](#3-core-workloads--controllers)
- [4. Services & Networking](#4-services--networking)
- [5. Configuration & Storage](#5-configuration--storage)
- [6. Scaling, Updates & Zero-Downtime](#6-scaling-updates--zero-downtime)
- [7. Scheduling & Placement](#7-scheduling--placement)
- [8. Production Security & Senior Deep-Dives](#8-production-security--senior-deep-dives)
- [9. Helm Charts, Operators & CNI Plugins](#9-helm-charts--operators--cni-plugins)
- [10. Production Troubleshooting Scenarios](#10-production-troubleshooting-scenarios)
- [11. kubectl Diagnostic Commands](#11-kubectl-diagnostic-commands)
- [12. Curated Interview Q&A](#12-curated-interview-qa)
- [13. Oracle DevOps Experience Positioning](#13-oracle-devops-experience-positioning)
- [14. 5-Minute Quick Revision Table](#14-5-minute-quick-revision-table)

---

## 1. What is Kubernetes?

Kubernetes (K8s) is an open-source container orchestration platform for deploying, scaling, and managing containerized applications at production scale.

### Why Kubernetes?

| Problem | Kubernetes Solution |
| ------- | ------------------- |
| Manual container management | Automated orchestration via Declarative API |
| No self-healing | Automatic pod restarts via ReplicaSet/Deployment |
| Manual scaling | Auto-scaling via Horizontal Pod Autoscaler (HPA) |
| No load balancing | Built-in Service discovery and kube-proxy |
| Difficult rolling updates | Rolling updates with zero downtime (maxSurge/maxUnavailable) |
| No service discovery | DNS-based service resolution (ClusterDNS) |

### Senior Mental Model: Cattle vs Pets

```text
Pets (VMs)                          Cattle (Containers/Pods)
├── Named individually              ├── Ephemeral, replaceable
├── Hand-fed, manually patched      ├── Managed by controllers
├── Long uptime expected            ├── Auto-healed on failure
├── SSH into for debugging          ├── kubectl debug / logs only
└── Snowflake configurations        └── Immutable infrastructure
```

**Senior mindset:** Treat Kubernetes workloads as cattle — never SSH into a production pod to fix it manually. Fix the manifest, redeploy, and let the controller reconcile.

---

## 2. Kubernetes Architecture & Mental Models

### Cluster Architecture Diagram

```text
                    +---------------------------------------------------+
                    |              CONTROL PLANE                        |
                    |                                                   |
                    |  kube-apiserver  —  Front door, validates REST    |
                    |  kube-scheduler  —  Assigns pods to nodes         |
                    |  kube-cm         —  Maintains desired state       |
                    |  etcd            —  Distributed KV store (source) |
                    +---------------------------------------------------+
                                  |
                                  | (kubectl / API calls)
                                  v
        +-------------------------+-------------------------+
        |                                                     |
  Worker Node 1                                         Worker Node 2
  +------------------+                                +------------------+
  | kubelet          |                                | kubelet          |
  | kube-proxy       |                                | kube-proxy       |
  | containerd/CRI-O |                                | containerd/CRI-O |
  | Pods (containers)|                                | Pods (containers)|
  +------------------+                                +------------------+

etcd is the single source of truth. If etcd is lost and not backed up,
the cluster state is unrecoverable — always back up etcd.
```

### Component Responsibilities

| Component | Role | Senior Responsibility |
| --------- | ---- | --------------------- |
| kube-apiserver | Front door; authenticates, authorizes, validates | Enable audit logging, RBAC, API priority/fairness |
| etcd | Distributed key-value store (cluster state) | Regular etcd snapshots, encryption at rest, quorum (3/5 nodes) |
| kube-scheduler | Assigns pods to nodes based on resources | Taints/tolerations, node affinity, pod topology spread |
| kube-controller-manager | Reconciles desired vs actual state | Monitor controller health, custom controllers |
| kubelet | Node agent; ensures pods run | Node problem detector, kubelet config, node draining |
| kube-proxy | Network rules / service LB | IPVS mode vs iptables, conntrack tuning |
| Container Runtime | Runs containers (containerd, CRI-O) | CRI compliance, image pull security, runtime class |

### Request Flow (Mental Model)

```text
kubectl get pods
    │
    ▼
kube-apiserver (authN/authZ → admission control → etcd read)
    │
    ▼
etcd (consistent read of stored state)
    │
    └──► Response to client

kubectl run nginx (write path)
    │
    ▼
kube-apiserver → validate → write to etcd
    │
    ▼
kube-scheduler → selects node → writes binding to etcd
    │
    ▼
kubelet (on selected node) → pulls image → starts container via CRI
    │
    ▼
kube-proxy → programs iptables/IPVS rules for service
    │
    ▼
Pod running and receiving traffic
```

---

## 3. Core Workloads & Controllers

### Pod

Smallest deployable unit. One or more containers sharing network namespace, storage, and IP.

```bash
kubectl get pods
kubectl describe pod <pod-name>
kubectl logs <pod-name>
kubectl exec -it <pod-name> -- bash
```

**Senior note:** Never create bare Pods in production. Use Deployments, StatefulSets, or DaemonSets so the cluster self-heals.

### ReplicaSet

Maintains a stable set of replica Pods. Rarely used directly — Deployments manage ReplicaSets.

### Deployment

Manages ReplicaSets and provides rolling updates, rollbacks, scaling, and revision history.

```bash
kubectl get deployments
kubectl scale deployment <app> --replicas=5
kubectl rollout status deployment <app>
kubectl rollout undo deployment <app>
kubectl rollout history deployment <app>
```

**Rollout strategy key fields:**
```yaml
strategy:
  type: RollingUpdate
  rollingUpdate:
    maxUnavailable: 1      # Max pods unavailable during rollout
    maxSurge: 1            # Max extra pods created above desired
```

### StatefulSet

For stateful applications requiring stable network identities and persistent storage (databases, Kafka, etc.).

### DaemonSet

Runs one Pod on every node (use cases: node-exporter, Fluentd, CNI plugins).

### Job / CronJob

One-off or scheduled batch workloads.

---

## 4. Services & Networking

### Service Types

| Type | Access Scope | Use Case |
| ---- | ------------ | -------- |
| ClusterIP | Internal cluster only | Default; backend-to-backend communication |
| NodePort | Node IP + static port | Testing, legacy external access |
| LoadBalancer | Cloud LB provisioned | Production external internet-facing traffic |
| ExternalName | Maps to external CNAME | Legacy external service integration |

### Service Selection

```yaml
apiVersion: v1
kind: Service
metadata:
  name: backend
spec:
  selector:
    app: backend       # Must match pod labels
  ports:
    - port: 80         # Service port
      targetPort: 8080 # Container port
```

### Ingress (HTTP/HTTPS Layer 7)

Single entry point for external HTTP/HTTPS traffic. Routes based on host/path, handles SSL termination.

```text
Internet → Ingress Controller → Service → Pod
```

**Senior depth:** Ingress Controller (NGINX, Traefik, ALB) is separate from Ingress resource. Configure rate limiting, WAF rules, and custom headers at the ingress layer.

### NetworkPolicy (CoreDNS Rules Example)

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: coredns-access
  namespace: kube-system
spec:
  podSelector:
    matchLabels:
      k8s-app: kube-dns
  policyTypes:
  - Ingress
  ingress:
  - from:
    - namespaceSelector: {}
    ports:
    - protocol: UDP
      port: 53
    - protocol: TCP
      port: 53
```

**Senior note:** Default deny-all NetworkPolicy then explicitly allow required traffic — zero-trust networking within the cluster.

---

## 5. Configuration & Storage

### ConfigMap vs Secret

| ConfigMap | Secret |
| --------- | ------ |
| Non-sensitive configuration (URLs, feature flags) | Sensitive data (passwords, API keys, certificates) |
| Plain text | Base64-encoded (not encryption by itself) |
| Mounted as volumes or env vars | Mounted as volumes or env vars |
| Use for: app config, connection strings | Use for: DB passwords, TLS certs, OAuth tokens |

### Storage

| Resource | Purpose |
| -------- | ------- |
| PersistentVolume (PV) | Cluster-level storage resource |
| PersistentVolumeClaim (PVC) | Pod's request for storage |
| StorageClass | Dynamic provisioning (gp3, standard, SSD) |
| Volume | Pod-level ephemeral storage |

**Senior depth:** For databases, use StatefulSet + PVC + StorageClass with `retain` reclaim policy. Never use emptyDir for production data.

---

## 6. Scaling, Updates & Zero-Downtime

### Horizontal Pod Autoscaler (HPA)

```bash
kubectl autoscale deployment <app> --min=3 --max=10 --cpu-percent=70
```

Metrics: CPU, memory, custom metrics (Prometheus adapter).

### Rolling Update (Zero Downtime)

```yaml
strategy:
  type: RollingUpdate
  rollingUpdate:
    maxUnavailable: 0   # Zero pods unavailable — true zero downtime
    maxSurge: 1
```

### Vertical Pod Autoscaler (VPA)

Adjusts CPU/memory requests automatically. Use with caution — can cause pod restarts.

### Disruption Budgets (PDB)

```yaml
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: app-pdb
spec:
  minAvailable: 2
  selector:
    matchLabels:
      app: my-app
```

Ensures minimum replicas during voluntary disruptions (node drains, cluster upgrades).

---

## 7. Scheduling & Placement

| Feature | Purpose | Senior Use Case |
| ------- | ------- | --------------- |
| Taints & Tolerations | Repel pods from nodes | Dedicated node pools (ingress, monitoring) |
| Node Affinity | Attract pods to nodes | GPU nodes, zone-aware scheduling |
| Pod Anti-Affinity | Spread pods across nodes | HA — avoid same-zone co-location |
| Resource Requests/Limits | QoS class assignment | Guaranteed/Burstable/BestEffort |
| PriorityClass | Preemption | Critical system pods (CoreDNS, metrics-server) |

### QoS Classes

| Class | Requirements | OOM Behavior |
| ----- | ------------ | ------------ |
| Guaranteed | requests == limits for all containers | Last to be killed |
| Burstable | requests < limits | Killed after BestEffort |
| BestEffort | No requests/limits | First to be killed |

**Senior depth:** Production workloads should run as Guaranteed QoS (set requests == limits) to avoid unpredictable OOM kills and node pressure evictions.

---

## 8. Production Security & Senior Deep-Dives

### RBAC (Role-Based Access Control)

### AWS EKS IRSA (IAM Roles for Service Accounts)
- **How IRSA works:** EKS exposes an OIDC issuer URL. An IAM Role trust policy allows `sts:AssumeRoleWithWebIdentity` from a specific Kubernetes `ServiceAccount` (`system:serviceaccount:<ns>:<sa>`).
- When a Pod starts with that `ServiceAccount`, EKS injects a projected OIDC JWT token and `AWS_ROLE_ARN`, allowing the AWS SDK to exchange the JWT with AWS STS for temporary, auto-rotating IAM credentials without static AWS keys.
```text
Role (namespace-scoped)     →  RoleBinding (user/SA → Role)
ClusterRole (cluster-wide)  →  ClusterRoleBinding (user/SA → ClusterRole)
```

Principle of least privilege: service accounts should only have permissions they need. Avoid mounting the default service account token unless required:
```yaml
automountServiceAccountToken: false
```

### Image Security & Distroless Debugging

- Use minimal/distroless base images (no shell, no package manager)
- Run as non-root user (`runAsNonRoot: true`, `runAsUser: 1000`)
- Read-only root filesystem (`readOnlyRootFilesystem: true`)
- Drop all Linux capabilities, add only required ones
- Scan images with Trivy/Snyk/Docker Scout

### kubectl debug (Ephemeral Containers)

For Distroless images without a shell, use `kubectl debug` to attach a debug container:

```bash
kubectl debug <pod-name> -it --image=nicolaka/netshoot --target=<container-name>
kubectl debug node/<node-name> -it --image=ubuntu
```

**Exit codes to know:** 137 (OOM kill, SIGKILL), 143 (SIGTERM graceful shutdown), 127 (command not found), 1 (generic application error).

### BuildKit Secrets (Docker)

```dockerfile
# syntax=docker/dockerfile:1
RUN --mount=type=secret,id=mysecret cat /run/secrets/mysecret
```
Build-time secrets never land in the final image layer.

---

## 9. Helm Charts, Operators & CNI Plugins

### Helm (Kubernetes Package Manager)

| Concept | Description |
| ------- | ----------- |
| Chart | Collection of YAML templates for an application |
| Release | Installed instance of a chart |
| Values | Configuration overrides (`values.yaml`) |
| Hooks | Pre/post-install jobs (backup, migration) |

**Helm vs Raw YAML:** Helm enables templating, versioning, rollback (`helm rollback`), and reuse across environments. Use Helm for production applications; raw manifests for simple one-off resources.

```bash
helm repo add bitnami https://charts.bitnami.com
helm install nginx bitnami/nginx
helm upgrade --install my-app ./my-chart
helm rollback my-app 1
```

### Kubernetes Operators

An Operator is a custom controller that extends Kubernetes with Custom Resources (CRDs) to manage complex applications (databases, Kafka, Elasticsearch).

```text
Operator Pattern:
Custom Resource (CR) → Operator watches CR → Reconciles → Creates/Updates native K8s resources
```

**Examples:** etcd-operator, Prometheus-Operator, AWS RDS Operator, Kafka Operator (Strimzi).

**Senior depth:** Use Operators when you need day-2 operations (backup, failover, scaling, upgrades) automated. Prefer Helm for simple stateless deployments.

### CNI Plugins (Container Network Interface)

CNI plugins provide pod networking. Common options:

| Plugin | Best For | Key Feature |
| ------ | -------- | ----------- |
| Calico | Enterprise, network policy | Rich NetworkPolicy support, BGP routing |
| Flannel | Simple clusters | Overlay networks, easy setup |
| Cilium | Security, observability | eBPF-based, L4/L7 policy, hubble |
| Amazon VPC CNI | EKS | Native VPC networking, ENI per pod |
| Azure CNI | AKS | Native VNET integration |

**Senior depth:** CNI choice affects NetworkPolicy support, performance, and IP address management. For production with strict security, choose Calico or Cilium.

---

## 10. Production Troubleshooting Scenarios

### Symptom → Root Cause → Diagnostic Commands

| Symptom | Likely Root Cause | Diagnostic Commands |
| ------- | ----------------- | ------------------- |
| CrashLoopBackOff | App crash, bad config, missing secret | `kubectl logs <pod>`, `kubectl describe pod <pod>`, `kubectl exec` env check |
| ImagePullBackOff | Wrong image/tag, registry auth, network | `kubectl describe pod`, `kubectl get events`, check imagePullSecrets |
| Pod Pending | Insufficient resources, taints, node selector | `kubectl describe pod` (events), `kubectl get nodes`, `kubectl top nodes` |
| Node NotReady | Kubelet down, disk pressure, network | `kubectl get nodes`, `kubectl describe node`, `ssh node systemctl status kubelet` |
| High CPU / OOMKill (exit 137) | Missing limits, memory leak, spike | `kubectl top pods`, `kubectl describe pod`, metrics-server |
| Service timeout | Wrong selector, pod not ready, NetworkPolicy | `kubectl get endpoints`, `kubectl get svc`, `kubectl get networkpolicy` |
| DNS resolution failure | CoreDNS down, NetworkPolicy blocking port 53 | `kubectl get pods -n kube-system`, `kubectl logs -n kube-system coredns`, `kubectl exec` nslookup |
| Evicted pod | Node disk/memory pressure | `kubectl describe pod`, `kubectl get events`, `df -h` on node |

### Specific Troubleshooting Playbooks

#### CrashLoopBackOff
```
Symptom: Pod restarts continuously
Root Cause: Application exits immediately (bad config, DB unreachable, missing secret)
Commands:
  kubectl logs <pod-name> --previous
  kubectl describe pod <pod-name>
  kubectl exec -it <pod-name> -- env | grep DB
```

#### ImagePullBackOff
```
Symptom: Pod cannot pull image
Root Cause: Wrong image tag, private registry without secret, network blocked
Commands:
  kubectl describe pod <pod-name>
  kubectl get events --sort-by=.lastTimestamp
  kubectl get secret <image-pull-secret>
```

#### DNS Failure (CoreDNS)
```
Symptom: Services cannot resolve each other
Root Cause: CoreDNS CrashLoopBackOff, NetworkPolicy blocking UDP/TCP 53
Commands:
  kubectl get pods -n kube-system | grep coredns
  kubectl logs -n kube-system deployment/coredns
  kubectl exec <test-pod> -- nslookup kubernetes.default.svc.cluster.local
  kubectl get networkpolicy -n kube-system
```

#### Exit Code 137 (OOMKill)
```
Symptom: Pod terminated with OOMKilled
Root Cause: Memory limit exceeded, missing requests/limits
Commands:
  kubectl get pod <pod> -o jsonpath='{.status.containerStatuses[*].lastState.terminated}'
  kubectl top pods
  kubectl describe pod <pod> | grep -A5 Limits
```

---

## 11. kubectl Diagnostic Commands

| Command | Purpose |
| ------- | ------- |
| `kubectl get pods -o wide` | Pods + node assignment + IP |
| `kubectl get events --sort-by=.lastTimestamp` | Recent cluster events |
| `kubectl describe pod <name>` | Detailed state, events, conditions |
| `kubectl logs <pod> --previous` | Previous container logs (crash) |
| `kubectl exec -it <pod> -- bash` | Debug shell (if image has one) |
| `kubectl debug <pod> -it --image=netshoot` | Ephemeral debug container (Distroless) |
| `kubectl get endpoints <svc>` | Backend pods behind service |
| `kubectl top pods / top nodes` | Resource usage (requires metrics-server) |
| `kubectl rollout status deploy <name>` | Deployment rollout progress |
| `kubectl rollout history deploy <name>` | Revision history |
| `kubectl rollout undo deploy <name>` | Rollback to previous revision |
| `kubectl api-resources` | List all API resources |
| `kubectl auth can-i <verb> <resource>` | RBAC authorization check |
| `kubectl get netpol` | Network policies |
| `kubectl describe node <name>` | Node conditions, capacity, allocatable |

---

## 12. Curated Interview Q&A

### Q1. Explain Kubernetes Architecture.

**Answer:** Kubernetes follows a Control Plane + Worker Node model. The Control Plane runs kube-apiserver (entry point), kube-scheduler (pod placement), kube-controller-manager (state reconciliation), and etcd (distributed state store). Worker nodes run kubelet (node agent), kube-proxy (networking), and a container runtime. All cluster state lives in etcd — back it up regularly.

### Q2. Deployment vs ReplicaSet?

**Answer:** ReplicaSet only maintains a replica count. Deployment manages ReplicaSets and adds rolling updates, rollbacks, revision history, and scaling. In production, always use Deployments — never bare ReplicaSets or Pods.

### Q3. Why do we need Services?

**Answer:** Pods are ephemeral — IPs change on recreation. Services provide a stable virtual IP (ClusterIP) and DNS name, load-balancing traffic across healthy pods via label selectors.

### Q4. ClusterIP vs NodePort vs LoadBalancer?

**Answer:** ClusterIP = internal only (default). NodePort = exposes on a static port on every node (testing). LoadBalancer = provisions a cloud load balancer (production internet-facing).

### Q5. What is Ingress?

**Answer:** Ingress is an API object that manages external HTTP/HTTPS access to services. An Ingress Controller (NGINX, ALB, Traefik) implements the routing. Enables host/path-based routing, SSL termination, and reduces cost vs multiple LoadBalancers.

### Q6. ConfigMap vs Secret?

**Answer:** ConfigMaps store non-sensitive config (URLs, flags). Secrets store sensitive data (passwords, API keys, certs). Secrets are base64-encoded (obfuscation, not encryption) — enable encryption at rest for etcd.

### Q7. Liveness vs Readiness Probe?

**Answer:** Liveness probe detects a dead container → Kubelet restarts it. Readiness probe detects a pod not ready to serve traffic → removed from Service endpoints. Use both: readiness for traffic routing, liveness for self-healing.

### Q8. What is HPA?

**Answer:** Horizontal Pod Autoscaler automatically adjusts replica count based on CPU/memory/custom metrics. Configure min/max replicas and target utilization.

### Q9. How do you achieve zero-downtime deployments?

**Answer:** Use Deployments with RollingUpdate strategy (maxUnavailable: 0), multiple replicas, readiness probes, and verify rollout status before considering deployment complete. Pair with PodDisruptionBudgets.

### Q10. How do you troubleshoot CrashLoopBackOff?

**Answer:** Check `kubectl logs <pod>` and `--previous` for crash reason. `kubectl describe pod` for events (config errors, missing secrets, OOM). Verify env vars, image entrypoint, and resource limits. For Distroless images, use `kubectl debug`.

### Q11. What are NetworkPolicies?

**Answer:** NetworkPolicies are Layer 3/4 firewall rules for pods. Default allow-all; apply default-deny then explicitly allow required ingress/egress. Example: CoreDNS allows UDP/TCP port 53 from all namespaces.

### Q12. What is etcd and why does it matter?

**Answer:** etcd is the distributed key-value store — the single source of truth for all cluster state. Loss of etcd without backup means cluster unrecoverable. Run 3/5-node quorum, enable encryption at rest, schedule regular snapshots.

### Q13. How do you secure a Kubernetes cluster?

**Answer:** RBAC with least privilege, NetworkPolicies for micro-segmentation, image scanning (Trivy), read-only root filesystems, non-root users, secrets management (external secrets/OCI Vault), etcd encryption, audit logging, and PodSecurityPolicy/PSA.

### Q14. What is an Operator?

**Answer:** An Operator is a custom controller that watches Custom Resources (CRDs) and automates day-2 operations for complex applications (databases, Kafka, Elasticsearch) — backup, failover, scaling, upgrades.

### Q15. CNI plugin choice — what matters?

**Answer:** CNI provides pod networking. Choose based on NetworkPolicy needs (Calico/Cilium), performance (Cilium eBPF), cloud integration (VPC CNI for EKS), and operational simplicity. Affects IPAM, overlay vs routing, and policy enforcement.

---

## 13. Oracle DevOps Experience Positioning

**Interview Answer:**

At Oracle, we managed Kubernetes clusters running mission-critical microservices. Deployments were delivered through GitLab CI/CD pipelines that built, scanned (Fortify, Black Duck, OWASP ZAP), and pushed Docker images to a registry before Helm-based deployment to K8s. We used Deployments with rolling updates and readiness probes for zero-downtime releases, Services for stable internal networking, Ingress for external HTTP/HTTPS traffic, ConfigMaps and Secrets for configuration, and HPA for autoscaling based on CPU/memory. CoreDNS handled service discovery, and NetworkPolicies enforced zero-trust segmentation between namespaces. Monitoring was via Prometheus/Grafana with Alertmanager for on-call paging.

**STAR Example (ADB Ingestion Special-Character RCA):**

- **Situation:** Production Oracle Autonomous Database (ADB) onboarding began failing for specific customer records.
- **Task:** Identify root cause and restore onboarding without data loss.
- **Action:** Investigation showed records containing special characters were causing ingestion parsing failures. We reviewed application logs, identified the parsing issue, implemented input validation and sanitization, validated successful onboarding, and introduced schema validation to prevent recurrence.
- **Result:** Onboarding success rate restored to 100%; no recurrence after deploying input validation. This is a strong STAR-format RCA example directly aligned with production experience.

---

## 14. 5-Minute Quick Revision Table

| Topic | Key Point |
| ----- | --------- |
| Pod | Smallest deployable unit; never use bare pods in prod |
| Deployment | Manages ReplicaSets; rolling updates & rollbacks |
| Service | Stable ClusterIP + DNS for pods |
| Ingress | HTTP/HTTPS entry point; SSL termination |
| ConfigMap | Non-sensitive configuration |
| Secret | Sensitive data (base64-encoded) |
| RBAC | Role/ClusterRole + Binding; least privilege |
| NetworkPolicy | Pod firewall; default-deny then allow |
| HPA | Auto-scale replicas on CPU/memory |
| PDB | Min replicas during disruptions |
| QoS | Guaranteed = requests==limits (production) |
| CNI | Pod networking (Calico, Cilium, VPC CNI) |
| Helm | Package manager; charts, releases, rollbacks |
| Operator | Custom controller for CRDs; day-2 automation |
| etcd | Cluster state store; backup + encrypt |
| kubectl debug | Ephemeral container for Distroless debugging |
| Exit 137 | OOMKill; set requests==limits |
| Exit 143 | SIGTERM graceful shutdown |
