# Kubernetes Interview Handbook

> **Target Audience:** DevOps Engineers (2–8 Years Experience)
> **Interview Weightage:** ⭐⭐⭐⭐⭐

---

## Table of Contents

- [1. What is Kubernetes?](#1-what-is-kubernetes)
- [2. Kubernetes Architecture](#2-kubernetes-architecture)
- [3. Core Workloads](#3-core-workloads)
- [4. Services & Networking](#4-services--networking)
- [5. Configuration & Storage](#5-configuration--storage)
- [6. Scaling & Updates](#6-scaling--updates)
- [7. Scheduling & Placement](#7-scheduling--placement)
- [8. Security & Access Control](#8-security--access-control)
- [9. Production Scenarios](#9-production-scenarios)
- [10. kubectl Commands](#10-kubectl-commands)
- [11. Interview Questions & Answers](#11-interview-questions--answers)

---

## 1. What is Kubernetes?

Kubernetes (K8s) is an open-source container orchestration platform for deploying, scaling, and managing containerized applications.

### Why Kubernetes?

| Problem | Solution |
| ------- | -------- |
| Manual container management | Automated orchestration |
| No self-healing | Automatic pod restarts |
| Manual scaling | Auto-scaling (HPA) |
| No load balancing | Built-in Service discovery |
| Difficult rolling updates | Rolling updates with zero downtime |
| No service discovery | DNS-based service resolution |

### Key Capabilities

- Container orchestration
- Auto-scaling (horizontal pod autoscaler)
- Self-healing (restarts failed pods)
- Load balancing and service discovery
- Rolling updates and rollbacks
- Secret and configuration management

---

## 2. Kubernetes Architecture

### Cluster Components

```text
Control Plane
├── API Server (kube-apiserver) — Entry point for all requests
├── Scheduler (kube-scheduler) — Assigns pods to nodes
├── Controller Manager — Maintains desired state
└── etcd — Distributed key-value store (cluster state)

Worker Nodes
├── Kubelet — Node agent, communicates with API server
├── Kube Proxy — Network routing and service connectivity
└── Container Runtime (containerd, CRI-O)
```

### Component Responsibilities

| Component | Purpose |
| --------- | ------- |
| API Server | Front door of cluster, validates requests |
| etcd | Stores all cluster data (source of truth) |
| Scheduler | Decides which node runs a new pod |
| Controller Manager | Watches cluster state, reconciles differences |
| Kubelet | Ensures pods are running on node |
| Kube Proxy | Manages network rules for services |

### Request Flow

```text
kubectl get pods → API Server → etcd → Response
kubectl run nginx → API Server → Scheduler → Kubelet → Container Runtime → Pod running
```

---

## 3. Core Workloads

### Pod

Smallest deployable unit. Contains one or more containers sharing network and storage.

```bash
kubectl get pods
kubectl describe pod my-pod
kubectl logs my-pod
kubectl exec -it my-pod -- bash
```

### ReplicaSet

Ensures specified number of pod replicas are running. If a pod crashes, ReplicaSet creates a replacement.

### Deployment

Manages ReplicaSets and provides rolling updates, rollbacks, and scaling. **Never create pods directly in production — always use Deployments.**

```bash
kubectl get deployments
kubectl scale deployment app --replicas=5
kubectl rollout status deployment app
kubectl rollout undo deployment app
```

### StatefulSet

For stateful applications requiring stable identities and persistent storage.

| Stateless (Deployment) | Stateful (StatefulSet) |
| ---------------------- | ---------------------- |
| Random pod names | Stable pod names (mongo-0, mongo-1) |
| Any pod is interchangeable | Each pod has unique identity |
| Shared volumes | Dedicated persistent volumes |
| Examples: API servers | Examples: MongoDB, Kafka, PostgreSQL |

### DaemonSet

Ensures one pod runs on every node. Used for logging, monitoring, and security agents.

---

## 4. Services & Networking

### Service Types

| Type | Description | Use Case |
| ---- | ----------- | -------- |
| ClusterIP | Internal cluster IP (default) | Backend-to-backend communication |
| NodePort | Exposes on node IP:port | Testing |
| LoadBalancer | Creates cloud load balancer | Production, external access |

### Ingress

HTTP/HTTPS routing to services. Single entry point for multiple applications.

```text
Internet → Ingress Controller → app1.company.com → Service A
                               → app2.company.com → Service B
```

### Ingress Controllers

- NGINX Ingress Controller
- AWS Load Balancer Controller
- Traefik

### Network Policies

By default, all pods can communicate. Network policies enforce security by explicitly allowing/denying traffic.

---

## 5. Configuration & Storage

### ConfigMap

Non-sensitive configuration data.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  DATABASE_URL: postgres://db:5432
  APP_NAME: payment-service
```

### Secret

Sensitive data (base64 encoded).

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: app-secret
data:
  DB_PASSWORD: cGFzc3dvcmQ=
```

| ConfigMap | Secret |
| --------- | ------ |
| Plain text | Base64 encoded |
| Non-sensitive | Sensitive data |
| URLs, feature flags | Passwords, API keys, certs |

### Volumes

| Type | Description |
| ---- | ----------- |
| emptyDir | Temporary, deleted with pod |
| hostPath | Node filesystem (not recommended) |
| PersistentVolumeClaim (PVC) | Durable storage, survives pod restarts |

---

## 6. Scaling & Updates

### Horizontal Pod Autoscaler (HPA)

Automatically adjusts pod count based on CPU, memory, or custom metrics.

```text
Traffic Spike → CPU > 80% → HPA → More Pods Created → Traffic Normal → HPA → Scale Down
```

### Rolling Updates

Replaces pods gradually without downtime.

```bash
kubectl set image deployment/app app=app:v2
kubectl rollout status deployment/app
```

### Rollback

```bash
kubectl rollout undo deployment/app
kubectl rollout history deployment/app
```

### Pod Disruption Budget (PDB)

Ensures minimum number of pods remain available during maintenance.

---

## 7. Scheduling & Placement

### Taints and Tolerations

| Concept | Description |
| ------- | ----------- |
| Taint | Repels pods from a node |
| Toleration | Allows pod to be scheduled on tainted node |

### Node Affinity

Controls which nodes a pod can be scheduled on.

| Type | Description |
| ---- | ----------- |
| required | Pod MUST be schduled on matching node |
| preferred | od SHOULD be scheduled on matching node |

---

## 8. Securry & Access Control

### RBAC (Role-Basd Acces Cntrol)

| Resource | Scope | Descripion |
| ----- | ---- | ---- |
| Role | Namespac| Permissions within one namespace |
| ClusteRole | Cluter-wide | Permissions cross all namespac|
| RoleBinding | Namespace | Binds Role to user/service account |
| ClusterRoleBinding | Clustr-wde | Binds ClusterRole to user |

### Network Policies

By default, all pods communicate reely. Netwrk Poliies estrict traffic.

### Security Best Practic

- Enable RAC
- Use Namespaces fr islation
- Apply Netwk Poicies
- Use Secrts (n ot nfiMaps) for rendentials
- Avoi unning as root
- Set sourec limits
- Scan mages for vulnerabilities
- Enab audt logging

---

## 9. Podctin Scenari

### Pod Stuck in Pending

**Cauess:**
- Insufficint CPU/memory
- Node selector/matchin taints
- PVC not binding
- Image pull isue

**Deug:** `kubectl describe pod my-pod`, `kubectl get events`

### Poo in CrashLoopbackOff

**Causes:**
- Aplication error
- Missing environmen varials
- Incorc configurion
- Resource limis
- Dependency ailue

**Deug:** `kubctl logs my-pod`, `kubectl describe pod my-pod`

### Pod Running but Application Not Accessible

**Chec:**
- Service seletor matches pod labels
- Endpoints exist: `kubecl get endopints`
- Ingress rules configured
- Network olcies not blocking
- Pobes (livess/readines) not faiing

### Node Not Ready

**Chck:**
- Kubelet is uning
- Disk spce available
- Netwok connectiviy
- Node conditions in `kubectl describe node`

---

## 10. kubectl Comands

| Comand | Desction |
| -------- | ---- |
| `kubectl get pods` | Lst pods |
| `kubctl get nodes` | List nods |
| `kubectl get svc` | Lst services |
| `kubect get deployents` | Lst depoys |
| `kubectl get events` | Lst cluster events |
| `kubectl descibe pod p` | Detaied pod nfo |
| `kubectl logs p` | View pod log |
| `kubectl exec -it p -- bash` | Enter pod |
| `kubectl get pods -A` | Pods in all namespac |
| `kubect apply - f le.xaml` | Crte/update sources|
| `kubectl delete -f file.yaml` | Delere resource |
| `kubetl rollout status deployent/ap `| Check rollout status |
| `kubectl scall deployment app --replicas=5` | Scale|
| `kubctl rollout undo deployment app` | Rollback |
| `kubectl top nodes`| Node resource usage|
| `kubectl top pods` | Pod reource usage |

---

## 11. nterview Qestions & Anewers

### Q1. Exlain Kbernetes Architectre.

**Aswe**
Klernetes follws a Contl Plae + Woker Node rchitecture. The Contrl Plae nsists f PI Srver (ntry poin), Scheduler (assigns pods), Controllr Manage (maintains desie state), and etc (cluste state store). Worer Ndes run kubelet (node agent), kube-roy (networking). ad container runtime (containerd/R-O).

### Q2. What is he differece beween Deloyment and ReplicaSet?

**Awer**
ReplicaSet nly mainsins pod count. Depoyment manage ReplicSets and dds rollin upates, rolbaks, verion histor, nd scaling. In prodution, we use Deloyments, never ReplcaSs directy.

### Q3. Why do we need evices?

**Anser**
Pods are ephemerl — their IP changes when recreaty. Serves provide a stable network endpoint and load balance traffic across healty pods

### Q4. Ference betwen CluterIP, NoPort, and LoadBalancer?

**Aswe**
- **ClterIP:** Internal only, default, for backnd communication- **NoePort:** Exposes on ode p:prt, good for tesing- **LadBalancer:** Creates cloud load balancer, use for prodution external access

### Q5. Wh is Iaress?

**Anser**
Ingres provides HTT/HTTPS routing to services. Instead of multiple LoadBalancers, a single Ingress Controller routes traffic based on hostnames or URL paths. Reduces os.

### Q6. ConfigMap vrs Secet?

**Answe**
ConfgMaps tore non-sensitive config (URLs, feature lags). Secrets tore enseitive dta (passwors, API key). Secres ae base4 encoded and can b encryptd at rest.

### Q7. Liveess vs Readness Pobe?

**Anser**
Livenes probe determine if he container is runing — if it fails, Kuberetes restarts the coniner. Readnes probe decide if the ctainer is eady to receiv traffic — if i fails, the pd is remove from sevie endpoint (ot estarte).

### Q8. Wh is HPA?

**Anser**
Horzontal Pod Aoscaler automaticaly adjust pe replic count based on CPR, memry, or cusom metric. It scales up whendemand icrease a das down whn it decreases.

### Q9. Ho do you roublehoo CrasLoopbackOff?

**Anse**
1. Hubectl logs pod-name` to se apication erors
2. `kubectl descibe pod pod-name`o check events nd config
3. Check envionment varibles ccessng
4. Verify resource its match aplication eeds

### Q0. W is ebacc ine K8s?

**Answe**
Role-Based Acce Ctrl) govern permissions. Roles deine what actions an be performed. Roleinding attach roles to users or srvic accounts. Principle of east privilege — grant only require mission.

---

## Quick Revision

| Topi | Key Cnce t |
| --- | ---------- |
| Pod | Smallest deployable nit |
| Deploment | Manags pods, ulng update, rolbak|
| Serice | Stable network point|
| Ingrss | HTTP/HTPS puan|
| HPA | Autoscaling |
| ConfigMap | Nn-startusig |
| Secret | Sensiteata |
| RBAC | cess control |
| Taint/Toleration | Pod sceing restriion |
| etc | Clusr state store|